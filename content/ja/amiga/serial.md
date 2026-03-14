---
title: "シリアルポート"
addr: "$DFF030"
description: "Amigaシリアルポート — UART動作、RS232通信、ボーレート設定、Paulaの送受信レジスタ。"
order: 12
---

<!-- section: $DFF030 "シリアルポートの概要" -->

Amigaは、Paulaカスタムチップに内蔵された**UART**（Universal Asynchronous Receiver/Transmitter）によって管理される標準RS232シリアルポートを搭載している。このポートは全二重通信をサポートし、別々の信号線でデータの送信と受信を同時に行える。

RS232コネクタは2種類の信号を持つ：

1. **データ信号** — TXD（送信データ）とRXD（受信データ）。実際のシリアルビットストリームを伝送する。
2. **ハンドシェイク信号** — CTS、RTS、DSR、DTRなど。CIAチップを通じて管理され、CIAのドキュメントで解説済み。

すべてのシリアルデータ転送はTXDとRXDラインで行われる。2台のデバイスを接続する際は、一方のTXDを他方のRXDに、またその逆に配線する（ヌルモデム/クロス配線）。

### RS232データフォーマット

各方向のデータは1本のワイヤだけで伝送されるため、バイトをビット単位でシリアル化する必要がある。RS232には独立したクロック信号がないため、送信側と受信側はビットタイミングについて合意しなければならない。これが**ボーレート**であり、1秒あたりの転送ビット数を規定する。一般的なボーレートは300、1200、2400、4800、9600である。

各バイトは以下のようにフレーミングされる：

1. **スタートビット** — アイドル状態（ハイ）からローへの遷移で、バイトの開始を示す。
2. **データビット** — LSBから順に送信。8ビットまたは9ビットフォーマット。
3. **ストップビット** — バイトの終了を示す1つまたは2つのハイビット。

受信側は、前のバイトのストップビットと新しいバイトのスタートビットの間のハイからローへの遷移によって各バイトの開始を検出する。

<!-- section: $DFF030 "UARTレジスタ" -->

PaulaのUARTはシリアルデータ転送に3つのレジスタを使用し、さらにADKCONレジスタの1ビットをブレーク制御に使用する。

<div class="register-block">
<div class="reg-title">シリアルポートレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF030</span><span class="reg-val">SERDAT — シリアル送信データ（書き込み専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF018</span><span class="reg-val">SERDATR — シリアル受信データ + ステータス（読み取り専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF032</span><span class="reg-val">SERPER — シリアル周期/ボーレート（書き込み専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF09E</span><span class="reg-val">ADKCON — ビット11（UARTBRK）：TXDをローに強制（書き込み専用）</span></div>
</div>

### SERDATR — 受信データとステータス（$DFF018、読み取り）

このレジスタは受信データバイトとUARTのステータスフラグの両方を含む。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">OVRUN</span><span class="bit-name">オーバーラン — バッファ読み取り前にシフトレジスタが満杯</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">14</span><span class="bit-dir">RBF</span><span class="bit-name">受信バッファフル — データ読み取り可能</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">13</span><span class="bit-dir">TBE</span><span class="bit-name">送信バッファエンプティ — 次のバイト送信可能</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">TSRE</span><span class="bit-name">送信シフトレジスタエンプティ — 送信完了</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11</span><span class="bit-dir">RXD</span><span class="bit-name">RXD信号ラインの現在のレベル</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">—</span><span class="bit-name">未使用</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">STP</span><span class="bit-name">ストップビット</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">STP/DB8</span><span class="bit-name">ストップビット（8ビットモード）またはデータビット8（9ビットモード）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">DB7</span><span class="bit-name">受信データビット7</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">DB6</span><span class="bit-name">受信データビット6</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">DB5</span><span class="bit-name">受信データビット5</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">DB4</span><span class="bit-name">受信データビット4</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">DB3</span><span class="bit-name">受信データビット3</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">DB2</span><span class="bit-name">受信データビット2</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">DB1</span><span class="bit-name">受信データビット1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">DB0</span><span class="bit-name">受信データビット0</span><span class="bit-func"></span></div>
</div>

### SERPER — 周期レジスタ（$DFF032、書き込み）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">LONG</span><span class="bit-name">1にセットで9ビット受信モード</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">14–0</span><span class="bit-dir">RATE</span><span class="bit-name">ボーレートを決定する15ビット周期値</span><span class="bit-func"></span></div>
</div>

