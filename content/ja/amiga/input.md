---
title: "マウスとジョイスティック"
addr: "$JOY00A"
description: "Amigaゲームポート入力 — マウスの直交復号、ジョイスティックのデジタル読み取り、パドルのアナログ入力、発射ボタンレジスタ。"
order: 11
---

<!-- section: $JOY00A "ゲームポートの概要" -->

Amigaは本体前面または側面に2つの**ゲームポート**（DB-9コネクタ）を備えており、それぞれマウス、デジタルジョイスティック、またはアナログパドルを接続できる。3種類のデバイスはすべて同じ物理コネクタを共有するが、ポート信号の異なるサブセットを使用する。入力ハードウェアはDeniseおよびPaulaカスタムチップに実装されており、ボタン入力はCIA-Aを経由する。

各ゲームポートは4つの方向信号、2つのアナログ入力、および1つのプライマリボタンラインを持つ。方向信号は二重の役割を果たす：マウスの場合は直交符号化パルスを伝送し、ジョイスティックの場合はシンプルなスイッチ接点信号を伝送する。Deniseは両モード用のカウンタとロジックを内蔵しており、**JOYxDAT**レジスタを通じてアクセスできる。

### ポート信号の割り当て

各DB-9コネクタの9ピンは以下のように割り当てられている：

- **ピン1–4:** 4つの方向信号（ジョイスティックでは上・下・左・右、マウスではV・VQ・H・HQ）
- **ピン5:** アナログポットY（POTY）
- **ピン6:** 発射ボタン（押下で直接アクティブ）
- **ピン7:** +5V電源
- **ピン8:** グランド
- **ピン9:** アナログポットX（POTX）

<!-- section: $JOY00A "マウスの直交信号" -->

マウスは光学式エンコーディングシステムを使用して移動を報告する。マウス内部では、ゴムでコーティングされたボールが直交する2本の回転軸を駆動する。各軸の端にはスリット付きディスクがあり、回転に伴って光ビームを断続する。ディスク1枚あたり2つのフォトセンサがスリット半分だけオフセットされて配置され、各軸ごとに直交信号のペアを生成する：

- **H**と**HQ**（水平パルスと水平直交パルス）
- **V**と**VQ**（垂直パルスと垂直直交パルス）

パルス信号と直交パルス信号の位相関係が移動方向を符号化する。マウスが右に動くとHがHQに先行し、左に動くとHQがHに先行する。垂直方向も同じ原理で動作する。

### 信号処理

Deniseは各パルスペアから単純な論理演算で2つの内部信号を導出する：

- **X1** = HQの反転
- **X0** = H XOR HQ（HとHQが異なるとき1になる）

水平信号の真理値表：

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">H</span>
<span class="bit-dir">HQ</span>
<span class="bit-name">X0 (H XOR HQ)</span>
<span class="bit-func">X1 (HQ)</span>
</div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">0</span><span class="bit-name">0</span><span class="bit-func">0</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">1</span><span class="bit-name">1</span><span class="bit-func">1</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">0</span><span class="bit-name">1</span><span class="bit-func">0</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">1</span><span class="bit-name">0</span><span class="bit-func">1</span></div>
</div>

これらの導出信号が内部8ビットカウンタを駆動する。右方向/下方向の移動でインクリメントし、左方向/上方向の移動でデクリメントする。カウンタ値はマウスの現在の相対位置を表す。マウスは1インチあたり約200パルス（1cmあたり約79パルス）を生成する。

<!-- section: $JOY00A "JOYxDATレジスタ" -->

Denise内の2つの読み取り専用レジスタが、各ゲームポートのマウス/ジョイスティックカウンタ値を保持する：

<div class="register-block">
<div class="reg-title">マウス/ジョイスティックデータレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF00A</span><span class="reg-val">JOY0DAT — ゲームポート0データ（読み取り専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF00C</span><span class="reg-val">JOY1DAT — ゲームポート1データ（読み取り専用）</span></div>
</div>

