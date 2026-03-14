---
title: "プレイフィールドとディスプレイ"
addr: "$DFF100"
description: "ビットプレーンの構成、解像度モード、カラーモード（HAM、EHB、デュアルプレイフィールド）、表示ウィンドウ、データフェッチ、スクロール。"
order: 7
---

<!-- section: $DFF100 "ビットプレーンの構成" -->

Amigaのすべてのグラフィックスは**ビットプレーン**から構築される――各ビットが1ピクセルを表す連続したメモリブロックである。1つのビットプレーンは2つの状態（オン/オフ）しか区別できないため、複数のビットプレーンを組み合わせてより多くの色を生成する。各プレーンの対応するビットを結合して得られる値がカラーレジスタを選択する。

### メモリレイアウト

ビットプレーンはChip RAM内の16ビットワードのシーケンスとして格納される。1ラインあたりのワード数が水平解像度を決定する：

- **低解像度（320ピクセル）：** 320 / 16 = 20ワード/ライン = 40バイト
- **高解像度（640ピクセル）：** 640 / 16 = 40ワード/ライン = 80バイト

各ワード内で、ビット15（MSB）が最も左のピクセルに、ビット0（LSB）が最も右のピクセルに対応する。ビットプレーンの最初のワードはディスプレイの左上隅にマッピングされる。

標準的な320x200 loresディスプレイの場合、1つのビットプレーンは20ワード x 200ライン = 4,000ワード = 8,000バイトを占有する。5ビットプレーン（32色）では合計40,000バイトのChip RAMを使用する。

### 解像度モード

Amigaは2つの水平解像度をサポートし、BPLCON0のHIRESビットで選択する：

- **低解像度（lores）：** 1ラインあたり320ピクセル。各ピクセルは140ns（2カラークロック）間表示される。最大**6ビットプレーン**を使用可能。
- **高解像度（hires）：** 1ラインあたり640ピクセル。各ピクセルは70ns（1カラークロック）間表示される。最大**4ビットプレーン**を使用可能。

垂直解像度はノンインターレースモードで200ライン（PALオーバースキャンで256ライン）、インターレースモードで400ライン（PALオーバースキャンで512ライン）。

### ビットプレーンポインタ

各ビットプレーンは一対のレジスタに格納される19ビット開始アドレスを必要とする。DMAコントローラはこれらのポインタを使用してChip RAMからビットプレーンデータを読み込む。

<div class="register-block">
<div class="reg-title">ビットプレーンポインタレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF0E0</span><span class="reg-val">BPL1PTH / BPL1PTL — ビットプレーン1ポインタ</span></div>
<div class="reg-row"><span class="reg-field">$DFF0E4</span><span class="reg-val">BPL2PTH / BPL2PTL — ビットプレーン2ポインタ</span></div>
<div class="reg-row"><span class="reg-field">$DFF0E8</span><span class="reg-val">BPL3PTH / BPL3PTL — ビットプレーン3ポインタ</span></div>
<div class="reg-row"><span class="reg-field">$DFF0EC</span><span class="reg-val">BPL4PTH / BPL4PTL — ビットプレーン4ポインタ</span></div>
<div class="reg-row"><span class="reg-field">$DFF0F0</span><span class="reg-val">BPL5PTH / BPL5PTL — ビットプレーン5ポインタ</span></div>
<div class="reg-row"><span class="reg-field">$DFF0F4</span><span class="reg-val">BPL6PTH / BPL6PTL — ビットプレーン6ポインタ</span></div>
</div>

各ポインタペアは`MOVE.L`命令1回でロードされる。各ラスタライン後、DMAコントローラは自動的にフェッチしたワード数分だけポインタをインクリメントする。最後の可視ライン後、ポインタは各ビットプレーンの先頭に**リセット**する必要がある――これは通常、各フレームの開始時にCopperが行う。

<!-- section: $DFF100 "カラーモード" -->

Amigaは赤、緑、青の3成分を混合して色を生成する。各成分は4ビットの強度（16レベル）を持ち、合計4,096色のパレット（$000〜$FFF）が利用可能。各カラーレジスタは12ビットRGB値を格納する。

### カラーパレット

