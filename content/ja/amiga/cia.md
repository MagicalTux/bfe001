---
title: "CIA 8520"
addr: "$BFE001"
description: "2つのMOS 8520 Complex Interface Adapter — パラレルポート、タイマー、イベントカウンタ、シリアルシフトレジスタ、割り込み制御。"
order: 2
---

<!-- section: $BFE001 "CIAの概要" -->

Amigaは、カスタムチップDMAの速度を必要としない周辺I/O用に、2つの**MOS 8520** Complex Interface Adapter（CIA）を使用する。8520はCommodore 64で知られる6526の派生型で、レジスタ8〜11のみが異なる（6526のTOD時計がイベントカウンタに置き換えられている）。

各8520は以下を提供する：

- 2つの8ビットプログラマブルパラレルポート（PAとPB）
- 2つの16ビットカウントダウンタイマー（タイマーAとタイマーB）
- アラーム機能付き24ビットイベントカウンタ
- 双方向シリアルシフトレジスタ
- 5つの割り込みソースを持つ割り込み制御レジスタ（ICR）

8520は8ビットの6502ファミリプロセッサ用に設計され、68000とは同期的に通信する。クロックは68000のEクロック、約716kHz。

### 2つのCIA

<div class="register-block">
<div class="reg-title">CIAベースアドレス</div>
<div class="reg-row"><span class="reg-field"><span class="highlight">$BFE001</span></span><span class="reg-val">CIA-A — データバスD0〜D7に接続（奇数アドレス、256バイト間隔）</span></div>
<div class="reg-row"><span class="reg-field">$BFD000</span><span class="reg-val">CIA-B — データバスD8〜D15に接続（偶数アドレス、256バイト間隔）</span></div>
</div>

アドレス<span class="highlight">$BFE001</span>――CIA-Aのベース――が本サイトの名前の由来である。キーボード、ディスク状態、ジョイスティックのファイアボタン、電源LEDの状態を確認するためにシステムが最初に読むアドレスである。Amigaプログラマにとって、このアドレスは即座に認識できる存在だ。

CIA-Aはデータバスの下位バイト（D0〜D7）に接続されるため、すべてのレジスタは奇数アドレスに現れる。CIA-Bは上位バイト（D8〜D15）に接続され、レジスタは偶数アドレスに現れる。チップセレクト信号はアドレスラインA12（CIA-A、アクティブロー）とA13（CIA-B、アクティブロー）から導出され、レジスタはアドレスラインA8〜A11をチップ内部のレジスタセレクト（チップ上のA0〜A3）として使用し、256バイト（$100）間隔で配置される。

### 割り込みルーティング

- **CIA-A**のIRQはPAULAの**INT2**（PORTS、レベル2割り込み）に接続
- **CIA-B**のIRQはPAULAの**INT6**（EXTER、レベル6割り込み）に接続

<!-- section: $BFE001 "レジスタマップ" -->

各CIAは16のレジスタを持つ（レジスタ11/$Bは未使用）。256バイト間隔のため、完全なレジスタマップは広いアドレス範囲にまたがる。

<div class="register-block">
<div class="reg-title">CIA-Aレジスタアドレス</div>
<div class="reg-row"><span class="reg-field"><span class="highlight">$BFE001</span></span><span class="reg-val">PRA — ポートAデータレジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFE101</span><span class="reg-val">PRB — ポートBデータレジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFE201</span><span class="reg-val">DDRA — ポートAデータ方向レジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFE301</span><span class="reg-val">DDRB — ポートBデータ方向レジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFE401</span><span class="reg-val">TALO — タイマーA下位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFE501</span><span class="reg-val">TAHI — タイマーA上位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFE601</span><span class="reg-val">TBLO — タイマーB下位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFE701</span><span class="reg-val">TBHI — タイマーB上位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFE801</span><span class="reg-val">EVENT LO — イベントカウンタ ビット0〜7</span></div>
<div class="reg-row"><span class="reg-field">$BFE901</span><span class="reg-val">EVENT MID — イベントカウンタ ビット8〜15</span></div>
<div class="reg-row"><span class="reg-field">$BFEA01</span><span class="reg-val">EVENT HI — イベントカウンタ ビット16〜23</span></div>
<div class="reg-row"><span class="reg-field">$BFEB01</span><span class="reg-val">—（未使用）</span></div>
<div class="reg-row"><span class="reg-field">$BFEC01</span><span class="reg-val">SP — シリアルデータレジスタ（キーボード）</span></div>
<div class="reg-row"><span class="reg-field">$BFED01</span><span class="reg-val">ICR — 割り込み制御レジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFEE01</span><span class="reg-val">CRA — 制御レジスタA</span></div>
<div class="reg-row"><span class="reg-field">$BFEF01</span><span class="reg-val">CRB — 制御レジスタB</span></div>
</div>

