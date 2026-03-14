---
title: "Blitter"
addr: "$DFF040"
description: "Amiga Blitter — データコピー、論理結合、面塗りつぶし、直線描画が可能な高速ブロック画像転送ユニット。"
order: 9
---

<!-- section: $DFF040 "Blitterの概要" -->

**Blitter**（Block Image Transferrer）は、Agnusチップに内蔵された専用ハードウェアユニットで、メモリブロックのコピーと結合を高速に実行する。毎秒**1600万ピクセル以上**を処理でき、68000 CPUのソフトウェア処理をはるかに上回る速度である。

Blitterには3つの主要動作モードがある。

- **データコピーと論理結合** — 最大3つのソースチャンネルを256種類のブール関数で結合しながらメモリブロックを転送
- **面塗りつぶし** — 単一ピクセル幅のエッジで囲まれた領域を塗りつぶす
- **直線描画** — Bresenhamアルゴリズムのハードウェア実装により毎秒100万ピクセルで直線を描画

AmigaOSは事実上すべてのグラフィック操作にBlitterを使用する：テキスト描画、ガジェット描画、ウィンドウのスクロール、グラフィカルデータのクリッピングなど。

### 基本動作

Blitterは矩形メモリ領域（「Blitterウィンドウ」）に対して動作する。最大3つのソースチャンネル — **A**、**B**、**C** — からデータを読み込み、選択された論理関数に従って結合し、結果をデスティネーション**D**に書き込む。4つのアドレスはすべてChip RAM（$000000〜$07FFFF）内の任意の位置に設定できる。

結合は**ビット単位**で行われる：各ビット位置について、A、B、Cの対応するビットが論理関数に入力され、Dの出力ビットが生成される。

<!-- section: $DFF040 "Blitterウィンドウとモジュロ" -->

Blitterウィンドウは1回の操作で処理される矩形メモリ領域を定義する。**BLTSIZE**レジスタで高さ（ライン数）と幅（ワード数）として指定する。

| レジスタ | ビット | 名前 | 機能 |
|----------|--------|------|------|
| BLTSIZE | 15–6 | H9–H0 | 高さ — 1〜1024ライン（0＝1024） |
| BLTSIZE | 5–0 | W5–W0 | 幅 — 1〜64ワード（0＝64、最大1024ピクセル） |

レジスタ値の計算式：`BLTSIZE = 高さ * 64 + 幅`

**BLTSIZEへの書き込みでBlitterが即座に起動する。** 常にBlitter操作で最後にプログラムされるレジスタでなければならない。

### ソースとデスティネーションのポインタ

各チャンネルには開始アドレスを保持するポインタレジスタペアがある。

<div class="register-block">
<div class="reg-title">Blitterポインタレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF050</span><span class="reg-val">BLTAPTH/L — ソースAポインタ（ビット16–18 / 0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF04C</span><span class="reg-val">BLTBPTH/L — ソースBポインタ（ビット16–18 / 0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF048</span><span class="reg-val">BLTCPTH/L — ソースCポインタ（ビット16–18 / 0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF054</span><span class="reg-val">BLTDPTH/L — デスティネーションDポインタ（ビット16–18 / 0–15）</span></div>
</div>

### モジュロ値

Blitterウィンドウが実際のビットマップより小さい場合、**モジュロ**値が各ライン末尾でスキップするバイト数を指定する。これにより、大きなビットマップの一部分だけをブリットできる。

<div class="register-block">
<div class="reg-title">Blitterモジュロレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF064</span><span class="reg-val">BLTAMOD — ソースAモジュロ（バイト、符号付き）</span></div>
<div class="reg-row"><span class="reg-field">$DFF062</span><span class="reg-val">BLTBMOD — ソースBモジュロ（バイト、符号付き）</span></div>
<div class="reg-row"><span class="reg-field">$DFF060</span><span class="reg-val">BLTCMOD — ソースCモジュロ（バイト、符号付き）</span></div>
<div class="reg-row"><span class="reg-field">$DFF066</span><span class="reg-val">BLTDMOD — デスティネーションDモジュロ（バイト、符号付き）</span></div>
</div>

例えば、10ワード幅のグラフィックを20ワード幅のビットプレーンにコピーする場合：ソースモジュロは0（グラフィックの幅がBlitterウィンドウと同じ）、デスティネーションモジュロは`(20 - 10) * 2 = 20`バイトとなる。

<!-- section: $DFF040 "ミンターム（論理関数）" -->