<div class="register-block">
<div class="reg-title">カラーレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF180</span><span class="reg-val">COLOR00 — 背景/ボーダー色</span></div>
<div class="reg-row"><span class="reg-field">$DFF182</span><span class="reg-val">COLOR01</span></div>
<div class="reg-row"><span class="reg-field">$DFF184–$DFF1BC</span><span class="reg-val">COLOR02–COLOR30</span></div>
<div class="reg-row"><span class="reg-field">$DFF1BE</span><span class="reg-val">COLOR31</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">範囲</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">15–12</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">未使用（常に0）</span></div>
<div class="bit-row"><span class="bit-num">11–8</span><span class="bit-dir">R3–R0</span><span class="bit-name">0–15</span><span class="bit-func">赤成分</span></div>
<div class="bit-row"><span class="bit-num">7–4</span><span class="bit-dir">G3–G0</span><span class="bit-name">0–15</span><span class="bit-func">緑成分</span></div>
<div class="bit-row"><span class="bit-num">3–0</span><span class="bit-dir">B3–B0</span><span class="bit-name">0–15</span><span class="bit-func">青成分</span></div>
</div>

ビットプレーンの結合値がカラーパレットへのインデックスとして機能する。同時に表示可能な色数はアクティブなビットプレーン数に依存する：

| ビットプレーン数 | 色数 | 使用レジスタ |
|-----------|--------|----------------|
| 1 | 2 | COLOR00–01 |
| 2 | 4 | COLOR00–03 |
| 3 | 8 | COLOR00–07 |
| 4 | 16 | COLOR00–15 |
| 5 | 32 | COLOR00–31 |

高解像度では4ビットプレーンのみ利用可能で、同時表示は最大16色に制限される。

### Extra Half-Brite（EHB）モード

低解像度で6ビットプレーンすべてがアクティブな場合、6番目のビットプレーンは特別な役割を果たす：追加のカラーインデックスビットとしてではなく、輝度を制御する。

ビットプレーン1〜5（ビット0〜4）は通常通りカラーレジスタ（0〜31）を選択する。ビット5（ビットプレーン6から）が**0**の場合、色はフル輝度で表示される。ビット5が**1**の場合、各RGB成分が1ビット右シフト（2で除算）され、同じ色の半輝度バージョンが生成される。

これにより64の実効色――フル輝度32色と半輝度32色――が追加のカラーレジスタなしで得られる。

### Hold-And-Modify（HAM）モード

HAMモードは**4,096色**すべてを画面上に同時表示可能にする。低解像度で6ビットプレーンを必要とし、自然画像の空間的一貫性――隣接ピクセルは類似した色を持つ傾向――を利用する。

上位2ビット（ビットプレーン5と6から）が、下位4ビット（ビットプレーン1〜4から）の解釈方法を決定する：

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット5</span>
<span class="bit-dir">ビット4</span>
<span class="bit-name">下位ビット</span>
<span class="bit-func">動作</span>
</div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">0</span><span class="bit-name">C3–C0</span><span class="bit-func">カラーレジスタC0–C3を直接選択（16パレット色）</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">1</span><span class="bit-name">B3–B0</span><span class="bit-func">前のピクセルの赤と緑を保持、青を変更</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">0</span><span class="bit-name">R3–R0</span><span class="bit-func">前のピクセルの青と緑を保持、赤を変更</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">1</span><span class="bit-name">G3–G0</span><span class="bit-func">前のピクセルの赤と青を保持、緑を変更</span></div>
</div>

各ラインの最初のピクセルでは、「前の」色としてCOLOR00（背景色）が使用される。ピクセルごとに1つのカラー成分しか変更できないため、3チャネルすべてにわたる滑らかなグラデーションには少なくとも3ピクセルが必要。この制約にもかかわらず、HAMは特に写真コンテンツに対して非常に自然な外観の画像を生成する。

### デュアルプレイフィールドモード

デュアルプレイフィールドモードは、2つの独立したプレイフィールドを重ね合わせて表示する。奇数番号のビットプレーン（1, 3, 5）が**プレイフィールド1**を、偶数番号のビットプレーン（2, 4, 6）が**プレイフィールド2**を形成する。

各プレイフィールドは最大3ビットプレーンを持ち、各8色。プレイフィールド1はCOLOR00〜07を、プレイフィールド2はCOLOR08〜15を使用する。いずれかのプレイフィールドでピクセル値0は**透過**を意味し、もう一方のプレイフィールド（または背景）が透けて見える。

| アクティブなビットプレーン数 | プレイフィールド1 | プレイフィールド2 |
|------------------|-------------|-------------|
| 2 | プレーン1 | プレーン2 |
| 4 | プレーン1, 3 | プレーン2, 4 |
| 6 | プレーン1, 3, 5 | プレーン2, 4, 6 |

hiresデュアルプレイフィールドモードでは、各プレイフィールドは2ビットプレーンのみ（4色）。BPLCON2のPF2PRIビットが、両方のプレイフィールドが同じ位置に非透過ピクセルを持つ場合にどちらが前面に表示されるかを制御する。