<div class="register-block">
<div class="reg-title">CIA-Bレジスタアドレス</div>
<div class="reg-row"><span class="reg-field">$BFD000</span><span class="reg-val">PRA — ポートAデータレジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFD100</span><span class="reg-val">PRB — ポートBデータレジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFD200</span><span class="reg-val">DDRA — ポートAデータ方向レジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFD300</span><span class="reg-val">DDRB — ポートBデータ方向レジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFD400</span><span class="reg-val">TALO — タイマーA下位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFD500</span><span class="reg-val">TAHI — タイマーA上位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFD600</span><span class="reg-val">TBLO — タイマーB下位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFD700</span><span class="reg-val">TBHI — タイマーB上位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFD800</span><span class="reg-val">EVENT LO — イベントカウンタ ビット0〜7</span></div>
<div class="reg-row"><span class="reg-field">$BFD900</span><span class="reg-val">EVENT MID — イベントカウンタ ビット8〜15</span></div>
<div class="reg-row"><span class="reg-field">$BFDA00</span><span class="reg-val">EVENT HI — イベントカウンタ ビット16〜23</span></div>
<div class="reg-row"><span class="reg-field">$BFDB00</span><span class="reg-val">—（未使用）</span></div>
<div class="reg-row"><span class="reg-field">$BFDC00</span><span class="reg-val">SP — シリアルデータレジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFDD00</span><span class="reg-val">ICR — 割り込み制御レジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFDE00</span><span class="reg-val">CRA — 制御レジスタA</span></div>
<div class="reg-row"><span class="reg-field">$BFDF00</span><span class="reg-val">CRB — 制御レジスタB</span></div>
</div>

<!-- section: $BFE001 "パラレルポート" -->

各CIAは2つの8ビット双方向パラレルポート（PAとPB）を持つ。各ビットの方向は対応するデータ方向レジスタ（DDR）で制御される：0ビットはピンを**入力**に、1ビットは**出力**に設定する。ポートレジスタの読み取りは、方向設定に関係なく常にピンの実際の状態を返す。

### CIA-A ポートA（<span class="highlight">$BFE001</span>）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">方向</span>
<span class="bit-name">信号</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">IN</span><span class="bit-name">/FIR1</span><span class="bit-func">ジョイスティックポート1、ファイアボタン（アクティブロー）</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">IN</span><span class="bit-name">/FIR0</span><span class="bit-func">ジョイスティックポート0、ファイアボタン（アクティブロー）</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">IN</span><span class="bit-name">/RDY</span><span class="bit-func">ディスクドライブレディ（アクティブロー）</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">IN</span><span class="bit-name">/TK0</span><span class="bit-func">ディスクヘッドがトラック0（アクティブロー）</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">IN</span><span class="bit-name">/WPRO</span><span class="bit-func">ディスク書き込み保護（アクティブロー）</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">IN</span><span class="bit-name">/CHNG</span><span class="bit-func">ディスク交換（アクティブロー）</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">1</span><span class="bit-dir">OUT</span><span class="bit-name">/LED</span><span class="bit-func">電源LED制御（0 = LED点灯）</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">OUT</span><span class="bit-name">/OVL</span><span class="bit-func">メモリオーバーレイ（セット時ROMが$000000にマップ）</span></div>
</div>