各レジスタは2つの8ビットカウンタを1つの16ビットワードにパックする：

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">15–8</span>
<span class="bit-name">7–0</span>
<span class="bit-func">アクセス</span>
</div>
<div class="bit-row"><span class="bit-num">フィールド</span><span class="bit-dir">Y7–Y0</span><span class="bit-name">X7–X0</span><span class="bit-func">読み取り専用</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">説明</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">15–8</span><span class="bit-dir">Y7–Y0</span><span class="bit-name">垂直移動カウンタ（Y軸）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–0</span><span class="bit-dir">X7–X0</span><span class="bit-name">水平移動カウンタ（X軸）</span><span class="bit-func"></span></div>
</div>

### カウンタオーバーフロー処理

各カウンタは8ビット（0–255）しかないため、マウスを約3–4cm動かすとオーバーフローする。絶対位置を追跡するには、ソフトウェアが頻繁にカウンタをポーリングしてオーバーフローを検出する必要がある。OSは**垂直帰線割り込み**の間にJOYxDATをサンプリングし、2回のサンプル間の移動が127カウンタステップを超えないことを保証する。

移動方向は現在のカウンタ値と前回の値を比較して判定する：

- **差が-127から+127の範囲内:** オーバーフローなし。正の値＝右/下、負の値＝左/上。
- **差が+127を超える:** アンダーフロー発生。実際の移動量 = -(256 - 差分)。
- **差が-127未満:** オーバーフロー発生。実際の移動量 = 256 + 差分。

### JOYTEST — カウンタプリセット

**JOYTEST**レジスタに書き込むことでカウンタをプリセットできる。JOY0DATとJOY1DATの両方が同時に設定される。

<div class="register-block">
<div class="reg-title">カウンタプリセットレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF036</span><span class="reg-val">JOYTEST — マウスカウンタ値の設定（書き込み専用）</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">説明</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">15–10</span><span class="bit-dir">Y7–Y2</span><span class="bit-name">垂直カウンタプリセット（上位6ビット）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9–8</span><span class="bit-dir">—</span><span class="bit-name">未使用</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–2</span><span class="bit-dir">X7–X2</span><span class="bit-name">水平カウンタプリセット（上位6ビット）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1–0</span><span class="bit-dir">—</span><span class="bit-name">未使用</span><span class="bit-func"></span></div>
</div>

各カウンタの上位6ビットのみプリセット可能。各カウンタの下位2ビットはマウス信号線から直接供給されるため、ソフトウェアからは書き込みできない。

<!-- section: $JOY00A "ジョイスティックの読み取り" -->

デジタルジョイスティックはシンプルなスイッチ接点を使用し、マウスの直交信号ラインと同じ信号を生成する。信号がパルスではなく静的であるため、Deniseのカウンタはスイッチ位置を符号化する固定値に落ち着く。ジョイスティックの方向はJOYxDATのビットから直接デコードされる：

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">方向</span>
<span class="bit-dir">条件</span>
<span class="bit-name">JOYxDATビット</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">右</span><span class="bit-dir">X1 = 1</span><span class="bit-name">ビット1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">左</span><span class="bit-dir">Y1 = 1</span><span class="bit-name">ビット9</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">後方（下）</span><span class="bit-dir">X0 XOR X1 = 1</span><span class="bit-name">ビット0 XOR ビット1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">前方（上）</span><span class="bit-dir">Y0 XOR Y1 = 1</span><span class="bit-name">ビット8 XOR ビット9</span><span class="bit-func"></span></div>
</div>

左右はシンプルな1ビットテスト。前方と後方は隣接するビット間のXOR演算が必要。以下はゲームポート1の標準的なジョイスティックテストルーチン：

```asm
TestJoystick:
    move.w $DFF00C,d0          ; JOY1DATをd0に読み込む
    btst   #1,d0               ; ビット1（X1）をテスト
    bne    right               ; セットされていれば右に倒されている
    btst   #9,d0               ; ビット9（Y1）をテスト
    bne    left                ; セットされていれば左に倒されている
    move.w d0,d1               ; d1にコピー
    lsr.w  #1,d1               ; 右シフト：X1がX0の位置に、Y1がY0の位置に
    eor.w  d0,d1               ; XOR：d1.bit0 = X0^X1、d1.bit8 = Y0^Y1
    btst   #0,d1               ; X0 XOR X1をテスト
    bne    backward            ; セットされていれば後方（下）に倒されている
    btst   #8,d1               ; Y0 XOR Y1をテスト
    bne    forward             ; セットされていれば前方（上）に倒されている
    bra    center              ; ジョイスティックは中立位置
```

