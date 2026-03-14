---
title: "カスタムチップ"
addr: "$DFF000"
description: "Amigaのカスタムチップセット — Agnus、Denise、Paula — アーキテクチャを定義する3つの専用プロセッサ。"
order: 3
---

<!-- section: $DFF000 "カスタムチップセット" -->

Amigaのユニークな能力は、Amiga Inc.のJay Minerチームが設計した3つのカスタムLSIチップから生まれる。これらは、グラフィックス、サウンド、I/OがDMA経由で68000 CPUから独立して動作する並列処理アーキテクチャを構成する。3つのチップはすべて**$DFF000**から始まるレジスタ空間を共有し、約200のライトオンリーレジスタで制御される。

カスタムチップは「チップバス」上にあり、**Chip RAM**（A500では最初の512 KB、ECSでは1 MBまで拡張可能）にのみアクセスできる。これはAmigaアーキテクチャの根本的な制約――すべてのグラフィックスデータ、サウンドサンプル、Copperリスト、スプライトデータはChip RAMに存在しなければならない。

<!-- section: $DFF000 "Agnus (8375)" -->

**Agnus**（Address Generator Unit）はカスタムチップセットのマスターチップである。Amiga 500では、Fat Agnus（8375）――マザーボード中央付近に配置される大型PLCC-84パッケージ。

Agnusの責務：

- **DMAコントローラ** — 25のDMAチャネルすべてを管理し、68000、Copper、Blitter、ビットプレーンフェッチ、スプライトフェッチ、オーディオ、ディスク間のChip RAMアクセスを調停
- **アドレス生成** — すべてのDMAアドレスを生成し、バスサイクル割り当てスキームによりChip RAMへのアクセスを多重化
- **Copper** — ディスプレイコプロセッサはAgnus内蔵
- **Blitter** — ブロックイメージ転送エンジンもAgnusの一部
- **ビームカウンタ** — ビデオビーム位置を追跡（VHPOSR/VHPOSW: $DFF004/$DFF02C）

<div class="register-block">
<div class="reg-title">主なAgnusレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF004 R</span><span class="reg-val">VPOSR — ビーム位置（垂直上位ビット + チップID）</span></div>
<div class="reg-row"><span class="reg-field">$DFF006 R</span><span class="reg-val">VHPOSR — ビーム位置（垂直下位 + 水平）</span></div>
<div class="reg-row"><span class="reg-field">$DFF096 W</span><span class="reg-val"><span class="highlight">DMACON — DMA制御（全DMAチャネルの有効/無効）</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF07C R</span><span class="reg-val">DENISEID — チップリビジョン識別</span></div>
</div>

### Fat Agnus (8375)

A1000のオリジナルAgnusは512 KBのChip RAMしかアドレスできなかった。A500とA2000の**Fat Agnus**（8375）はアドレスラインを1本追加して1 MBに拡張した。Fat AgnusはPAL/NTSCタイミング生成も統合しており、チップはリージョン固有――PALとNTSCのFat Agnusチップは互換性がない。

<!-- section: $DFF000 "Denise (8362)" -->

**Denise**（Display ENabler）はすべてのビデオ出力を処理する。Agnusからビットプレーン、スプライト、カラーデータを受け取り、モニタに送信される最終的なアナログRGB信号を生成する。

Deniseの責務：

- **ビットプレーンからピクセルへの変換** — 最大6つのビットプレーンDMAストリームをピクセルカラー値にシリアライズ
- **カラーパレット** — 32のカラーレジスタ（COLOR00–COLOR31: $DFF180–$DFF1BE）、12ビットRGB（4096色）
- **スプライトレンダリング** — プレイフィールド上に最大8つのスプライトを合成
- **プレイフィールド優先度** — スプライト対プレイフィールドの描画順序を決定（BPLCON2）
- **衝突検出** — スプライトとプレイフィールド間のハードウェア衝突検出（CLXDAT/CLXCON）
- **表示モード** — HAM（Hold-And-Modify）、Extra Half Brite（EHB）、デュアルプレイフィールド

<div class="register-block">
<div class="reg-title">主なDeniseレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF100 W</span><span class="reg-val">BPLCON0 — ビットプレーン制御（プレーン数、解像度、HAM/EHB有効化）</span></div>
<div class="reg-row"><span class="reg-field">$DFF102 W</span><span class="reg-val">BPLCON1 — プレイフィールドの水平スクロール値</span></div>
<div class="reg-row"><span class="reg-field">$DFF104 W</span><span class="reg-val">BPLCON2 — スプライト-プレイフィールド優先度と透過</span></div>
<div class="reg-row"><span class="reg-field">$DFF180 W</span><span class="reg-val"><span class="highlight">COLOR00 — 背景色（32カラーレジスタの最初）</span></span></div>
</div>