DDRAのデフォルトは$03（ビット0〜1が出力、ビット2〜7が入力）。/OVLビットはKickstart ROMがアドレス$000000にマップされるか（オーバーレイアクティブ）を制御し、ブートシーケンスで重要な役割を果たす。/LEDビットは電源LEDを直接制御する――おなじみのディスクアクティビティの点滅は、単にソフトウェアがこのビットをトグルしているだけである。

### CIA-A ポートB（$BFE101）

CIA-AのポートBは**パラレル（Centronics）ポート**に接続されている。8ビットすべてがプリンタ通信用のデータを伝送する。ハンドシェイク信号PC（/DRDY — データレディ）とFLAG（/ACK — アクノリッジ）が転送を調整する。

### CIA-B ポートA（$BFD000）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">方向</span>
<span class="bit-name">信号</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">OUT</span><span class="bit-name">/DTR</span><span class="bit-func">シリアルポートDTR出力</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">OUT</span><span class="bit-name">/RTS</span><span class="bit-func">シリアルポートRTS出力</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">IN</span><span class="bit-name">/CD</span><span class="bit-func">シリアルポート キャリア検出</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">IN</span><span class="bit-name">/CTS</span><span class="bit-func">シリアルポート 送信許可</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">IN</span><span class="bit-name">/DSR</span><span class="bit-func">シリアルポート データセットレディ</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">OUT</span><span class="bit-name">SEL</span><span class="bit-func">パラレルポートSELECT</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">OUT</span><span class="bit-name">POUT</span><span class="bit-func">パラレルポート 用紙切れ</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">IN</span><span class="bit-name">BUSY</span><span class="bit-func">パラレルポート ビジー</span></div>
</div>

DDRAデフォルト：$C0（ビット6〜7が出力、残りは入力）。

### CIA-B ポートB（$BFD100）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">方向</span>
<span class="bit-name">信号</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">OUT</span><span class="bit-name">/MTR</span><span class="bit-func">ディスクドライブモーター制御</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">OUT</span><span class="bit-name">/SEL3</span><span class="bit-func">ドライブDF3:選択</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">OUT</span><span class="bit-name">/SEL2</span><span class="bit-func">ドライブDF2:選択</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">OUT</span><span class="bit-name">/SEL1</span><span class="bit-func">ドライブDF1:選択</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">OUT</span><span class="bit-name">/SEL0</span><span class="bit-func">内蔵ドライブDF0:選択</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">OUT</span><span class="bit-name">/SIDE</span><span class="bit-func">ディスクサイド選択（0 = 上面、1 = 下面）</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">OUT</span><span class="bit-name">DIR</span><span class="bit-func">ディスクヘッドステップ方向（0 = 外側、1 = 内側）</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">OUT</span><span class="bit-name">/STEP</span><span class="bit-func">ディスクヘッドステップパルス（アクティブロー）</span></div>
</div>

DDRBデフォルト：$FF（すべて出力）。このポートがフロッピーディスクドライブ機構全体を制御する。

### ハンドシェイク（PCとFLAG）

ポートBのデータ転送は2つのハンドシェイク信号で調整できる。**PC**（ポートコントロール）ピンは、ポートBへのアクセスから3クロックサイクル後にローになり、データが書き込まれたことを通知する。**FLAG**ピンは入力がハイからローに遷移すると発火し、ICRのFLAGビットをセットする。一方のCIAのPCをもう一方のFLAGに接続すると、効率的なハードウェアハンドシェイクが実現する。

CIA-A: SP = KDAT（キーボードシリアルデータ）、CNT = KCLK（キーボードクロック）、PC = /DRDY、FLAG = /ACK。
CIA-B: FLAG = /INDEX（ディスクインデックスパルス）、PCは未使用。

<!-- section: $BFE001 "タイマー" -->

各CIAは2つの独立した16ビットカウントダウンタイマーを持つ。タイマーはプリセット値からゼロまでカウントダウンし、ゼロに達するとICRにフラグをセットし、オプションで割り込みを生成する。

### タイマーレジスタ

各タイマーは2つの8ビットレジスタを使用する。**読み取り**時は現在のカウンタ値を返す。**書き込み**時は値がラッチ（プリスケーラ）に格納され、カウンタには即座にロードされない。

