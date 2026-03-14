---
title: "DMAと割り込み"
addr: "$DFF096"
description: "DMAチャネル制御、バスサイクル割当て、PAULAが管理する14ソースの割り込みシステム。"
order: 5
---

<!-- section: $DFF096 "DMAの概要" -->

カスタムチップとChip RAM間のすべてのデータ転送は、Agnusが制御する**DMA（Direct Memory Access）**チャネルを通じて行われる。68000 CPUはメモリへのアクセスを競合する多くのバスマスタの一つに過ぎない。各カスタムチップサブシステム――ビットプレーン、スプライト、ディスク、オーディオ、Copper、Blitter――は、プロセッサとは独立してChip RAMの読み書きができる専用のDMAチャネルを持つ。

DMA転送は、各I/Oユニットの3種類のレジスタを通じて動作する：

- **制御レジスタ** — CPUがパラメータ（解像度、音量、ディスクトラックなど）を設定するために使用
- **データレジスタ** — ユニットとChip RAM間で転送される実際のデータを保持
- **アドレスレジスタ（ポインタ）** — Chip RAMのどこから読み書きするかをDMAコントローラに指示

中央DMA制御レジスタ**DMACON**が、各チャネルを個別に有効化・無効化する。いずれのチャネルも動作するにはマスタイネーブルビットもセットされている必要がある。

### 6つのDMAチャネル

**ビットプレーンDMA** — 画面表示データをChip RAMからビットプレーンデータレジスタに読み込み、Deniseのビデオシーケンサに供給して画面出力を行う。

**スプライトDMA** — スプライト画像データをChip RAMからスプライトデータレジスタに転送する。

**ディスクDMA** — フロッピーディスクデータをディスクコントローラとChip RAM間で双方向に転送する。

**オーディオDMA** — デジタル化されたサウンドサンプルデータをChip RAMからオーディオチャネルデータレジスタに読み込む。

**Copper DMA** — CopperコプロセッサのプログラムをChip RAMからフェッチする。

**Blitter DMA** — エリアフィル、ライン描画、ブロックコピー操作のためにBlitterとの間でデータを転送する。

<!-- section: $DFF096 "バスサイクルの割当て" -->

Amigaのバスタイミングはビデオラスタに同期している。1本の水平ラスタラインは約63.5マイクロ秒で、227.5バスサイクルから構成される。各バスサイクルは約280ナノ秒――この間に1回のメモリアクセスが可能である。

### 奇数サイクルと偶数サイクル

Amigaのバスアーキテクチャの要は、サイクルの**奇数**と**偶数**への分割である：

- **奇数サイクル**はDMA専用。68000は使用できない。
- **偶数サイクル**はCPU、Copper、Blitterで共有。DMAが常に優先される。

68000はそのクロック周波数により560nsに1回しかメモリアクセスできない――ちょうどバスサイクル2つ分である。そのため偶数サイクルのみを使用できる。つまり、奇数サイクルのDMAチャネル（スプライト、オーディオ、ディスク、リフレッシュ）はプロセッサを全く遅延させずに動作する。

### DMA優先度とサイクル割当て

各ラスタライン内でDMAスロットは固定順序で割り当てられる：