このルーチンは斜め方向に対応していない。斜めをサポートするには、if-elseチェーンではなく各軸を独立してテストする必要がある。

<!-- section: $JOY00A "パドル/アナログ入力" -->

各ゲームポートはパドルやアナログジョイスティックなどの比例コントローラを接続するための2つのアナログ入力（POTXとPOTY）を提供する。パドルには可変抵抗器（ポテンショメータ）が内蔵されており、その位置が抵抗値を決定する。アナログジョイスティックも同様に、各軸に1つのポテンショメータを持つ。

### アナログ測定の原理

Amigaはコンデンサベースのタイミング方式で抵抗値を測定する。4つのアナログ入力はそれぞれ、入力ピンとグランド間に47 nFのコンデンサが内部接続されている。測定サイクルは以下のように動作する：

1. Paulaが入力をグランドに引くことで4つのコンデンサを急速に放電し、カウンタレジスタをリセットする。
2. コンデンサが外部ポテンショメータを通じて充電を開始する。
3. 充電中、ディスプレイラインごとにカウンタが1ずつインクリメントされる。
4. コンデンサの電圧がしきい値を超えると、対応するカウンタが停止する。

低い抵抗値はコンデンサを素早く充電する（低いカウンタ値）。高い抵抗値はゆっくり充電する（高いカウンタ値）。ポテンショメータの最大抵抗値は470 kオーム（+/-10%）でなければならない。

### POTxDATレジスタ

<div class="register-block">
<div class="reg-title">アナログ入力レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF012</span><span class="reg-val">POT0DAT — ゲームポート0アナログ値（読み取り専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF014</span><span class="reg-val">POT1DAT — ゲームポート1アナログ値（読み取り専用）</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">説明</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">15–8</span><span class="bit-dir">Y7–Y0</span><span class="bit-name">POTYカウンタ値（右パドル/Y軸）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–0</span><span class="bit-dir">X7–X0</span><span class="bit-name">POTXカウンタ値（左パドル/X軸）</span><span class="bit-func"></span></div>
</div>

### POTGO / POTGOR — 制御と読み取り

**POTGO**レジスタはアナログ測定サイクルを制御し、アナログラインを汎用デジタルI/Oとして構成することもできる。

<div class="register-block">
<div class="reg-title">アナログ制御レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF034</span><span class="reg-val">POTGO — アナログポート制御（書き込み専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF016</span><span class="reg-val">POTGOR (POTINP) — アナログポートステータス（読み取り専用）</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">OUTRY</span><span class="bit-name">ゲームポート1 POTYを出力に設定</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">DATRY</span><span class="bit-name">ゲームポート1 POTYデータビット</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">13</span><span class="bit-dir">OUTRX</span><span class="bit-name">ゲームポート1 POTXを出力に設定</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">DATRX</span><span class="bit-name">ゲームポート1 POTXデータビット</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">11</span><span class="bit-dir">OUTLY</span><span class="bit-name">ゲームポート0 POTYを出力に設定</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">DATLY</span><span class="bit-name">ゲームポート0 POTYデータビット</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">9</span><span class="bit-dir">OUTLX</span><span class="bit-name">ゲームポート0 POTXを出力に設定</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">DATLX</span><span class="bit-name">ゲームポート0 POTXデータビット</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–1</span><span class="bit-dir">—</span><span class="bit-name">未使用</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">0</span><span class="bit-dir">START</span><span class="bit-name">コンデンサを放電して測定を開始</span><span class="bit-func"></span></div>
</div>

POTGOへの書き込みは両方のPOTxDATカウンタをリセットする。通常、STARTビットは垂直帰線期間中に1にセットされる。アクティブ表示期間中にコンデンサが充電されカウンタが動作する。次の垂直帰線期間までに、POTxDATレジスタには最終的な測定値が格納される。