このモードはゲームでパララックススクロール（背景と前景のレイヤーが異なる速度でスクロール）に広く使用される。

<!-- section: $DFF100 "表示ウィンドウとデータフェッチ" -->

プレイフィールドの画面上の位置とサイズは2組のレジスタで制御される：**表示ウィンドウ**レジスタが可視領域を定義し、**データフェッチ**レジスタがビットプレーンDMAのタイミングを定義する。

### 表示ウィンドウ（DIWSTRT / DIWSTOP）

<div class="register-block">
<div class="reg-title">表示ウィンドウレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF08E</span><span class="reg-val">DIWSTRT — 表示ウィンドウ開始位置</span></div>
<div class="reg-row"><span class="reg-field">$DFF090</span><span class="reg-val">DIWSTOP — 表示ウィンドウ終了位置</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">レジスタ</span>
<span class="bit-dir">ビット</span>
<span class="bit-name">名前</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">DIWSTRT</span><span class="bit-dir">15–8</span><span class="bit-name">V7–V0</span><span class="bit-func">垂直開始位置（ライン番号）</span></div>
<div class="bit-row"><span class="bit-num">DIWSTRT</span><span class="bit-dir">7–0</span><span class="bit-name">H7–H0</span><span class="bit-func">水平開始位置（ピクセル）</span></div>
<div class="bit-row"><span class="bit-num">DIWSTOP</span><span class="bit-dir">15–8</span><span class="bit-name">V7–V0</span><span class="bit-func">垂直終了位置（V8はV7の反転で生成）</span></div>
<div class="bit-row"><span class="bit-num">DIWSTOP</span><span class="bit-dir">7–0</span><span class="bit-name">H7–H0</span><span class="bit-func">水平終了位置（H8は1にハードワイヤ）</span></div>
</div>

DIWSTRTはプレイフィールドが始まる左上隅を定義する。DIWSTOPは右下隅+1（プレイフィールドの*直後*の最初の位置）を定義する。表示ウィンドウの外側ではボーダー色（COLOR00）が表示される。

不足する上位ビットはハードウェアで生成される：DIWSTRTでは、V8とH8は0と見なされる（開始位置はライン0〜255、カラム0〜255に制限）。DIWSTOPでは、H8は1にハードワイヤされ、V8はV7の反転で、垂直方向128〜312、水平方向256〜511の停止位置が可能。

**標準値**（320x256ディスプレイをセンタリング）：

```
DIWSTRT = $2C81  （ライン44、カラム129）
DIWSTOP = $2CC1  （ライン300、カラム449）
```

PALディスプレイの可視領域は、垂直方向でライン26〜312、水平方向でカラム107以降。これらの範囲外のピクセルはブランキング期間内に入り、見ることができない。

### データフェッチ（DDFSTRT / DDFSTOP）

<div class="register-block">
<div class="reg-title">データフェッチレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF092</span><span class="reg-val">DDFSTRT — データフェッチ開始（最初のビットプレーンDMAサイクル）</span></div>
<div class="reg-row"><span class="reg-field">$DFF094</span><span class="reg-val">DDFSTOP — データフェッチ終了（最後のビットプレーンDMAサイクル）</span></div>
</div>

これらのレジスタはビットプレーンDMAが開始・終了する水平バスサイクル位置を指定する。ハードウェアは表示前にデータをフェッチする必要がある――loresでは表示開始の8.5バスサイクル（17 loresピクセル）前、hiresでは4.5サイクル（9 hiresピクセル）前。

**DDFSTRTとDDFSTOPの計算（lores）：**

```
DDFSTRT = (Hstart/2 - 8.5) AND $FFF8
DDFSTOP = DDFSTRT + (ピクセル数/ライン/2 - 8)
```

Hstart = $81、1ラインあたり320ピクセルの場合：
```
DDFSTRT = ($81/2 - 8.5) AND $FFF8 = $38
DDFSTOP = $38 + (320/2 - 8) = $D0
```

**DDFSTRTとDDFSTOPの計算（hires）：**

```
DDFSTRT = (Hstart/2 - 4.5) AND $FFFC
DDFSTOP = DDFSTRT + (ピクセル数/ライン/4 - 8)
```

Hstart = $81、1ラインあたり640ピクセルの場合：
```
DDFSTRT = ($81/2 - 4.5) AND $FFFC = $3C
DDFSTOP = $3C + (640/4 - 8) = $D4
```

**制約：** DDFSTRTは$18未満にしてはならない。DDFSTOPの最大値は$D8。DDFSTRTが$28未満だと水平ブランキング領域に入る。$34未満ではビットプレーンDMAがスプライトDMAスロットと重複し、スプライトが消失する――DDFSTRTを$30にするとスプライト1〜7が消える（スプライト0、マウスポインタは保持される）。