BlitterによるソースA、B、Cの論理結合は**ミンターム**と呼ばれる8ビット（LF0〜LF7）で制御される。これらのビットは8つの入力組み合わせのどれが出力ビットDで1を生成するかを選択する。選択されたすべてのミンタームの結果がOR結合される。

| ビット | ミンターム | 入力ABC | 等式（真のとき1） |
|--------|-----------|---------|-------------------|
| LF7 | ABC | 111 | A AND B AND C |
| LF6 | ABc | 110 | A AND B AND (NOT C) |
| LF5 | AbC | 101 | A AND (NOT B) AND C |
| LF4 | Abc | 100 | A AND (NOT B) AND (NOT C) |
| LF3 | aBC | 011 | (NOT A) AND B AND C |
| LF2 | aBc | 010 | (NOT A) AND B AND (NOT C) |
| LF1 | abC | 001 | (NOT A) AND (NOT B) AND C |
| LF0 | abc | 000 | (NOT A) AND (NOT B) AND (NOT C) |

### よく使うミンターム値

- **D = A**（単純コピー）：LF7–LF0 = `$F0`（11110000）。B、Cに関係なくA=1のときD=1。
- **D = NOT A**（反転）：LF7–LF0 = `$0F`（00001111）。
- **D = A OR B**（ビットプレーンへのグラフィック重ね描き）：LF7–LF0 = `$FC`（11111100）。
- **D = A AND B**（マスク）：LF7–LF0 = `$C0`（11000000）。
- **クッキーカット**（D = AB + aC、マスク付きブリット）：LF7–LF0 = `$CA`。

使用しないソースチャンネルは、そのDMAを無効にし（BLTCON0のUSEx=0）、ミンタームがDに影響しないようにする。

<!-- section: $DFF040 "シフトとマスク" -->

### バレルシフタ

Blitterはソース**A**と**B**のデータを0〜15ビット右にシフトできるバレルシフタを備えている。これにより、ワード境界だけでなく任意のピクセル位置にグラフィックスを正確に配置できる。

シフトはライン全体に適用され、1ワードからシフトアウトしたビットは次のワードに入る。この操作に追加時間は不要で、シフト量に関係なくBlitterは最高速度で動作する。

シフト値は制御レジスタの上位4ビットに格納される。
- **ASH3–ASH0**（BLTCON0ビット15–12）：ソースAのシフト量
- **BSH3–BSH0**（BLTCON1ビット15–12）：ソースBのシフト量

### 先頭・末尾ワードマスク

グラフィックの端がワード境界に合わない場合、ソースAの**先頭ワードマスク**と**末尾ワードマスク**で各ラインの先頭と末尾の不要なビットを選択的にマスクできる。

<div class="register-block">
<div class="reg-title">Blitterマスクレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF044</span><span class="reg-val">BLTAFWM — ソースA先頭ワードマスク（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF046</span><span class="reg-val">BLTALWM — ソースA末尾ワードマスク（ビット0–15）</span></div>
</div>

マスクで**セットされた**ビットのみが通過し、マスクされたビットは0に強制される。Blitterウィンドウが1ワード幅の場合、両方のマスクが同時に適用される（AND結合）。

<!-- section: $DFF040 "制御レジスタ" -->

### BLTCON0（$DFF040、ライトオンリー）

| ビット | 名前 | 機能 | 説明 |
|--------|------|------|------|
| 15–12 | ASH3–ASH0 | ソースAシフト | ソースAデータの右シフト量（0–15） |
| 11 | USEA | A DMA有効 | ソースAのDMAチャンネルを有効化 |
| 10 | USEB | B DMA有効 | ソースBのDMAチャンネルを有効化 |
| 9 | USEC | C DMA有効 | ソースCのDMAチャンネルを有効化 |
| 8 | USED | D DMA有効 | デスティネーションDのDMAチャンネルを有効化 |
| 7–0 | LF7–LF0 | ミンターム | 論理関数の選択（上記参照） |

USExビットが0の場合、対応するDMAチャンネルは無効になる。Blitterはそのチャンネルのデータレジスタ（BLTxDAT）に最後に書き込まれた値を再利用する。CPUでBLTxDATに値を書き込み、そのチャンネルのDMAを無効にしてBlitterを実行することで、メモリをパターンで塗りつぶすことができる。

### BLTCON1（$DFF042、ライトオンリー）