<div class="register-block">
<div class="reg-title">タイマーレジスタアドレス（CIA-A）</div>
<div class="reg-row"><span class="reg-field">$BFE401</span><span class="reg-val">TALO — タイマーA 下位バイト（読み取り: カウンタ、書き込み: ラッチ）</span></div>
<div class="reg-row"><span class="reg-field">$BFE501</span><span class="reg-val">TAHI — タイマーA 上位バイト（読み取り: カウンタ、書き込み: ラッチ）</span></div>
<div class="reg-row"><span class="reg-field">$BFE601</span><span class="reg-val">TBLO — タイマーB 下位バイト（読み取り: カウンタ、書き込み: ラッチ）</span></div>
<div class="reg-row"><span class="reg-field">$BFE701</span><span class="reg-val">TBHI — タイマーB 上位バイト（読み取り: カウンタ、書き込み: ラッチ）</span></div>
</div>

### 安全なカウンタ読み取り

タイマーは非同期にデクリメントされるため、上位バイトと下位バイトを別々に読むとレースコンディションが発生する可能性がある。例えば、カウンタが$0100の場合、上位バイトの読み取りは$01を返すが、下位バイトの読み取り前にカウンタがデクリメントされると、下位バイトは$FFを返し、不正な値$01FFが得られる。安全な方法は：上位を読み、下位を読み、再度上位を読む。上位バイトが変化していれば、プロセスを繰り返す。

### クロックソース

**タイマーA**はCRAのINMODEビットで制御される2つの入力モードを持つ：

- INMODE = 0：各クロックサイクルでデクリメント（Eクロックから約716kHz）
- INMODE = 1：CNT信号の各正エッジでデクリメント

**タイマーB**はCRBのビット6〜5で制御される4つの入力モードを持つ：

- 00：クロックサイクル（約716kHz）
- 01：CNT信号の正エッジ
- 10：タイマーAのアンダーフロー（A+Bを32ビットタイマーとして連結）
- 11：CNTハイでゲートされたタイマーAアンダーフロー（CNTパルス幅の測定）

### 動作モード

**RUNMODE**ビットで選択する：

- **連続モード**（RUNMODE = 0）：ゼロに達した後、ラッチから再ロードしてカウントを継続
- **ワンショットモード**（RUNMODE = 1）：ゼロに達した後、カウンタが停止しSTARTビットがクリアされる

### カウンタのロード

ラッチ値は3つの方法でカウンタに転送される：

1. **強制ロード：** 制御レジスタのLOADビット（ストローブ）をセットすると、即座にラッチ値がロードされる
2. **アンダーフロー：** カウンタがゼロに達すると自動的にラッチが再ロードされる
3. **停止時の自動ロード：** タイマーが停止中にタイマー上位バイトに書き込むと自動ロードがトリガーされる

タイマー値を書き込む際は、意図しない自動ロードを避けるため、常に**上位バイトを先に**、次に**下位バイト**を書き込むこと。

### ポートBへのタイマー出力

各タイマーはオプションでポートBのピンに出力可能：タイマーAはPB6へ、タイマーBはPB7へ。制御レジスタのPBONビットで有効化する。OUTMODEビットで選択：

- **パルスモード**（OUTMODE = 0）：各アンダーフローで1サイクルの正パルス
- **トグルモード**（OUTMODE = 1）：各アンダーフローで出力がトグル

<!-- section: $BFE001 "制御レジスタ" -->

### 制御レジスタA（CRA、レジスタ$E）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">値</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">TODIN</span><span class="bit-name">0=60Hz, 1=50Hz</span><span class="bit-func">TOD入力周波数（イベントカウンタクロック選択）</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">SPMODE</span><span class="bit-name">0=入力, 1=出力</span><span class="bit-func">シリアルポート方向</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">INMODE</span><span class="bit-name">0=クロック, 1=CNT</span><span class="bit-func">タイマーAクロックソース</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">4</span><span class="bit-dir">LOAD</span><span class="bit-name">ストローブ</span><span class="bit-func">ラッチをカウンタに強制ロード（1を書き込んでトリガー）</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">RUNMODE</span><span class="bit-name">0=連続, 1=ワンショット</span><span class="bit-func">タイマーA動作モード</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">OUTMODE</span><span class="bit-name">0=パルス, 1=トグル</span><span class="bit-func">PB6上のタイマーA出力波形</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">PBON</span><span class="bit-name">0=オフ, 1=オン</span><span class="bit-func">PB6へのタイマーA出力を有効化</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">START</span><span class="bit-name">0=停止, 1=開始</span><span class="bit-func">タイマーAの開始/停止</span></div>
</div>

