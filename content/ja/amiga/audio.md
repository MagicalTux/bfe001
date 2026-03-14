---
title: "オーディオシステム"
addr: "$DFF0A0"
description: "Amigaオーディオシステム — 8ビットサンプル、ステレオ出力、ボリューム制御、チャンネル間変調を備えた4チャンネルDMA駆動オーディオ。"
order: 10
---

<!-- section: $DFF0A0 "オーディオの概要" -->

Amigaのオーディオシステムは、Chip RAMからデジタル化された8ビットサウンドサンプルを再生できる**4つの独立したDMAチャンネル**で構成される。**ステレオ**出力で、チャンネルとスピーカーの対応は固定されている。

- **左スピーカー**: チャンネル0とチャンネル3
- **右スピーカー**: チャンネル1とチャンネル2

各チャンネルには独自のサンプルポインタ、長さカウンタ、ボリューム制御、ピリオド（サンプルレート）レジスタがある。ハードウェアがDMA経由で連続再生を自動的に処理するため、CPUはレジスタを設定してDMAを有効にするだけでよい。

### サウンド生成の仕組み

Amigaはデジタル化された波形をメモリに**8ビット符号付き値**（2の補数、範囲-128〜+127）のシーケンスとして格納する。デジタル・アナログ変換器（DAC）がプログラム可能なレートでこれらのサンプルを読み取り、アナログ電圧出力を生成する。出力はローパスフィルタを通ってオーディオ出力ジャックに送られる。

サウンドサンプルは16ビットワードにパックされたバイトペアとしてChip RAMに格納される。DMAコントローラは一度に1ワードを読み込み、**上位バイト**（ビット15–8）が先に出力され、次に**下位バイト**（ビット7–0）が出力される。このため、波形データは常に**偶数個のサンプル**で構成する必要がある。

サウンドの3つの基本パラメータ — **周波数**（ピッチ）、**振幅**（音量）、**音色**（波形の形状）— はすべてソフトウェアで制御可能である。周波数はピリオドレジスタ、振幅はボリュームレジスタで設定し、音色は波形データ自体で決まる。

<!-- section: $DFF0A0 "チャンネルごとのレジスタ" -->

各オーディオチャンネルには16バイトを占める5つのレジスタがある。4チャンネルは$DFF0A0から連続した16バイトオフセットに配置されている。

<div class="register-block">
<div class="reg-title">チャンネル0レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF0A0</span><span class="reg-val">AUD0LCH — サンプルポインタ、上位ワード（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A2</span><span class="reg-val">AUD0LCL — サンプルポインタ、下位ワード（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A4</span><span class="reg-val">AUD0LEN — サンプル長（ワード単位、1ワード＝2サンプル）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A6</span><span class="reg-val">AUD0PER — サンプルピリオド（再生レート）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A8</span><span class="reg-val">AUD0VOL — ボリューム（0–64）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0AA</span><span class="reg-val">AUD0DAT — オーディオデータレジスタ（書き込みでDMA転送をトリガー）</span></div>
</div>

<div class="register-block">
<div class="reg-title">チャンネル1レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF0B0</span><span class="reg-val">AUD1LCH — サンプルポインタ、上位ワード</span></div>
<div class="reg-row"><span class="reg-field">$DFF0B2</span><span class="reg-val">AUD1LCL — サンプルポインタ、下位ワード</span></div>
<div class="reg-row"><span class="reg-field">$DFF0B4</span><span class="reg-val">AUD1LEN — サンプル長（ワード単位）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0B6</span><span class="reg-val">AUD1PER — サンプルピリオド</span></div>
<div class="reg-row"><span class="reg-field">$DFF0B8</span><span class="reg-val">AUD1VOL — ボリューム（0–64）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0BA</span><span class="reg-val">AUD1DAT — オーディオデータレジスタ</span></div>
</div>