Deniseは23ピンビデオコネクタを通じてアナログRGBを出力する。各カラーチャネルは4ビットの精度を持ち、チャネルあたり16段階の輝度と合計4096色のパレットを提供する。同時表示色数は表示モードに依存する——1ビットプレーンの2色からHAMモードの4096色まで。

<!-- section: $DFF000 "Paula (8364)" -->

**Paula**はオーディオ出力、ディスクI/O、UARTシリアル通信、割り込み管理を担当する。カスタムチップセットの「I/Oハブ」。

Paulaの責務：

- **オーディオ** — 4つの独立した8ビットDMAオーディオチャネル、チャネル毎のボリューム制御、ステレオ出力（チャネル0+3が左、1+2が右）、チャネル間モジュレーション
- **ディスクコントローラ** — MFMフロッピーディスクDMA、同期ワード検出、リード/ライト制御
- **シリアルポート** — RS232通信用UART
- **割り込みコントローラ** — すべてのソースから割り込み要求を収集、優先度を付けて68000のIPLラインを駆動
- **アナログ入力** — ポテンショメータ/パドル入力測定（POTGO/POTINP）

<div class="register-block">
<div class="reg-title">主なPaulaレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF09A W</span><span class="reg-val"><span class="highlight">INTENA — 割り込みイネーブル（マスタースイッチ + ソース毎のイネーブル）</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF09C W</span><span class="reg-val">INTREQ — 割り込み要求（個別割り込みフラグのセット/クリア）</span></div>
<div class="reg-row"><span class="reg-field">$DFF01E R</span><span class="reg-val">INTREQR — 割り込み要求読み取り</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A0 W</span><span class="reg-val">AUD0LCH — オーディオチャネル0ロケーションポインタ（16オーディオレジスタの最初）</span></div>
<div class="reg-row"><span class="reg-field">$DFF020 W</span><span class="reg-val">DSKPTH — ディスクDMAポインタ（上位ワード）</span></div>
<div class="reg-row"><span class="reg-field">$DFF030 W</span><span class="reg-val">SERDAT — シリアルポートデータ書き込み</span></div>
</div>

<!-- section: $DFF000 "Gary (5719)" -->

**Gary**はアドレスデコーダとバスコントローラ――「グルーロジック」チップ。カスタムチップセットの一部ではないが、システム動作に不可欠。

Garyの責務：

- **アドレスデコーディング** — 各68000バスサイクルでどのチップが応答するかを決定（Chip RAM、Kickstart ROM、CIA-A、CIA-B、カスタムチップ、または拡張バス）
- **DTACK生成** — 68000にバスサイクルの完了を通知
- **ROMオーバーレイ** — リセット時のROMオーバーレイを制御（OVLビットへの最初の書き込みまで、Kickstart ROMをアドレス$000000にマッピング）
- **バスタイムアウト** — マップされていないアドレスへのアクセス時にバスエラーを生成

GaryはまたCIA-Aを奇数アドレス（$BFE001、$BFE101など）に、CIA-Bを偶数アドレス（$BFD000、$BFD100など）に配置するアドレスラインマッピングも処理する——このサイト名の由来である。

<!-- section: $DFF000 "バスサイクル割り当て" -->

Amigaのバスは約3.58 MHz（280 nsに1メモリサイクル）で動作する。ディスプレイの各水平ラインは227.5カラークロック（PAL）で構成され、各カラークロックは1バスサイクルに相当する。これらのサイクルは2つのカテゴリに分類される：

**奇数サイクル** — ビットプレーンDMA、スプライトDMA、オーディオDMA、ディスクDMA、リフレッシュに予約。68000は奇数サイクル中にアクセスできない。

**偶数サイクル** — Copper、Blitter、68000で共有。Copperが最も高い優先度を持ち、次にBlitter（BLTPRIがセットされている場合）、最後に68000。

このインターリーブ方式により、カスタムチップは連続的なDMAストリーム（例：毎ライン毎のビットプレーンデータフェッチ）を、68000を目に見える形で中断することなく維持できる――DMA活動が割り当てられた帯域幅内に収まっている限り。アクティブなDMAチャネルが多すぎる場合（例：hiresで6ビットプレーン + 全スプライト）、68000はバスサイクルを完全に奪われる可能性がある。