### 制御レジスタB（CRB、レジスタ$F）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">値</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">ALARM</span><span class="bit-name">0=TOD書込, 1=アラーム書込</span><span class="bit-func">イベントカウンタアクセスモード: カウンタまたはアラームを書き込む</span></div>
<div class="bit-row"><span class="bit-num">6–5</span><span class="bit-dir">INMODE</span><span class="bit-name">00/01/10/11</span><span class="bit-func">タイマーBクロックソース（上記参照）</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">4</span><span class="bit-dir">LOAD</span><span class="bit-name">ストローブ</span><span class="bit-func">ラッチをカウンタに強制ロード（1を書き込んでトリガー）</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">RUNMODE</span><span class="bit-name">0=連続, 1=ワンショット</span><span class="bit-func">タイマーB動作モード</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">OUTMODE</span><span class="bit-name">0=パルス, 1=トグル</span><span class="bit-func">PB7上のタイマーB出力波形</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">PBON</span><span class="bit-name">0=オフ, 1=オン</span><span class="bit-func">PB7へのタイマーB出力を有効化</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">START</span><span class="bit-name">0=停止, 1=開始</span><span class="bit-func">タイマーBの開始/停止</span></div>
</div>

### Amigaでのタイマー使用

- **CIA-A タイマーA**はOSがキーボード通信に常時使用
- **CIA-A タイマーB**は汎用に利用可能
- **CIA-B タイマーA**はシリアルポートのボーレート生成に使用
- **CIA-B タイマーB**はBlitterの同期モードで使用

<!-- section: $BFE001 "イベントカウンタとシリアルポート" -->

### イベントカウンタ（レジスタ$8〜$A）

8520は6526のTOD（Time of Day）時計を単純な24ビットイベントカウンタに置き換えている。0から16,777,215（$FFFFFF）までカウントし、TOD入力信号の各正エッジでインクリメントされる。$FFFFFFに達すると0にラップする。

Amigaでは：
- **CIA-A**のイベントカウンタは電源からの50Hzパルス（商用電源周波数）を受信し、単純な経過時間カウンタとして機能する。
- **CIA-B**のイベントカウンタは15,625Hzの水平同期パルスを受信し、高分解能タイミングソースを提供する。

### 読み書き

カウンタは**MSB**（レジスタ$A）が読まれたときに値をラッチする。その後のレジスタ$9と$8の読み取りは、内部カウンタが動き続ける間、ラッチされた値を返す。**LSB**（レジスタ$8）が読まれるとラッチが解放され、次のスナップショットの準備が整う。

書き込み時は、MSBを書き込むとカウンタが停止する。LSBを書き込むと再開する。常にMSBを先に、次にミドルバイト、最後にLSBを書き込むこと。

### アラーム機能

CRBのビット7（ALARM）をセットすると、レジスタ$8〜$Aへの書き込みがカウンタ値ではなくアラーム値を設定する。カウンタがアラーム値に達するとICRのAlarmビットがセットされ、割り込みが生成される。レジスタ$8〜$Aの読み取りはALARMビットの状態に関係なく常に現在のカウンタ値を返す。

### シリアルシフトレジスタ（レジスタ$C）

シリアルポートはデータレジスタ（SDR）と内部シフトレジスタで構成される。方向はCRAのSPMODEビットで制御される：

**入力モード**（SPMODE = 0）：SPピン上のデータがCNT信号の各正エッジでシフトインされる。8ビット後、シフトレジスタがSDRに転送され、SP割り込みフラグがセットされる。