<div class="register-block">
<div class="reg-title">DMAサイクル割当て（ラスタラインごと）</div>
<div class="reg-row"><span class="reg-field">$00–$03</span><span class="reg-val">リフレッシュDMA — DRAMリフレッシュ用4サイクル（奇数サイクル）</span></div>
<div class="reg-row"><span class="reg-field">$04–$06</span><span class="reg-val">ディスクDMA — 3サイクル（奇数サイクル）</span></div>
<div class="reg-row"><span class="reg-field">$07–$0E</span><span class="reg-val">オーディオDMA — チャネル0〜3（奇数サイクル）</span></div>
<div class="reg-row"><span class="reg-field">$0F–$26</span><span class="reg-val">スプライトDMA — スプライト0〜7、各2サイクル（奇数サイクル）</span></div>
<div class="reg-row"><span class="reg-field">DDFSTRT–DDFSTOP</span><span class="reg-val">ビットプレーンDMA — 可変位置、奇数サイクル（lores最大4プレーン/hires最大2プレーン）と偶数サイクル（lores 5〜6プレーン/hires 3〜4プレーン）を使用</span></div>
<div class="reg-row"><span class="reg-field">偶数サイクル</span><span class="reg-val">Copper DMA — 空いている偶数サイクルを使用、BlitterとCPUより高優先度</span></div>
<div class="reg-row"><span class="reg-field">偶数サイクル</span><span class="reg-val">Blitter DMA — 空いている偶数サイクルを使用、CPUより高優先度</span></div>
<div class="reg-row"><span class="reg-field">偶数サイクル</span><span class="reg-val">68000 CPU — 残った偶数サイクルを使用</span></div>
</div>

低解像度で4プレーン以下の場合、すべてのビットプレーンデータは奇数サイクルに収まり、偶数サイクルはCPUに開放される。loresで5〜6プレーンの場合、8サイクルグループごとに2つの偶数サイクルがビットプレーンDMAに消費され、CPUの帯域幅が減少する。高解像度では2プレーンは奇数サイクルに収まるが、hiresで3〜4プレーンになると偶数サイクルを奪い取り、CPUはバス時間の半分以上を失う可能性がある。

**Fast RAM**や**Kickstart ROM**から実行されるプログラムはDMA競合の影響を受けない。それらのバスはChip RAMバスとは独立しているためである。

<!-- section: $DFF096 "画面構造とラスタ" -->

Amigaのビデオ出力は**PAL**標準に準拠する（北米モデルではNTSC）。すべてのDMAタイミングはラスタから導出されるため、ラスタの理解は不可欠である。

### PALタイミング

PALフレームは625ラインで構成され、毎秒50フィールドである。フリッカーを避けつつライン周波数を管理可能な範囲（15,625Hz）に保つため、画像は2つの**インターレース**フィールドに分割される：

- **ロングフレーム**（奇数ライン：1, 3, 5, ... 625）— 313ライン
- **ショートフレーム**（偶数ライン：2, 4, 6, ... 624）— 312ライン

Amigaのデフォルトのノンインターレースモードでは、両方のフィールドが同じ313ラインを50Hzで表示し、ライン間隔が見える安定した画像を生成する。インターレースモードを有効にすると垂直解像度が625ラインに倍増するが、高コントラストの水平エッジでフリッカーが発生する。

### ビーム位置レジスタ

Agnusは電子ビームの位置を追跡する内部カウンタを持つ。CPUはこのカウンタを読むことで、フレーム内の正確な位置を判定できる。

<div class="register-block">
<div class="reg-title">ビーム位置レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF006</span><span class="reg-val">VHPOSR — 垂直（V7–V0）・水平（H8–H1）位置（読み取り）</span></div>
<div class="reg-row"><span class="reg-field">$DFF004</span><span class="reg-val">VPOSR — LOFフラグ（ビット15）とV8（ビット0）（読み取り）</span></div>
<div class="reg-row"><span class="reg-field">$DFF02C</span><span class="reg-val">VHPOSW — ビーム位置（書き込み）</span></div>
<div class="reg-row"><span class="reg-field">$DFF02A</span><span class="reg-val">VPOSW — LOFとV8（書き込み）</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">レジスタ</span>
<span class="bit-dir">ビット</span>
<span class="bit-name">名前</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">VHPOS</span><span class="bit-dir">15–8</span><span class="bit-name">V7–V0</span><span class="bit-func">垂直位置（ライン番号、下位8ビット）</span></div>
<div class="bit-row"><span class="bit-num">VHPOS</span><span class="bit-dir">7–0</span><span class="bit-name">H8–H1</span><span class="bit-func">水平位置（バスサイクル番号、0–$E3）</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">VPOS</span><span class="bit-dir">15</span><span class="bit-name">LOF</span><span class="bit-func">ロングフレームフラグ（1 = ロングフレーム/奇数ライン）</span></div>
<div class="bit-row"><span class="bit-num">VPOS</span><span class="bit-dir">0</span><span class="bit-name">V8</span><span class="bit-func">垂直位置ビット8（ライン256〜312用）</span></div>
</div>