<!-- section: $DFF100 "スクロールとモジュロ" -->

Amigaは表示可能領域より大きなプレイフィールドのスムーズスクロールをサポートし、モジュロレジスタとBPLCON1のスクロール値を使用する。

### モジュロレジスタ

<div class="register-block">
<div class="reg-title">モジュロレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF108</span><span class="reg-val">BPL1MOD — 奇数ビットプレーン（1, 3, 5）のモジュロ</span></div>
<div class="reg-row"><span class="reg-field">$DFF10A</span><span class="reg-val">BPL2MOD — 偶数ビットプレーン（2, 4, 6）のモジュロ</span></div>
</div>

モジュロ値は各表示ライン後にBPLxPTに加算される。これにより、メモリ内のビットプレーンを表示ウィンドウより**広く**できる。例えば、ビットプレーンが640ピクセル幅（40ワード = 80バイト/ライン）だが320ピクセル（20ワード = 40バイト）のみ表示する場合、モジュロはフルライン幅と表示幅の差、40バイトに設定する。

DMAが1ラインの可視部分をフェッチした後、BPLxPTは40バイト分インクリメントされている。モジュロ（40バイト）を加算すると、非表示部分をスキップして、より広いビットプレーン内の次のラインの先頭に進む。

垂直スクロールでは、BPLxPTをビットプレーン内の異なるラインを指すように調整する。例えば、320ピクセル幅のディスプレイで100ライン下にスクロールするには、ベースアドレスに100 x 40 = 4,000バイトを加算する。Copperが毎フレームBPLxPTを更新する。

奇数と偶数のモジュロは別々で、デュアルプレイフィールドモードで2つのプレイフィールドを独立してスクロールできる。シングルプレイフィールドモードでは、両方のレジスタに同じ値を設定する。

### 水平ファインスクロール（BPLCON1）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">範囲</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">15–8</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">未使用</span></div>
<div class="bit-row"><span class="bit-num">7–4</span><span class="bit-dir">PF2H3–PF2H0</span><span class="bit-name">0–15</span><span class="bit-func">偶数ビットプレーン（プレイフィールド2）のスクロール遅延</span></div>
<div class="bit-row"><span class="bit-num">3–0</span><span class="bit-dir">PF1H3–PF1H0</span><span class="bit-name">0–15</span><span class="bit-func">奇数ビットプレーン（プレイフィールド1）のスクロール遅延</span></div>
</div>

スクロール値はビットプレーンデータの出力を0〜15低解像度ピクセル分遅延させ、表示を**右**にシフトする。16ピクセル（1ワード）単位のBPLxPT調整と組み合わせることで、ピクセル精度の水平スクロールが実現する。

**右スクロール：** スクロール値を0から15に増加させ、0にリセットしてBPLxPTを1ワード分デクリメント。

**左スクロール：** スクロール値を15から0に減少させ、15にリセットしてBPLxPTを1ワード分インクリメント。

スクロール中の左端の視覚的アーティファクトを防ぐため、DDFSTRTを通常より8バスサイクル早く設定すべき（loresで$38の代わりに$30）。これにより1ラインあたり1ワード多くのデータがフェッチされ、スクロールオフセットが正しく配置する。追加ワードに対応してモジュロ値も調整する必要がある。

BPLCON1は垂直帰線消去中に変更しなければならない――Copperリストまたは VERTB割り込みハンドラで行う。

<!-- section: $DFF100 "BPLCONレジスタ" -->

3つの制御レジスタが表示モード、スクロール、スプライト/プレイフィールドの優先度を設定する。

### BPLCON0（$DFF100）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">値</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">HIRES</span><span class="bit-name">0/1</span><span class="bit-func">高解像度モード（1ラインあたり640ピクセル）</span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">BPU2</span><span class="bit-name" rowspan="3">0–6</span><span class="bit-func">アクティブなビットプレーン数（3ビットカウンタ）</span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">BPU1</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">BPU0</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11</span><span class="bit-dir">HOMOD</span><span class="bit-name">0/1</span><span class="bit-func">Hold-And-Modifyモード有効化</span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">DBPLF</span><span class="bit-name">0/1</span><span class="bit-func">デュアルプレイフィールドモード有効化</span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">COLOR</span><span class="bit-name">0/1</span><span class="bit-func">コンポジットカラー出力有効化（RGBには影響しない）</span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">GAUD</span><span class="bit-name">0/1</span><span class="bit-func">Genlockオーディオ有効化</span></div>
<div class="bit-row"><span class="bit-num">7–4</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">未使用</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">LPEN</span><span class="bit-name">0/1</span><span class="bit-func">ライトペン有効化</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">2</span><span class="bit-dir">LACE</span><span class="bit-name">0/1</span><span class="bit-func">インターレースモード（垂直解像度2倍）</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">ERSY</span><span class="bit-name">0/1</span><span class="bit-func">外部同期（Genlock用）</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">未使用</span></div>
</div>