<div class="register-block">
<div class="reg-title">チャンネル2レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF0C0</span><span class="reg-val">AUD2LCH — サンプルポインタ、上位ワード</span></div>
<div class="reg-row"><span class="reg-field">$DFF0C2</span><span class="reg-val">AUD2LCL — サンプルポインタ、下位ワード</span></div>
<div class="reg-row"><span class="reg-field">$DFF0C4</span><span class="reg-val">AUD2LEN — サンプル長（ワード単位）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0C6</span><span class="reg-val">AUD2PER — サンプルピリオド</span></div>
<div class="reg-row"><span class="reg-field">$DFF0C8</span><span class="reg-val">AUD2VOL — ボリューム（0–64）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0CA</span><span class="reg-val">AUD2DAT — オーディオデータレジスタ</span></div>
</div>

<div class="register-block">
<div class="reg-title">チャンネル3レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF0D0</span><span class="reg-val">AUD3LCH — サンプルポインタ、上位ワード</span></div>
<div class="reg-row"><span class="reg-field">$DFF0D2</span><span class="reg-val">AUD3LCL — サンプルポインタ、下位ワード</span></div>
<div class="reg-row"><span class="reg-field">$DFF0D4</span><span class="reg-val">AUD3LEN — サンプル長（ワード単位）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0D6</span><span class="reg-val">AUD3PER — サンプルピリオド</span></div>
<div class="reg-row"><span class="reg-field">$DFF0D8</span><span class="reg-val">AUD3VOL — ボリューム（0–64）</span></div>
<div class="reg-row"><span class="reg-field">$DFF0DA</span><span class="reg-val">AUD3DAT — オーディオデータレジスタ</span></div>
</div>

### AUDxLC — サンプルポインタ

この18ビットレジスタペアはChip RAM内の波形データの先頭を指す。DMAコントローラはデータ取得を開始する前にこの値を内部アドレスレジスタにコピーするため、元のAUDxLC値は保持され、現在の再生を中断せずに次のサイクル用に更新できる。

### AUDxLEN — サンプル長

長さは**ワード単位**（バイトではない）で指定する。例えば、16サンプルの波形は8ワードを占める。

```asm
MOVE.W #(End-Start)/2,AUD0LEN(a5)
```

DMAコントローラがAUDxLENワードを出力し終えると、AUDxLCとAUDxLENを内部レジスタに再ロードし、先頭から再開してループを形成する。

### AUDxPER — ピリオド（サンプルレート）

ピリオドレジスタは連続するサンプル出力間の時間を制御する。各単位は1バスサイクル＝**279.365ナノ秒**（PALシステムの場合、3.579545 MHzクロックに相当）。

ピリオド値の計算式：

```
ピリオド = 1 / (周波数 * 1サイクルあたりのサンプル数 * 279.365e-9)
```

または同等の式：

```
ピリオド = 3,579,545 / (周波数 * 1サイクルあたりのサンプル数)
```

**例：** 16サンプルの波形で440 Hzの音を再生する場合：

```
ピリオド = 1 / (440 * 16 * 0.000000279365) = 508
```

実用的な最小ピリオドは**124**（約28,867 Hzのサンプルレート）である。これ以下では、DMAチャンネルがデータを十分な速度で取得できない（ラスタラインあたり1つのDMAスロットしかない）ため、サンプルが繰り返される可能性がある。最大ピリオドは65,535で、極めて低速な再生が可能。

### AUDxVOL — ボリューム

ボリュームは**0**（無音）から**64**（最大）の範囲。64を超える値は64として扱われる。各チャンネルのボリュームは独立している。

```asm
MOVE.W #32,AUD0VOL(a5)    ; チャンネル0を半分の音量に
```

最良の音質を得るには、波形データを常に8ビットフル振幅（-128〜+127）で保持し、音量の制御にはボリュームレジスタを使用すること。波形の振幅を直接縮小すると相対的な量子化ノイズが増加する。

<!-- section: $DFF0A0 "オーディオDMAと割り込み" -->

### オーディオDMAの有効化