水平位置は$00から$E3（0〜227）の範囲で、$0F〜$35が水平帰線消去期間に対応する。垂直ライン0〜25が垂直帰線消去期間で、可視領域はライン26からライン312まで。

**LOF**ビットは現在どちらのフィールドタイプが表示されているかを示す――ロングフレーム（奇数ライン）でセット、ショートフレーム（偶数ライン）でクリア。ノンインターレースモードではLOFは常に1。

<!-- section: $DFF096 "DMACONレジスタ" -->

**DMACON**レジスタはすべてのDMAチャネルの中央スイッチである。SET/CLR機構を使用し、直接書き込みはできず、個々のビットのセットまたはクリアのみ可能。

<div class="register-block">
<div class="reg-title">DMACONレジスタアドレス</div>
<div class="reg-row"><span class="reg-field">$DFF096</span><span class="reg-val">DMACON — DMA制御（書き込み）</span></div>
<div class="reg-row"><span class="reg-field">$DFF002</span><span class="reg-val">DMACONR — DMA制御（読み取り）</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func">詳細</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">SET/CLR</span><span class="bit-name">セット/クリア制御</span><span class="bit-func">1 = リストされたビットをセット; 0 = クリア</span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">BBUSY</span><span class="bit-name">Blitterビジー</span><span class="bit-func">読み取り専用: Blitter動作中に1</span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">BZERO</span><span class="bit-name">Blitterゼロ</span><span class="bit-func">読み取り専用: Blitter出力がすべてゼロの場合1</span></div>
<div class="bit-row"><span class="bit-num">12–11</span><span class="bit-dir">—</span><span class="bit-name">未使用</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">BLTPRI</span><span class="bit-name">Blitter優先度</span><span class="bit-func">1 = BlitterがCPUに対して絶対優先</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">9</span><span class="bit-dir">DMAEN</span><span class="bit-name">マスタDMAイネーブル</span><span class="bit-func">いずれかのDMAチャネル（ビット0–8）が動作するにはセット必須</span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">BPLEN</span><span class="bit-name">ビットプレーンDMA</span><span class="bit-func">ビットプレーン表示DMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">COPEN</span><span class="bit-name">Copper DMA</span><span class="bit-func">CopperコプロセッサDMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">BLTEN</span><span class="bit-name">Blitter DMA</span><span class="bit-func">Blitter DMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">SPREN</span><span class="bit-name">スプライトDMA</span><span class="bit-func">スプライトDMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">DSKEN</span><span class="bit-name">ディスクDMA</span><span class="bit-func">ディスクDMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">AUD3EN</span><span class="bit-name">オーディオch. 3</span><span class="bit-func">オーディオチャネル3 DMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">AUD2EN</span><span class="bit-name">オーディオch. 2</span><span class="bit-func">オーディオチャネル2 DMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">AUD1EN</span><span class="bit-name">オーディオch. 1</span><span class="bit-func">オーディオチャネル1 DMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">AUD0EN</span><span class="bit-name">オーディオch. 0</span><span class="bit-func">オーディオチャネル0 DMAを有効化</span></div>
</div>

### SET/CLR機構

DMACONは通常のレジスタのように書き込むことはできない。ビット15が操作を決定する：

- **ビット15 = 1:** 書き込み値で1にセットされたすべてのビットがDMACONで**セット**される。他のビットは変更されない。
- **ビット15 = 0:** 書き込み値で1にセットされたすべてのビットがDMACONで**クリア**される。他のビットは変更されない。

DMAチャネルは個別イネーブルビット**と**マスタDMAENビット（ビット9）の**両方**がセットされている場合のみアクティブになる。