OUTxxビットを1にセットすると、対応するラインがデジタル出力モードに切り替わり、コンデンサ測定回路から切断される。その後、POTGOのDATxxビット値がピンに出力される。POTGORのDATxxを読み取ると、モードに関係なく常にピンの現在のロジックレベルが反映される。

**注意:** アナログポートをデジタルI/Oとして使用する場合、ライン上の47 nFのコンデンサにより、各信号遷移後に最大300マイクロ秒の安定化遅延が発生する。

<!-- section: $JOY00A "発射ボタン" -->

各入力デバイスは1つ以上のボタンを持つ。プライマリ発射ボタン（またはマウス左ボタン）はアクティブローで、CIA-Aに直接接続されている。

### ゲームポート0のボタン

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ボタン</span>
<span class="bit-dir">レジスタ</span>
<span class="bit-name">ビット</span>
<span class="bit-func">アクティブ</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">左マウス/発射</span><span class="bit-dir">CIA-A PRA ($BFE001)</span><span class="bit-name">ビット6</span><span class="bit-func">Low（0=押下）</span></div>
<div class="bit-row"><span class="bit-num">右マウス</span><span class="bit-dir">POTGOR ($DFF016)</span><span class="bit-name">DATLY（ビット10）</span><span class="bit-func">Low（0=押下）</span></div>
<div class="bit-row"><span class="bit-num">第3マウスボタン</span><span class="bit-dir">POTGOR ($DFF016)</span><span class="bit-name">DATLX（ビット8）</span><span class="bit-func">Low（0=押下）</span></div>
<div class="bit-row"><span class="bit-num">左パドルボタン</span><span class="bit-dir">JOY0DAT ($DFF00A)</span><span class="bit-name">ビット9</span><span class="bit-func">High（1=押下）</span></div>
<div class="bit-row"><span class="bit-num">右パドルボタン</span><span class="bit-dir">JOY0DAT ($DFF00A)</span><span class="bit-name">ビット1</span><span class="bit-func">High（1=押下）</span></div>
</div>

### ゲームポート1のボタン

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ボタン</span>
<span class="bit-dir">レジスタ</span>
<span class="bit-name">ビット</span>
<span class="bit-func">アクティブ</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">左マウス/発射</span><span class="bit-dir">CIA-A PRA ($BFE001)</span><span class="bit-name">ビット7</span><span class="bit-func">Low（0=押下）</span></div>
<div class="bit-row"><span class="bit-num">右マウス</span><span class="bit-dir">POTGOR ($DFF016)</span><span class="bit-name">DATRY（ビット14）</span><span class="bit-func">Low（0=押下）</span></div>
<div class="bit-row"><span class="bit-num">第3マウスボタン</span><span class="bit-dir">POTGOR ($DFF016)</span><span class="bit-name">DATRX（ビット12）</span><span class="bit-func">Low（0=押下）</span></div>
<div class="bit-row"><span class="bit-num">左パドルボタン</span><span class="bit-dir">JOY1DAT ($DFF00C)</span><span class="bit-name">ビット9</span><span class="bit-func">High（1=押下）</span></div>
<div class="bit-row"><span class="bit-num">右パドルボタン</span><span class="bit-dir">JOY1DAT ($DFF00C)</span><span class="bit-name">ビット1</span><span class="bit-func">High（1=押下）</span></div>
</div>

特に指定がない限り、ボタンは**アクティブロー**（0=押下）である。例外はパドルボタンで、これらはアクティブハイ（1=押下）。ポート1（標準ジョイスティックポート）の発射ボタンを読み取るには：

```asm
    btst #7,$BFE001        ; CIA-A PRA ビット7をテスト
    beq  fire_pressed      ; ゼロならボタン押下
```

右マウスボタンと第3マウスボタンはPOTGORレジスタを通じて読み取る。使用するには、POTGOの対応するOUTxxビットがクリアされている（入力モード）必要があるが、これはデフォルト状態である。