| ビット | 名前 | 機能 | 説明 |
|--------|------|------|------|
| 15–12 | BSH3–BSH0 | ソースBシフト | ソースBデータの右シフト量（0–15） |
| 11–5 | — | 未使用 | コピーモードでは0 |
| 4 | EFE | 排他的塗りつぶし | 排他的塗りつぶしモードを有効化 |
| 3 | IFE | 包括的塗りつぶし | 包括的塗りつぶしモードを有効化 |
| 2 | FCI | フィルキャリー初期値 | フィルキャリービットの初期値 |
| 1 | DESC | 降順モード | アドレスを降順で処理 |
| 0 | LINE | 直線描画モード | 直線描画モードを有効化（コピー/塗りつぶし時は0） |

### 昇順モード vs 降順モード

**昇順モード**（DESC=0、デフォルト）では、BlitterはBLTxPTから開始し各ワード後にアドレスをインクリメントする。**降順モード**（DESC=1）では、BLTxPTから開始しデクリメントする。降順モードはソース領域とデスティネーション領域が重なり、デスティネーションがより高いアドレスにある場合に不可欠である。

モード選択のルール：
- 重なりなし：どちらのモードでも正常に動作
- デスティネーションがソースの前：昇順モードのみ
- デスティネーションがソースの後：降順モードのみ

降順モードでは、BLTxPTは領域の**最後のワード**（右下隅）を指す必要がある。

<!-- section: $DFF040 "面塗りつぶし" -->

Blitterは各ラインを右から左にスキャンし、セットされたピクセルごとに**フィルキャリー（FC）**ビットをトグルすることで、囲まれた領域を塗りつぶせる。2つのセットされたピクセル間のすべてのピクセルが塗りつぶされる。

塗りつぶしアルゴリズム：各ラインの最右ビットから開始し、FCビットは初期値0（またはBLTCON1のFCI=1の場合は1）に設定される。各入力ビットについて：
- 入力ビットが0の場合、出力は現在のFC値を取る
- 入力ビットが1の場合、FCビットが**トグル**（反転）される

1ラインに奇数個の境界ピクセルがあると塗りつぶしエラーが発生し、ラインの端まで「漏れる」。

### 包括的 vs 排他的塗りつぶし

- **包括的塗りつぶし（IFE=1）**：境界ピクセルが出力に保持される。塗りつぶし領域は両端を含む。
- **排他的塗りつぶし（EFE=1）**：左境界ピクセル（FCが1から0に遷移する箇所）がクリアされる。これにより1ピクセル幅の塗りつぶし領域が可能になる。

### 塗りつぶしの要件

- **降順モードを使用**（DESC=1）— Blitterは右から左に塗りつぶすため、ワードを高アドレスから低アドレスの順で処理する必要がある
- LINEは0
- ソースデータには適切な単一ピクセル幅の境界線が必要
- BLTSIZEは塗りつぶすグラフィックのサイズに設定

塗りつぶし速度はコピー速度と同等：毎秒最大1600万ピクセル。

<!-- section: $DFF040 "直線描画" -->

Blitterはハードウェア実装のBresenhamスタイルアルゴリズムを使用して、毎秒**100万ピクセル**で直線を描画できる。2つの端点を単純に与えるのではなく、直線の傾きと方向を定義する事前計算値が必要となる。

### オクタント

始点から終点への方向は**8つのオクタント**の1つに分類される。3つの比較でオクタントが決定される：

1. X2 >= X1か？（左右の判定）
2. Y2 >= Y1か？（上下の判定）
3. DeltaX >= DeltaYか？（主軸の判定）

各オクタントは3つの制御ビットで構成される3ビットコードに対応する。

| オクタント | 条件 | SUD/SUL/AUL | コード |
|-----------|------|-------------|--------|
| 0 | X1<=X2, Y1<=Y2, dX>=dY | 1/1/0 | 6 |
| 1 | X1<=X2, Y1<=Y2, dX<=dY | 0/0/1 | 1 |
| 2 | X1>=X2, Y1<=Y2, dX<=dY | 0/1/1 | 3 |
| 3 | X1>=X2, Y1<=Y2, dX>=dY | 1/1/1 | 7 |
| 4 | X1>=X2, Y1>=Y2, dX>=dY | 1/0/1 | 5 |
| 5 | X1>=X2, Y1>=Y2, dX<=dY | 0/1/0 | 2 |
| 6 | X1<=X2, Y1>=Y2, dX<=dY | 0/0/0 | 0 |
| 7 | X1<=X2, Y1>=Y2, dX>=dY | 1/0/0 | 4 |

### 傾きの計算

**Pdelta** = min(DeltaX, DeltaY)、**Gdelta** = max(DeltaX, DeltaY) とする。Blitterには以下が必要：

1. `2 * Pdelta`（BLTBMODに書き込む）
2. `2 * Pdelta - Gdelta`（BLTAPTLに書き込む）
3. `2 * Pdelta - 2 * Gdelta`（BLTAMODに書き込む）