<!-- section: $DFF030 "データ受信" -->

シリアル受信はシフトレジスタとデータバッファの2段階で行われる。

### 第1段階：シフトレジスタ

RXDピンに到着するビットは、SERPERで設定されたレートでシフトレジスタにクロックインされる。スタートビットがプロセスを開始する：UARTは各ビット期間の中央でRXDをサンプリングし、パラレルデータワードを再構成する。

### 第2段階：データバッファ

シフトレジスタが満杯になると（すべてのデータビットとストップビットを受信）、その内容が受信バッファ（SERDATRのデータビット）に転送される。シフトレジスタは直ちに次のバイトの受信を開始できる。

### データフォーマット

データ幅はSERPERのLONGビットに依存する：

- **LONG = 0（8ビットモード）:** SERDATRのビット7–0に8ビットのデータ。ビット8にストップビット。ビット9に2番目のストップビット（存在する場合）。
- **LONG = 1（9ビットモード）:** ビット8–0に9ビットのデータ。ビット9にストップビット。

### ステータスフラグ

**RBF**（受信バッファフル）は、完全なデータワードがシフトレジスタからバッファに転送されると1にセットされる。このフラグはINTREQ/INTENのビット11にも存在し、割り込みを生成する。データ読み取り後、ソフトウェアはINTREQに書き込んでRBFをクリアする必要がある：

```asm
    move.w #$0800,$DFF09C      ; INTREQ内のRBFをクリア（SERDATRのRBFもクリアされる）
```

### オーバーラン検出

受信バッファが読み取られる前にシフトレジスタが再び満杯になると、**オーバーラン**が発生する。SERDATRのOVRUNビットが1にセットされ、データが失われたことを示す。最終的にRBFがクリアされると、シフトレジスタの内容がバッファに転送されOVRUNがリセットされる。バッファにシフトレジスタからの（遅延した）データが格納されるため、RBFは直ちに再び1になる。

オーバーランを避けるには、SERDATRの読み取りとRBFのクリアを迅速に行うこと。理想的にはRBF割り込みハンドラ内で処理する。

<!-- section: $DFF030 "データ送信" -->

シリアル送信もデータバッファ（SERDAT）と出力シフトレジスタの2段パイプラインを使用する。

### データの書き込み

SERDATの送信データフォーマットは、希望するワード長とストップビット数に依存する。データビットはレジスタの下位部分を占め、その直後に1つまたは2つのストップビット（1にセット）が続く。上位ビットはすべて0でなければならない。

**8ビットデータ、2ストップビット：**

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">15–10</span>
<span class="bit-name">9–8</span>
<span class="bit-func">7–0</span>
</div>
<div class="bit-row"><span class="bit-num">値</span><span class="bit-dir">0 0 0 0 0 0</span><span class="bit-name">1 1（ストップビット）</span><span class="bit-func">D7–D0（データ）</span></div>
</div>

**8ビットデータ、1ストップビット：**

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">15–9</span>
<span class="bit-name">8</span>
<span class="bit-func">7–0</span>
</div>
<div class="bit-row"><span class="bit-num">値</span><span class="bit-dir">0 0 0 0 0 0 0</span><span class="bit-name">1（ストップビット）</span><span class="bit-func">D7–D0（データ）</span></div>
</div>

**9ビットデータ、1ストップビット：**

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">15–10</span>
<span class="bit-name">9</span>
<span class="bit-func">8–0</span>
</div>
<div class="bit-row"><span class="bit-num">値</span><span class="bit-dir">0 0 0 0 0 0</span><span class="bit-name">1（ストップビット）</span><span class="bit-func">D8–D0（データ）</span></div>
</div>

SERPERのLONGビットは受信にのみ影響する。送信フォーマットはSERDATに書き込まれた値のみで決定される。

### 送信ステータスフラグ

**TBE**（送信バッファエンプティ）は、SERDAT内のデータが出力シフトレジスタに転送されると1にセットされる。バッファは次のワードを受け付ける準備ができる。TBEはINTREQ/INTENのビット0でもある。RBFと同様に、サービス後はINTREQ経由でクリアする必要がある。

**TSRE**（送信シフトレジスタエンプティ）は、シフトレジスタがすべてのビットの送信を完了し、バッファに新しいデータが待機していないと1にセットされる。これは送信が本当に完了したことを示す。TSREはTBEがクリアされるとリセットされる。