```asm
    ; ビットプレーンとCopper DMAを有効化:
    MOVE.W #$8380,$DFF096    ; SET/CLR=1, DMAEN=1, BPLEN=1, COPEN=1

    ; Blitter DMAのみ無効化:
    MOVE.W #$0040,$DFF096    ; SET/CLR=0, BLTEN=1 → BLTENをクリア
```

### Blitter優先度（BLTPRI）

BLTPRIがセットされると、Blitterは68000に対して絶対優先権を持ち、Blitter操作中CPUは偶数サイクルのバスアクセスを一切得られない。クリアの場合、CPUは偶数バスサイクル4回に1回を付与され、長時間のロックアウトが防止される。これはCPUがタイムクリティカルな割り込みを処理したり、Chip RAM内のOSデータ構造にアクセスする必要がある場合に重要である。

<!-- section: $DFF096 "割り込みシステム" -->

Amigaの割り込みシステムは**PAULA**が管理し、すべてのソースからの割り込み要求を収集して適切な68000割り込みレベル信号を生成する。14の割り込みソースが6つの優先度レベルに構成されている（レベル7/NMIは使用されない）。

2つのレジスタがシステムを制御し、いずれもDMACONと同じSET/CLR機構を使用する：

<div class="register-block">
<div class="reg-title">割り込みレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF09C</span><span class="reg-val">INTREQ — 割り込み要求（書き込み）</span></div>
<div class="reg-row"><span class="reg-field">$DFF01E</span><span class="reg-val">INTREQR — 割り込み要求（読み取り）</span></div>
<div class="reg-row"><span class="reg-field">$DFF09A</span><span class="reg-val">INTENA — 割り込みイネーブル（書き込み）</span></div>
<div class="reg-row"><span class="reg-field">$DFF01C</span><span class="reg-val">INTENAR — 割り込みイネーブル（読み取り）</span></div>
</div>

INTREQとINTENAは同じビットレイアウトを共有する：

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">レベル</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">SET/CLR</span><span class="bit-name">—</span><span class="bit-func">セット/クリア制御ビット</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">14</span><span class="bit-dir">INTEN</span><span class="bit-name">(6)</span><span class="bit-func">マスタ割り込みイネーブル（割り込みが発生するにはセット必須）</span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">EXTER</span><span class="bit-name">6</span><span class="bit-func">CIA-B割り込みまたは拡張ポート</span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">DSKSYN</span><span class="bit-name">5</span><span class="bit-func">ディスク同期ワード一致検出</span></div>
<div class="bit-row"><span class="bit-num">11</span><span class="bit-dir">RBF</span><span class="bit-name">5</span><span class="bit-func">シリアルポート受信バッファフル</span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">AUD3</span><span class="bit-name">4</span><span class="bit-func">オーディオチャネル3データ送信完了</span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">AUD2</span><span class="bit-name">4</span><span class="bit-func">オーディオチャネル2データ送信完了</span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">AUD1</span><span class="bit-name">4</span><span class="bit-func">オーディオチャネル1データ送信完了</span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">AUD0</span><span class="bit-name">4</span><span class="bit-func">オーディオチャネル0データ送信完了</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">BLIT</span><span class="bit-name">3</span><span class="bit-func">Blitter操作完了</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">VERTB</span><span class="bit-name">3</span><span class="bit-func">垂直帰線消去 — 各フレーム開始時（ライン0、50Hz）</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">COPER</span><span class="bit-name">3</span><span class="bit-func">Copper生成割り込み</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">PORTS</span><span class="bit-name">2</span><span class="bit-func">CIA-A割り込みまたは拡張ポート</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">SOFT</span><span class="bit-name">1</span><span class="bit-func">ソフトウェア割り込み（INTREQへの書き込みでトリガー）</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">DSKBLK</span><span class="bit-name">1</span><span class="bit-func">ディスクDMA転送完了</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">TBE</span><span class="bit-name">1</span><span class="bit-func">シリアルポート送信バッファ空</span></div>
</div>

### 割り込みの発生条件