**SIGN**フラグ（BLTCON1のビット6）は`2 * Pdelta < Gdelta`のとき1にセットする。

### 直線モードのレジスタ設定

<div class="register-block">
<div class="reg-title">直線モードのBlitterレジスタ</div>
<div class="reg-row"><span class="reg-field">BLTAPTL</span><span class="reg-val">2 * Pdelta - Gdelta（初期誤差項）</span></div>
<div class="reg-row"><span class="reg-field">BLTAMOD</span><span class="reg-val">2 * Pdelta - 2 * Gdelta</span></div>
<div class="reg-row"><span class="reg-field">BLTBMOD</span><span class="reg-val">2 * Pdelta</span></div>
<div class="reg-row"><span class="reg-field">BLTCPT / BLTDPT</span><span class="reg-val">ビットプレーン内の開始ピクセルのアドレス</span></div>
<div class="reg-row"><span class="reg-field">BLTCMOD / BLTDMOD</span><span class="reg-val">ビットプレーンの幅（バイト単位、例：320ピクセル表示で40）</span></div>
<div class="reg-row"><span class="reg-field">BLTADAT</span><span class="reg-val">$8000（ビット15に単一ピクセル）</span></div>
<div class="reg-row"><span class="reg-field">BLTBDAT</span><span class="reg-val">直線パターンマスク（例：$FFFFで実線）</span></div>
<div class="reg-row"><span class="reg-field">BLTAFWM</span><span class="reg-val">$FFFF</span></div>
<div class="reg-row"><span class="reg-field">BLTSIZE</span><span class="reg-val">幅=2、高さ=直線の長さ（Gdelta + 1）</span></div>
</div>

直線モードの**BLTCON0**：ビット15–12 = START位置（X1 AND $F）、ビット11–8 = `$B`（USEA=1, USEB=0, USEC=1, USED=1）、ビット7–0 = `$CA`（ミンターム D = aC + AB）。

直線モードの**BLTCON1**：ビット15–12 = STARTと同値（テクスチャ整列用）、ビット6 = SIGN、ビット4–2 = SUD/SUL/AUL、ビット1 = SING（塗りつぶし境界用に水平ライン1ピクセルのみ）、ビット0 = LINE = 1。

BLTBDATの直線パターンで破線や点線が描ける。`$FFFF`で実線、`$FF00`のようなパターンで破線になる。

<!-- section: $DFF040 "DMAとステータス" -->

Blitterは4つのDMAチャンネル（ソース/デスティネーション各1つ）を使用し、**DMACON**の**BLTEN**ビット（ビット6）で制御される。4チャンネルすべてが同時に有効化される。

<div class="register-block">
<div class="reg-title">Blitterデータレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF000</span><span class="reg-val">BLTDDAT — デスティネーションデータ出力（リードオンリー）</span></div>
<div class="reg-row"><span class="reg-field">$DFF070</span><span class="reg-val">BLTCDAT — ソースCデータレジスタ</span></div>
<div class="reg-row"><span class="reg-field">$DFF072</span><span class="reg-val">BLTBDAT — ソースBデータレジスタ</span></div>
<div class="reg-row"><span class="reg-field">$DFF074</span><span class="reg-val">BLTADAT — ソースAデータレジスタ</span></div>
</div>

### DMAコンステータスビット

DMACONR（$DFF002）の3つのリードオンリービットがBlitterの状態を報告する。

- **ビット14 — BBUSY**：Blitter動作中に1にセットされる。BLTSIZEへの書き込み後にBBUSYがセットされ、操作完了時にクリアされる。同時にBlitter完了割り込み（INTREQビット6）がトリガーされる。

- **ビット13 — BZERO**：最後の操作で**すべての**出力ビットがゼロだった場合に1にセットされる。衝突検出に有用：2つのソースが重なった場合のみD=1となるようミンタームを設定し、USEDを無効にして（書き込み回避）、完了後にBZEROを確認する。BZERO=0なら少なくとも1ピクセルが重なっている。

- **ビット10 — BLTPRI**：セットすると、Blitterが68000 CPUに対して絶対的なバス優先権を持つ（Blitter nastyモード）。Blitterのスループットは最大化されるが、完了までCPUは完全に停止する。

### Blitterの完了待ち

新しいBlitter操作を開始する前に、前の操作が完了していることを必ずBBUSYをポーリングして確認する。

```asm
.wait:
    btst    #6,$DFF002     ; DMAコンRのBBUSYをテスト
    bne.s   .wait          ; Blitterがアイドルになるまでループ
```