### UARTBRK — ブレーク信号

ADKCONレジスタのビット11（UARTBRK）をセットすると、TXD出力ラインがローに強制され、シリアル送信が停止する。これはRS232ライン上に「ブレーク」状態を生成し、リモートデバイスが検出できる。ビットをクリアすると通常動作に復帰する。

<div class="register-block">
<div class="reg-title">ADKCONシリアルビット</div>
<div class="reg-row"><span class="reg-field">$DFF09E</span><span class="reg-val">ADKCONビット11 — UARTBRK：<span class="highlight">セットでTXDをローに強制（ブレーク状態）</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF010</span><span class="reg-val">ADKCONRビット11 — UARTBRKの状態を読み取り</span></div>
</div>

<!-- section: $DFF030 "ボーレート計算" -->

SERPERの下位15ビット（ビット14–0、RATEフィールド）は、ビット周期をバスクロックサイクル数で指定する。1バスクロックサイクルは約**279.365ナノ秒**（PALシステムクロック3.546895 MHzに基づく）。

希望するボーレートをSERPER値に変換する公式：

<div class="mem-block">
SERPER = (1 / (baud_rate * 279.365 * 10^-9)) - 1
</div>

同等の式：

<div class="mem-block">
SERPER = (3,579,545 / baud_rate) - 1     （NTSCクロック）
SERPER = (3,546,895 / baud_rate) - 1     （PALクロック）
</div>

### 一般的なボーレート

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ボーレート</span>
<span class="bit-dir">SERPER値（PAL）</span>
<span class="bit-name">SERPER値（NTSC）</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">300</span><span class="bit-dir">11822</span><span class="bit-name">11931</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1200</span><span class="bit-dir">2955</span><span class="bit-name">2982</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">2400</span><span class="bit-dir">1477</span><span class="bit-name">1491</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">4800</span><span class="bit-dir">738</span><span class="bit-name">745</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9600</span><span class="bit-dir">369</span><span class="bit-name">372</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">19200</span><span class="bit-dir">184</span><span class="bit-name">186</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">31250（MIDI）</span><span class="bit-dir">112</span><span class="bit-name">113</span><span class="bit-func"></span></div>
</div>

計算値は最も近い整数に丸めてSERPERに書き込む：

```asm
    move.w #745,$DFF032        ; 4800ボーに設定（NTSC）、LONG=0（8ビット）
```

9ビット受信モードの場合はビット15をセット：

```asm
    move.w #$8000+745,$DFF032  ; 4800ボー、LONG=1（9ビット受信）
```

<!-- section: $DFF030 "割り込みとプログラミング" -->

UARTは2つの割り込み信号を生成し、両方ともINTREQ/INTENで管理される：

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">INTREQビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">条件</span>
<span class="bit-func">レベル</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">0</span><span class="bit-dir">TBE</span><span class="bit-name">送信バッファエンプティ — 次のワード送信可能</span><span class="bit-func">1</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">11</span><span class="bit-dir">RBF</span><span class="bit-name">受信バッファフル — データ利用可能</span><span class="bit-func">5</span></div>
</div>

### 典型的な受信ハンドラ

```asm
SerialReceiveISR:
    move.w $DFF018,d0          ; SERDATRを読み取る
    btst   #15,d0              ; OVRUNをチェック
    bne    .overrun            ; オーバーラン状態を処理
    and.w  #$00FF,d0           ; 8データビットを抽出
    ; ... d0の受信バイトを処理 ...
    move.w #$0800,$DFF09C      ; INTREQ内のRBFをクリア
    rte

.overrun:
    move.w #$0800,$DFF09C      ; RBFをクリアして復旧
    ; ... オーバーランエラーを通知 ...
    rte
```

### 典型的な送信シーケンス

```asm
SendByte:
    ; d0.b = 送信するバイト
    and.w  #$00FF,d0           ; 8ビットにマスク
    or.w   #$0100,d0           ; 1ストップビットを追加（ビット8 = 1）
    move.w d0,$DFF030          ; SERDATに書き込む
    ; 次のバイト送信可能時にTBE割り込みが発生する
    rts
```

PaulaのUARTはスタートビットの挿入とビットのシリアル化を自動的に処理する。ソフトウェアはデータワード（ストップビット付き）をSERDATに書き込み、次のバイトを送信する前にTBEを待つだけでよい。