割り込みが68000に到達するには、3つの条件を満たす必要がある：

1. **INTREQ**の対応するビットがセットされている（ハードウェアソースがサービスを要求）
2. **INTENA**の対応するビットがセットされている（ソースが有効化されている）
3. INTENAの**INTEN**ビット（ビット14）がセットされている（マスタイネーブルがオン）

3つの条件すべてが真のとき、PAULAは68000のIPLピンに適切な割り込みレベルをアサートする。

### 割り込みの確認応答

割り込みの処理後、ハンドラはINTREQの対応するビットを書き込みで**必ず**クリアしなければならない。ハードウェアはリクエストビットを自動クリアしない。CIA割り込みについては、CIAのICRレジスタを読むとCIA側のフラグはクリアされるが、対応するINTREQビット（CIA-Aはビット3、CIA-Bはビット13）は明示的にクリアする必要がある。

<!-- section: $DFF096 "割り込みベクタ" -->

68000はAmiga上で自動ベクタ割り込みを使用する。各優先度レベルは固定のベクタアドレスにマッピングされる：

<div class="register-block">
<div class="reg-title">68000割り込みベクタ</div>
<div class="reg-row"><span class="reg-field">$064 (Vec 25)</span><span class="reg-val">レベル1 — TBE, DSKBLK, SOFT</span></div>
<div class="reg-row"><span class="reg-field">$068 (Vec 26)</span><span class="reg-val">レベル2 — PORTS（CIA-A、拡張）</span></div>
<div class="reg-row"><span class="reg-field">$06C (Vec 27)</span><span class="reg-val">レベル3 — COPER, VERTB, BLIT</span></div>
<div class="reg-row"><span class="reg-field">$070 (Vec 28)</span><span class="reg-val">レベル4 — AUD0, AUD1, AUD2, AUD3</span></div>
<div class="reg-row"><span class="reg-field">$074 (Vec 29)</span><span class="reg-val">レベル5 — RBF, DSKSYN</span></div>
<div class="reg-row"><span class="reg-field">$078 (Vec 30)</span><span class="reg-val">レベル6 — EXTER（CIA-B、拡張）、INTEN</span></div>
<div class="reg-row"><span class="reg-field">$07C (Vec 31)</span><span class="reg-val">レベル7 — NMI（Amigaハードウェアでは未使用）</span></div>
</div>

レベル番号が大きいほど優先度が高い。レベル6の割り込みはレベル3のハンドラを先取りできるが、逆は不可。複数のソースが同一レベルを共有するため、割り込みハンドラはINTREQRを読んでどのソースがトリガーしたかを判定する必要がある。

### CIA割り込みのルーティング

2つのCIAはPAULAを介して異なる方法で割り込みをルーティングする：

- **CIA-A**は**PORTS**割り込み（ビット3、レベル2）をトリガーする。具体的なCIA-Aソースは、CIA-AのICRレジスタを読んで特定する。
- **CIA-B**は**EXTER**割り込み（ビット13、レベル6）をトリガーする。具体的なCIA-Bソースは、CIA-BのICRレジスタを読んで特定する。

PORTSとEXTERはどちらもZorroバス上の拡張カードからトリガーすることも可能。

### ソフトウェア割り込みとCopper割り込み

SOFTビット（ビット2）により、INTREQに書き込むことでソフトウェアがレベル1割り込みを生成できる。CopperもMOVE操作のみを実行できるため、INTREQに適切な値を書き込むことでCOPER割り込み（ビット4、レベル3）をトリガーする：

```asm
    ; 割り込みをトリガーするCopper命令:
    MOVE #$8010,INTREQ    ; INTREQのビット4（COPER）をセット
```

**VERTB**割り込み（ビット5、レベル3）は毎回の垂直帰線消去期間の開始時（ライン0）、PALでは毎秒50回発生する。これはフレーム同期操作の主要なタイミング基準であり、アニメーション更新、入力読み取り、Copperによるビットプレーンポインタの再初期化に使用される。