オーディオDMAは**DMACON**レジスタ（$DFF096）のビット0–3で制御される。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">DMAコンビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">チャンネル</span>
<span class="bit-func">説明</span>
</div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">AUD0EN</span><span class="bit-name">チャンネル0</span><span class="bit-func">オーディオチャンネル0のDMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">AUD1EN</span><span class="bit-name">チャンネル1</span><span class="bit-func">オーディオチャンネル1のDMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">AUD2EN</span><span class="bit-name">チャンネル2</span><span class="bit-func">オーディオチャンネル2のDMAを有効化</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">AUD3EN</span><span class="bit-name">チャンネル3</span><span class="bit-func">オーディオチャンネル3のDMAを有効化</span></div>
</div>

チャンネル0で再生を開始するには：

```asm
LEA     $DFF000,a5
MOVE.L  #SampleData,AUD0LCH(a5)   ; サンプルアドレスを設定
MOVE.W  #(SampleEnd-SampleData)/2,AUD0LEN(a5)  ; 長さを設定
MOVE.W  #508,AUD0PER(a5)          ; ピリオドを設定（16サンプルで440 Hz）
MOVE.W  #64,AUD0VOL(a5)           ; 最大ボリューム
MOVE.W  #$8201,DMACON(a5)         ; AUD0EN + DMAENを有効化
```

### DMAサイクル

オーディオDMAが有効化されると、以下のシーケンスが実行される：

1. AUDxLCとAUDxLENが内部レジスタにコピーされる
2. **オーディオ割り込み**がトリガーされる（新しい値をロードできることをCPUに通知）
3. DMAコントローラが内部アドレスからワードを取得（ラスタラインあたり1ワード）
4. 各ワードの上位バイトが先に出力され、次に下位バイトがAUDxPERで設定されたレートで出力
5. 内部ワードカウンタがゼロに達すると、AUDxLCとAUDxLENが再ロードされ、別の割り込みが発生

これにより、DMA動作中にCPUがAUDxLCとAUDxLENを更新できる。変更は次のサイクルの開始時に有効になる。2つのバッファを交互に使用することで、長いサンプルのシームレスなストリーミングが可能。

### オーディオ割り込み

各チャンネルにはINTREQ/INTENAに専用の割り込みビットがある。

- **ビット7**: オーディオチャンネル0
- **ビット8**: オーディオチャンネル1
- **ビット9**: オーディオチャンネル2
- **ビット10**: オーディオチャンネル3

4つすべてが**レベル4割り込み**である。DMAコントローラがAUDxLC/AUDxLENをレジスタから再ロードするたびに割り込みが発生する。高周波のサウンドでは割り込みが非常に頻繁に発生するため、CPUの飽和を避けるために必要な場合のみ有効にすること。

<!-- section: $DFF0A0 "変調" -->

Amigaはチャンネル間の**変調**をサポートしており、あるオーディオチャンネルが次のチャンネルのピリオドやボリュームをリアルタイムで変更する。変調チャンネルの波形データはDACには送られず、対象チャンネルのピリオドまたはボリュームレジスタに直接書き込まれる。

変調チェーンは固定されている：

- チャンネル0がチャンネル1を変調
- チャンネル1がチャンネル2を変調
- チャンネル2がチャンネル3を変調
- チャンネル3はモジュレータとして設定可能だが対象がない（データは破棄）

チャンネルがモジュレータとして使用される場合、そのオーディオ出力は**自動的に無効**になる。

### ADKCON — オーディオ/ディスク制御レジスタ