**重要事項：**

- HOMODとDBPLFを同時に有効にしてはならない
- Extra-Half-Briteモードは6ビットプレーン有効時に自動的にアクティブになる（HOMODもDBPLFもセットされていない場合）
- LACEを設定するとVPOSのLOFビットが毎フレームトグルし、ロングフレームとショートフレームが交互に表示される
- ERSYは同期信号ピンを出力から入力に切り替え、外部ビデオソースによるAmigaのディスプレイ同期を可能にする（Genlockデバイスで使用）

### BPLCON1（$DFF102）

両プレイフィールドの水平スクロール値（上記スクロールセクション参照）。

### BPLCON2（$DFF104）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">値</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">15–7</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">未使用</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">6</span><span class="bit-dir">PF2PRI</span><span class="bit-name">0/1</span><span class="bit-func">プレイフィールド2のプレイフィールド1に対する優先度（デュアルプレイフィールドモード）</span></div>
<div class="bit-row"><span class="bit-num">5–3</span><span class="bit-dir">PF2P2–PF2P0</span><span class="bit-name">0–7</span><span class="bit-func">プレイフィールド2対スプライトの優先度</span></div>
<div class="bit-row"><span class="bit-num">2–0</span><span class="bit-dir">PF1P2–PF1P0</span><span class="bit-name">0–7</span><span class="bit-func">プレイフィールド1対スプライトの優先度</span></div>
</div>

PFxP値はどのスプライトが各プレイフィールドの前面または背面に表示されるかを決定する。スプライト自身の優先度（スプライトリスト内の位置で設定）と組み合わせることで、Amigaで知られるレイヤードディスプレイ合成が実現する。

<!-- section: $DFF100 "インターレースモード" -->

インターレースモードは各フレームで交互にラインのセットを表示することで垂直解像度を倍増させる。解像度が倍増するにもかかわらず、必要なプログラミング変更は最小限である：

### セットアップ

1. BPLCON0の**LACE**ビット（ビット2）をセット
2. **モジュロ**値を1ラインの幅（バイト単位）に設定――各フレームが1ライン置きにスキップするようにする
3. **2つのCopperリスト**を作成：奇数ライン用（ロングフレーム）と偶数ライン用（ショートフレーム）
4. 各リストはBPLxPTを適切な開始ラインに初期化し、リストの最後でもう一方のリストのアドレスをCOP1LCにロード

```asm
Copper1:                              ; ロングフレーム（奇数ライン）
    MOVE #line1_hi,BPL1PTH
    MOVE #line1_lo,BPL1PTL
    ... 表示命令 ...
    MOVE #Copper2_hi,COP1LCH         ; 次フレームでCopper2に切替
    MOVE #Copper2_lo,COP1LCL
    WAIT ($FF,$FE)

Copper2:                              ; ショートフレーム（偶数ライン）
    MOVE #line2_hi,BPL1PTH
    MOVE #line2_lo,BPL1PTL
    ... 表示命令 ...
    MOVE #Copper1_hi,COP1LCH         ; Copper1に戻す
    MOVE #Copper1_lo,COP1LCL
    WAIT ($FF,$FE)
```

### 初期化シーケンス

1. COP1LCをCopper1を指すように設定
2. VPOS（$DFF02A）の**LOF**ビット（ビット15）を0にクリアし、最初に表示されるフレームがロングフレームであることを保証
3. BPLCON0のLACEビットを有効化
4. ライン0を待つ（次のフレームの開始）
5. Copper DMAを有効化

### フリッカーの低減

インターレースディスプレイは各ラインが毎秒25回（1フレームおき）しかリフレッシュされないため、目に見えるフリッカーが発生する。フリッカーは高コントラストの水平エッジで最も目立つ。最小化するには、低コントラストのカラーペアを使用し、1ピクセル高の水平ラインを避ける。

### ディスプレイの有効化

すべてのレジスタを設定した後、必要なDMAチャネルを有効にする：

```asm
    MOVE.W #$8380,$DFF096    ; DMAEN + BPLEN + COPENをセット
```

これによりマスタDMAイネーブル、ビットプレーンDMA、Copper DMAが1回の書き込みで同時にアクティブになる。