**出力モード**（SPMODE = 1）：タイマーAがボーレートを制御する。SDRに書き込まれたデータがシフトレジスタに転送され、タイマーA周波数の半分の速度でSPからMSBファーストでクロックアウトされる。CNTピンがクロック信号を出力する。最大出力レートは8520のクロック周波数の4分の1。

CIA-AではSPがキーボードシリアルデータ（KDAT）、CNTがキーボードクロック（KCLK）を伝送する。

<!-- section: $BFE001 "割り込み制御レジスタ" -->

ICR（レジスタ$D）は5つの割り込みソースをデュアルレジスタ構造で管理する：**データレジスタ**（読み取り）と**マスクレジスタ**（書き込み）で、同じアドレスからアクセスする。

### ICRの読み取り（データレジスタ）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">ソース</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">7</span><span class="bit-dir">IR</span><span class="bit-name">—</span><span class="bit-func">割り込み発生（有効なソースが発火）</span></div>
<div class="bit-row"><span class="bit-num">6–5</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">常に0（未使用）</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">FLAG</span><span class="bit-name">FLAGピン</span><span class="bit-func">FLAG入力で負エッジ検出</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">SP</span><span class="bit-name">シリアル</span><span class="bit-func">シフトレジスタフル（入力時）またはエンプティ（出力時）</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">ALARM</span><span class="bit-name">カウンタ</span><span class="bit-func">イベントカウンタがアラーム値に一致</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">TB</span><span class="bit-name">タイマーB</span><span class="bit-func">タイマーBアンダーフロー（ゼロに到達）</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">TA</span><span class="bit-name">タイマーA</span><span class="bit-func">タイマーAアンダーフロー（ゼロに到達）</span></div>
</div>

ICRを読むとデータレジスタの値が返され、IRを含む**すべてのビットがクリア**される。後で値が必要な場合は、読み取り直後にRAMに保存すること。

### ICRへの書き込み（マスクレジスタ）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">値</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">7</span><span class="bit-dir">S/C</span><span class="bit-name">0=クリア, 1=セット</span><span class="bit-func">マスクビットのセット/クリア制御</span></div>
<div class="bit-row"><span class="bit-num">6–5</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">未使用</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">FLAG</span><span class="bit-name">マスク</span><span class="bit-func">FLAG割り込みの有効化/無効化</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">SP</span><span class="bit-name">マスク</span><span class="bit-func">シリアルポート割り込みの有効化/無効化</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">ALARM</span><span class="bit-name">マスク</span><span class="bit-func">アラーム割り込みの有効化/無効化</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">TB</span><span class="bit-name">マスク</span><span class="bit-func">タイマーB割り込みの有効化/無効化</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">TA</span><span class="bit-name">マスク</span><span class="bit-func">タイマーA割り込みの有効化/無効化</span></div>
</div>

マスクレジスタはDMACONと同じSET/CLR機構を使用する。S/C = 1のとき、書き込み値でセットされたビットがマスクで**有効化**される。S/C = 0のとき、**無効化**される。0として書き込まれたビットは影響を受けない。

割り込みは、データレジスタのビットがセットされ**かつ**対応するマスクビットが有効な場合のみ生成される（IRQピンがローになる）。ICRが読まれる（データレジスタがクリアされる）と、IRQラインはハイに戻る。

```asm
    ; CIA-AのタイマーA割り込みを有効化:
    MOVE.B #$81,$BFED01    ; S/C=1, TA=1 → マスクでTAを有効化

    ; 両タイマー割り込みを無効化:
    MOVE.B #$03,$BFED01    ; S/C=0, TA=1, TB=1 → マスクでTAとTBをクリア
```

### CIA割り込みの流れ

CIA割り込みが発生すると、以下のシーケンスで処理される：

1. ソースがCIAのICRデータレジスタの対応ビットをセット
2. 対応するマスクビットがセットされていれば、IRビット（ビット7）がセットされIRQがローになる
3. PAULAがIRQを検出し、対応するINTREQビット（CIA-Aはビット3、CIA-Bはビット13）をセット
4. INTENAが許可していれば、68000が割り込みを受信
5. ハンドラがCIAのICRを読んでソースを特定（CIA側のフラグがクリアされる）
6. ハンドラがPAULAのINTREQビットをクリア