変調は**ADKCON**レジスタ（$DFF09E 書き込み / $DFF010 読み込み）で設定される。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func">説明</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">SET/CLR</span><span class="bit-name">セット/クリア</span><span class="bit-func">1＝リストされたビットをセット、0＝クリア</span></div>
<div class="bit-row"><span class="bit-num">14–8</span><span class="bit-dir">—</span><span class="bit-name">ディスク制御</span><span class="bit-func">ディスクコントローラのビット（オーディオとは無関係）</span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">USE3PN</span><span class="bit-name">Ch 3 ピリオド変調</span><span class="bit-func">チャンネル3は何も変調しない（対象なし）</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">USE2P3</span><span class="bit-name">Ch 2 -> Ch 3 ピリオド</span><span class="bit-func">チャンネル2がチャンネル3のピリオドを変調</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">USE1P2</span><span class="bit-name">Ch 1 -> Ch 2 ピリオド</span><span class="bit-func">チャンネル1がチャンネル2のピリオドを変調</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">USE0P1</span><span class="bit-name">Ch 0 -> Ch 1 ピリオド</span><span class="bit-func">チャンネル0がチャンネル1のピリオドを変調</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">USE3VN</span><span class="bit-name">Ch 3 ボリューム変調</span><span class="bit-func">チャンネル3は何も変調しない（対象なし）</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">USE2V3</span><span class="bit-name">Ch 2 -> Ch 3 ボリューム</span><span class="bit-func">チャンネル2がチャンネル3のボリュームを変調</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">USE1V2</span><span class="bit-name">Ch 1 -> Ch 2 ボリューム</span><span class="bit-func">チャンネル1がチャンネル2のボリュームを変調</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">USE0V1</span><span class="bit-name">Ch 0 -> Ch 1 ボリューム</span><span class="bit-func">チャンネル0がチャンネル1のボリュームを変調</span></div>
</div>

### ピリオド変調

ピリオド変調が有効な場合（例：USE0P1=1）、チャンネル0の波形データが16ビットピリオド値として解釈され、AUD1PERに直接書き込まれる。これにより**ビブラート**効果が生まれる — チャンネル1のピッチがチャンネル0で再生される波形に従って振動する。

### ボリューム変調

ボリューム変調が有効な場合（例：USE0V1=1）、チャンネル0のデータワードが7ビットボリューム値（ビット6–0に0–64）として解釈され、AUD1VOLに書き込まれる。これにより**トレモロ**効果が生まれ、また振幅エンベロープ（アタック/ディケイ/サスティン/リリース）の形成にも使用できる。

### 複合変調

同じチャンネルペアでピリオド変調とボリューム変調を同時に有効にできる。この場合、データワードは交互に使用される：

| ワード番号 | 書き込み先 |
|-----------|----------|
| 1 | ボリューム |
| 2 | ピリオド |
| 3 | ボリューム |
| 4 | ピリオド |
| ... | ... |

変調チャンネルのデータは2倍の速度で消費されるため、波形の長さをそれに合わせて計画すること。

<!-- section: $DFF0A0 "ローパスフィルタ" -->

Amigaには、DAC出力とオーディオ出力ジャック間に**ハードウェアローパスフィルタ**が搭載されている。このフィルタは約**4 kHz**以上の周波数を減衰させ、**7 kHz**以上を完全に遮断する。

### 目的

デジタル波形をアナログ信号として復元する際、サンプルレートと目的の周波数の和と差に等しい周波数で**エイリアシング**アーティファクトが発生する。ローパスフィルタがこれらのアーティファクトを除去し、よりクリーンなオーディオ出力を生成する。ただし、オーディオ帯域幅も制限され、複雑な波形（矩形波、鋸歯状波など）の高周波倍音が失われる。高いピッチの矩形波は正弦波のように聞こえ始める。

### フィルタ制御

フィルタは**CIA-AポートBのビット1**（$BFE001）で制御される。これは電源LEDの輝度を制御するのと同じビットである。

- **ビット1 = 0**: フィルタ**有効**（LEDが明るい）— デフォルト状態
- **ビット1 = 1**: フィルタ**無効**（LEDが暗い）

```asm
; ローパスフィルタを無効にする
BSET #1,$BFE001
```

プリフィルタ済みまたは高品質のサンプルを再生する場合、あるいは最大の周波数レンジが必要な場合はフィルタを無効にすべきである。単純な合成波形の場合は、可聴エイリアシングを避けるためにフィルタを有効のままにしておくこと。

### サンプリングレートのガイドライン

フィルタを有効にしていても可聴エイリアシングを避けるために、以下のルールに従う：

```
サンプルレート > 最高周波数成分 + 7000 Hz
```

「最高周波数成分」には基本周波数だけでなく波形の倍音も含まれることに注意。1 kHzの矩形波は3 kHz、5 kHz、7 kHz以上に重要な倍音を持つ。
