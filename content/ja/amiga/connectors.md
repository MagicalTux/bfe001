---
title: "コネクタ"
addr: "$CONN01"
description: "Amigaの外部コネクタ — オーディオ/ビデオ、RGB、パラレル、シリアル、フロッピー、ゲームポート、拡張ポート、電源のピン配置。"
order: 15
---

<!-- section: $CONN01 "オーディオ & ビデオ" -->

オーディオ/ビデオコネクタの構成はAmigaのモデルによって異なる。A500とA1000はRCA（CINCH）コネクタでコンポジットビデオを提供するが、A2000にはコンポジットビデオ出力がなく、オプションのビデオモジュレータまたはGenlockアダプタ用のヘッダのみがある。

### コンポジットビデオ

A1000のコンポジットビデオ信号は完全な**FBAS**（カラーコンポジット）信号であり、大半のモニタと互換性がある。A500ではコスト削減のため、より単純な**BAS**信号（モノクロコンポジットのみ）となっている。フランス語キーボードを持たない初期のA1000モデルはPALではなくNTSCを出力するため、PALモニタでは白黒に干渉線が入った映像となる。

ビデオ出力はトランジスタバッファを通して**75オームの出力インピーダンス**で出力され、永続的な短絡保護を提供する。

### ステレオオーディオ

オーディオはすべてのモデルで2つのRCAコネクタで出力される：

<div class="register-block">
<div class="reg-title">オーディオRCAコネクタ</div>
<div class="reg-row"><span class="reg-field">赤</span><span class="reg-val">右ステレオチャンネル</span></div>
<div class="reg-row"><span class="reg-field">白</span><span class="reg-val">左ステレオチャンネル</span></div>
</div>

出力インピーダンスは**1 Kohm**で、標準的なアンプ（AUX、TAPE、CD入力）に接続可能。短絡保護のため内部に36オームの負荷抵抗を備えている。

### A1000 TVモジュレータコネクタ

A1000には追加の**8ピンDIN**オーディオ/ビデオコネクタがある（C64のモジュレータコネクタと同一）。テレビ接続用のRFモジュレータ向けで、ビデオとオーディオの両方に加えてモジュレータ用の+12V電源も提供する。このコネクタのオーディオピンは1 Kohmの出力インピーダンスだが内部負荷抵抗がないため、RCA出力と比較して約4倍の無負荷信号レベルとなる。

<div class="register-block">
<div class="reg-title">A1000 DIN-8 TVモジュレータ ピン配置</div>
<div class="reg-row"><span class="reg-field">ピン1</span><span class="reg-val">— （未使用）</span></div>
<div class="reg-row"><span class="reg-field">ピン2</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">ピン3</span><span class="reg-val">左オーディオチャンネル</span></div>
<div class="reg-row"><span class="reg-field">ピン4</span><span class="reg-val">コンポジットビデオ出力</span></div>
<div class="reg-row"><span class="reg-field">ピン5</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">ピン6</span><span class="reg-val">— （未使用）</span></div>
<div class="reg-row"><span class="reg-field">ピン7</span><span class="reg-val">+12V（モジュレータ電源）</span></div>
<div class="reg-row"><span class="reg-field">ピン8</span><span class="reg-val">右オーディオチャンネル</span></div>
</div>

<!-- section: $CONN01 "RGBビデオ" -->

RGBコネクタは**23ピンD-sub**で、3つのAmigaモデルすべてで同一である。アナログRGBモニタ（Amigaモニタなど）、デジタルRGBモニタ、およびGenlockアダプタなどの特殊デバイスをサポートする。

<div class="register-block">
<div class="reg-title">RGBコネクタ ピン配置（23ピンD-sub）</div>
<div class="reg-row"><span class="reg-field">ピン1</span><span class="reg-val">XCLK — 外部クロック入力（XCLKENがLOWのとき有効）</span></div>
<div class="reg-row"><span class="reg-field">ピン2</span><span class="reg-val">XCLKEN — 外部クロックイネーブル（アクティブLOW）</span></div>
<div class="reg-row"><span class="reg-field">ピン3</span><span class="reg-val">R — <span class="highlight">アナログ赤</span>（75オーム、トランジスタバッファ）</span></div>
<div class="reg-row"><span class="reg-field">ピン4</span><span class="reg-val">G — <span class="highlight">アナログ緑</span>（75オーム、トランジスタバッファ）</span></div>
<div class="reg-row"><span class="reg-field">ピン5</span><span class="reg-val">B — <span class="highlight">アナログ青</span>（75オーム、トランジスタバッファ）</span></div>
<div class="reg-row"><span class="reg-field">ピン6</span><span class="reg-val">DI — デジタルインテンシティ</span></div>
<div class="reg-row"><span class="reg-field">ピン7</span><span class="reg-val">DB — デジタル青（TTLレベル、47オーム）</span></div>
<div class="reg-row"><span class="reg-field">ピン8</span><span class="reg-val">DG — デジタル緑（TTLレベル、47オーム）</span></div>
<div class="reg-row"><span class="reg-field">ピン9</span><span class="reg-val">DR — デジタル赤（TTLレベル、47オーム）</span></div>
<div class="reg-row"><span class="reg-field">ピン10</span><span class="reg-val">CSYNC — <span class="highlight">コンポジット同期</span>（75オーム、トランジスタバッファ）</span></div>
<div class="reg-row"><span class="reg-field">ピン11</span><span class="reg-val">HSY — 水平同期（双方向、TTLレベル、47オーム）</span></div>
<div class="reg-row"><span class="reg-field">ピン12</span><span class="reg-val">VSY — 垂直同期（双方向、TTLレベル、47オーム）</span></div>
<div class="reg-row"><span class="reg-field">ピン13</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">ピン14</span><span class="reg-val">ZD — ゼロ検出（Genlock用背景インジケータ）</span></div>
<div class="reg-row"><span class="reg-field">ピン15</span><span class="reg-val">C1U — 3.58 MHzクロック出力</span></div>
<div class="reg-row"><span class="reg-field">ピン16–20</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">ピン21</span><span class="reg-val">-5V</span></div>
<div class="reg-row"><span class="reg-field">ピン22</span><span class="reg-val">+12V</span></div>
<div class="reg-row"><span class="reg-field">ピン23</span><span class="reg-val">+5V</span></div>
</div>

### アナログRGB信号

アナログRGBモニタ（Commodore 1084など）への接続にはピン3（R）、4（G）、5（B）、10（CSYNC）を使用する。Deniseのデジタル RGB出力は**4ビットDAC**を通じてアナログに変換される。Agnusからのコンポジット同期信号は、垂直同期と水平同期のミックスである。4つの出力すべてが**75オームの出力インピーダンス**を持ち、トランジスタバッファで短絡保護されている。

### デジタルRGB信号

ピン6–9は**デジタルRGB信号**をデジタルモニタ向けに提供する。Deniseのデジタル RGB出力から**74HC244**バッファを通して出力される。カラー信号はDeniseの上位ビットに接続されている（例：DBはB3に接続）。4信号すべて**47オームの出力インピーダンス**でTTLレベルである。

### 同期信号

HSYとVSYピンは**双方向**である。通常動作では、Agnusからの水平・垂直同期を出力する（TTLレベル、47オームインピーダンス、Agnusピンに直結）。同期信号は**アクティブLOW** — 通常は5Vで、同期パルス時に0Vとなる。

Agnusの**Genlockビット**が有効になると、これらのピンは入力モードに切り替わる。Amigaは HSYとVSYに供給される外部同期信号にビデオタイミングを同期させ、外部ビデオソースとのGenlockを実現する。

### ゼロ検出（ZD）

ZD信号は、現在表示されているピクセルが背景色（カラーレジスタ0）の場合にLOWとなる。Genlockアダプタが外部ビデオをAmigaの表示の後ろにキーイングするために使用される。垂直帰線期間中（VSY=0）、ZDはレジスタ$100（BPLCON0）からの**GAUDビット**（Genlock Audio Enable）を伝送し、Genlockインターフェースのオーディオスイッチとして使用される。

### 外部クロック

**C1U**ピンはカスタムチップのCLK信号と同一の3.58 MHzクロックを出力する。**XCLK**と**XCLKEN**ピンにより、外部マスタークロックをAmigaに供給可能。Amigaのすべてのクロックは28 MHzの発振器から派生しており、XCLKENをLOWに駆動するとXCLK経由の外部クロックに置き換えられる。ピン13（GND）がクロックのグランド基準となる。

<!-- section: $CONN01 "パラレルポート（Centronics）" -->

Centronicsパラレルポートは**25ピンD-sub**コネクタを使用する。A500とA2000ではPC互換のピン配置であり、標準的なPCパラレルプリンタケーブルがそのまま使用可能。A1000は異なるピン配置（特にピン23が+5Vでありは GNDではない）のため、標準ケーブルを接続するとショートする。A1000にはカスタムケーブルが必要。

<div class="register-block">
<div class="reg-title">Centronicsパラレルポート ピン配置（A500/A2000）</div>
<div class="reg-row"><span class="reg-field">ピン1</span><span class="reg-val">STROBE — データ有効（出力、アクティブLOW、1.4 µsパルス）</span></div>
<div class="reg-row"><span class="reg-field">ピン2</span><span class="reg-val">データビット0（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン3</span><span class="reg-val">データビット1（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン4</span><span class="reg-val">データビット2（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン5</span><span class="reg-val">データビット3（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン6</span><span class="reg-val">データビット4（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン7</span><span class="reg-val">データビット5（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン8</span><span class="reg-val">データビット6（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン9</span><span class="reg-val">データビット7（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン10</span><span class="reg-val">/ACK — プリンタからの確認応答（入力、アクティブLOW、1 µsパルス）</span></div>
<div class="reg-row"><span class="reg-field">ピン11</span><span class="reg-val">BUSY — プリンタビジー（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン12</span><span class="reg-val">POUT — 用紙切れ（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン13</span><span class="reg-val">SELECT — プリンタオンライン（双方向）</span></div>
<div class="reg-row"><span class="reg-field">ピン14</span><span class="reg-val">+5V</span></div>
<div class="reg-row"><span class="reg-field">ピン15</span><span class="reg-val">— （未使用）</span></div>
<div class="reg-row"><span class="reg-field">ピン16</span><span class="reg-val">RESET（出力）</span></div>
<div class="reg-row"><span class="reg-field">ピン17–25</span><span class="reg-val">GND</span></div>
</div>

### CIA-A接続マッピング

すべてのパラレルポート信号はCIA-Aを通じてルーティングされる：

<div class="register-block">
<div class="reg-title">CentronicsからCIA-Aへの信号マッピング</div>
<div class="reg-row"><span class="reg-field">STROBE（ピン1）</span><span class="reg-val">CIA-A ピン18 — PC（ハンドシェイク出力）</span></div>
<div class="reg-row"><span class="reg-field">データ0–7（ピン2–9）</span><span class="reg-val">CIA-A PB0–PB7（ピン10–17）</span></div>
<div class="reg-row"><span class="reg-field">/ACK（ピン10）</span><span class="reg-val">CIA-A PB8（ピン24）</span></div>
<div class="reg-row"><span class="reg-field">BUSY（ピン11）</span><span class="reg-val">CIA-B PA0およびSP（ピン2、39）</span></div>
<div class="reg-row"><span class="reg-field">POUT（ピン12）</span><span class="reg-val">CIA-B PA1およびCNT（ピン3、40）</span></div>
<div class="reg-row"><span class="reg-field">SELECT（ピン13）</span><span class="reg-val">CIA-B PA2（ピン4）</span></div>
</div>

### 転送プロトコル

有効なデータバイトがピン2–9に置かれると、STROBE信号が**1.4マイクロ秒**LOWにパルスし、データが準備完了であることをプリンタに通知する。プリンタは/ACKを**1マイクロ秒**LOWにパルスして確認応答する。プリンタがBUSYをアサートした場合、AmigaはBUSYがデアサートされるまで次のバイト送信を待つ。POUTは用紙切れを、SELECTはプリンタがオンライン（LOW）かオフライン（HIGH）かを示す。

パラレルポートはサウンドデジタイザなどの拡張ハードウェアに適している。CIAの方向レジスタを通じて、すべてのデータピンを入力または出力としてプログラム可能であるためである。

<!-- section: $CONN01 "シリアルポート（RS-232）" -->

シリアルポートは**25ピンD-sub**コネクタを使用し、完全なRS-232インターフェースといくつかの非標準信号を提供する。

<div class="register-block">
<div class="reg-title">RS-232シリアルポート ピン配置（A500/A2000）</div>
<div class="reg-row"><span class="reg-field">ピン1</span><span class="reg-val">GND — フレームグランド（保護接地）</span></div>
<div class="reg-row"><span class="reg-field">ピン2</span><span class="reg-val">TXD — <span class="highlight">送信データ</span>（出力、Paulaから）</span></div>
<div class="reg-row"><span class="reg-field">ピン3</span><span class="reg-val">RXD — <span class="highlight">受信データ</span>（入力、Paulaへ）</span></div>
<div class="reg-row"><span class="reg-field">ピン4</span><span class="reg-val">RTS — 送信要求（出力）</span></div>
<div class="reg-row"><span class="reg-field">ピン5</span><span class="reg-val">CTS — 送信可能（入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン6</span><span class="reg-val">DSR — データセットレディ（入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン7</span><span class="reg-val">GND — 信号グランド</span></div>
<div class="reg-row"><span class="reg-field">ピン8</span><span class="reg-val">CD — キャリア検出（入力、モデム用）</span></div>
<div class="reg-row"><span class="reg-field">ピン9</span><span class="reg-val">+12V</span></div>
<div class="reg-row"><span class="reg-field">ピン10</span><span class="reg-val">-12V</span></div>
<div class="reg-row"><span class="reg-field">ピン11</span><span class="reg-val">AUDOUT — 左オーディオチャンネル出力（1 Kohmインピーダンス）</span></div>
<div class="reg-row"><span class="reg-field">ピン12–17</span><span class="reg-val">— （未使用）</span></div>
<div class="reg-row"><span class="reg-field">ピン18</span><span class="reg-val">AUDIN — オーディオ入力（47オーム、Paula AUDRに接続）</span></div>
<div class="reg-row"><span class="reg-field">ピン19</span><span class="reg-val">— （未使用）</span></div>
<div class="reg-row"><span class="reg-field">ピン20</span><span class="reg-val">DTR — データ端末レディ（出力）</span></div>
<div class="reg-row"><span class="reg-field">ピン21</span><span class="reg-val">RI — リングインジケータ（入力、トランジスタ経由でCentronics SELECTに接続）</span></div>
<div class="reg-row"><span class="reg-field">ピン22–25</span><span class="reg-val">— （未使用）</span></div>
</div>

### RS-232信号ルーティング

データラインTXDとRXDはPaulaのシリアルデータピンに直結する。5つのハンドシェイク信号は**CIA-BポートA**を通じてルーティングされる：

<div class="register-block">
<div class="reg-title">RS-232ハンドシェイクからCIA-Bへのマッピング</div>
<div class="reg-row"><span class="reg-field">DSR</span><span class="reg-val">CIA-B PA3</span></div>
<div class="reg-row"><span class="reg-field">CTS</span><span class="reg-val">CIA-B PA4</span></div>
<div class="reg-row"><span class="reg-field">CD</span><span class="reg-val">CIA-B PA5</span></div>
<div class="reg-row"><span class="reg-field">RTS</span><span class="reg-val">CIA-B PA6</span></div>
<div class="reg-row"><span class="reg-field">DTR</span><span class="reg-val">CIA-B PA7</span></div>
</div>

RS-232信号はCIAに直接接続されていない。**レベルコンバータ**（1488出力ドライバ、1489A入力レシーバ）を通じてTTLとRS-232電圧レベル間を変換する。出力ドライバは+12Vから-5Vのスイングを使用する。入力レシーバは-12Vから+0.5VをLOW、3Vから25VをHIGHとして受け付ける。

RS-232の規約ではハンドシェイク信号はアクティブHIGH、TXDとRXDはマーク（1）に負論理を使用する。出力ドライバが信号を反転するため、対応するCIA-Bポートビットは**アクティブLOW**となる。CIAレジスタの0がRS-232ラインのHIGHとなる。

### オーディオパススルー

AUDOUTピンは1 Kohmの出力インピーダンスで左オーディオチャンネルを出力する。AUDINピン（47オームインピーダンス）はPaulaの右オーディオ入力（AUDR）に直結する。AUDINから入力された外部オーディオ信号は、Paulaのローパスフィルタを通過して右オーディオRCA出力から出力される。

### A1000との違い

A1000のシリアルポートには追加の非標準信号がある：

<div class="register-block">
<div class="reg-title">A1000固有のシリアルポートピン</div>
<div class="reg-row"><span class="reg-field">ピン14</span><span class="reg-val">-5V</span></div>
<div class="reg-row"><span class="reg-field">ピン15</span><span class="reg-val">AUDOUT（オーディオ出力、ピン11の代替）</span></div>
<div class="reg-row"><span class="reg-field">ピン16</span><span class="reg-val">AUDIN（オーディオ入力、ピン18の代替）</span></div>
<div class="reg-row"><span class="reg-field">ピン17</span><span class="reg-val">E — 716 KHzバスクロック（68000からバッファ経由）</span></div>
<div class="reg-row"><span class="reg-field">ピン18</span><span class="reg-val">/INT2 — レベル2割り込み入力（Paulaに接続）</span></div>
<div class="reg-row"><span class="reg-field">ピン21</span><span class="reg-val">+5V</span></div>
<div class="reg-row"><span class="reg-field">ピン23</span><span class="reg-val">+12V</span></div>
<div class="reg-row"><span class="reg-field">ピン24</span><span class="reg-val">MCLK — 3.58 MHzマスタークロック</span></div>
<div class="reg-row"><span class="reg-field">ピン25</span><span class="reg-val">/RESET</span></div>
</div>

<!-- section: $CONN01 "外部フロッピードライブ" -->

外部フロッピーコネクタは**23ピンD-sub**であり、Shugartバス規格を使用する。内蔵ドライブ（DF0）に加えて最大3台の外部ドライブ（DF1–DF3）をサポートする。

<div class="register-block">
<div class="reg-title">外部フロッピーコネクタ ピン配置（23ピンD-sub）</div>
<div class="reg-row"><span class="reg-field">ピン1</span><span class="reg-val">/RDY — ドライブレディ（入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン2</span><span class="reg-val">/DKRD — ディスク読み取りデータ（入力、Paulaに接続）</span></div>
<div class="reg-row"><span class="reg-field">ピン3–7</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">ピン8</span><span class="reg-val">/MTRX — モーター制御（出力、アクティブLOW）</span></div>
<div class="reg-row"><span class="reg-field">ピン9</span><span class="reg-val">/SEL2 — ドライブDF2選択（出力）</span></div>
<div class="reg-row"><span class="reg-field">ピン10</span><span class="reg-val">/DRES — ドライブリセット（出力、システムRESETに接続）</span></div>
<div class="reg-row"><span class="reg-field">ピン11</span><span class="reg-val">/CHNG — ディスク交換（入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン12</span><span class="reg-val">+5V</span></div>
<div class="reg-row"><span class="reg-field">ピン13</span><span class="reg-val">/SIDE — サイド選択（出力：HIGH = サイド0、LOW = サイド1）</span></div>
<div class="reg-row"><span class="reg-field">ピン14</span><span class="reg-val">/WPRO — ライトプロテクト（入力：LOW = 保護あり）</span></div>
<div class="reg-row"><span class="reg-field">ピン15</span><span class="reg-val">/TK0 — トラック0インジケータ（入力：LOW = ヘッドがトラック0上）</span></div>
<div class="reg-row"><span class="reg-field">ピン16</span><span class="reg-val">/DKWE — ディスク書き込みイネーブル（出力：LOW = 書き込みモード）</span></div>
<div class="reg-row"><span class="reg-field">ピン17</span><span class="reg-val">/DKWD — ディスク書き込みデータ（出力、Paulaから）</span></div>
<div class="reg-row"><span class="reg-field">ピン18</span><span class="reg-val">/STEP — ヘッドステップパルス（出力）</span></div>
<div class="reg-row"><span class="reg-field">ピン19</span><span class="reg-val">/DIR — ヘッド方向（出力：LOW = 内側、HIGH = 外側）</span></div>
<div class="reg-row"><span class="reg-field">ピン20</span><span class="reg-val">/SEL3 — ドライブDF3選択（出力）</span></div>
<div class="reg-row"><span class="reg-field">ピン21</span><span class="reg-val">/SEL1 — ドライブDF1選択（出力）</span></div>
<div class="reg-row"><span class="reg-field">ピン22</span><span class="reg-val">/INDEX — インデックスパルス（入力、ディスク1回転ごとに1回）</span></div>
<div class="reg-row"><span class="reg-field">ピン23</span><span class="reg-val">+12V</span></div>
</div>

### フリップフロップによるモーター制御

MTRX信号は個々のドライブモーターを直接制御しない。代わりに、各ドライブには**フリップフロップ**があり、そのSELラインがLOWになったときにMTRXの状態をラッチする。これにより各ドライブのモーターを独立して制御できる。例えば、内蔵ドライブのモーターを起動するには、MTRXを0にしてSEL0をLOWにパルスする。フリップフロップがモーター状態を保持し、その後MTRXを変更しても再びSEL0がパルスされるまで当該ドライブには影響しない。

内蔵ドライブのフリップフロップはマザーボード上にある。外部ドライブには独自のフリップフロップ回路が必要であり、通常**74LS74**と**74LS38** NANDゲートで構成される。

### ドライブ識別

モーターがオフでドライブが選択されているとき、特別な**32ビット識別**モードが利用可能。識別を開始するには、モーター信号を短時間トグルしてドライブのシフトレジスタをリセットする。次に、SELをLOWにサイクルしてRDY信号を読み取ることで各データビットを読み取る。これを32回繰り返す（MSBファースト）。信号はアクティブLOWであるため、ビットを反転する必要がある。

<div class="register-block">
<div class="reg-title">ドライブ識別コード</div>
<div class="reg-row"><span class="reg-field">$00000000</span><span class="reg-val">ドライブ未接続</span></div>
<div class="reg-row"><span class="reg-field">$FFFFFFFF</span><span class="reg-val"><span class="highlight">標準3.5インチドライブ</span></span></div>
<div class="reg-row"><span class="reg-field">$55555555</span><span class="reg-val">5.25インチドライブ、2×40トラック</span></div>
</div>

実際には最初の2ビットのみを読めばよい：00 = ドライブなし、11 = 3.5インチ、01 = 5.25インチ。

### CIA接続

4つの入力信号（/CHNG、/WPRO、/TK0、/RDY）は**CIA-A PA4–PA7**に接続される。8つの出力信号（/STEP、/DIR、/SIDE、/SEL0–/SEL3、/MTR）は**CIA-B PB0–PB7**から出力される。読み書きデータ信号（/DKRD、/DKWD、/DKWE）はPaulaに直結する。すべての信号は**オープンコレクタドライバ**（7407タイプ）を使用し、アクティブLOWである。

<!-- section: $CONN01 "ゲームポート" -->

2つのゲームポートは**9ピンD-sub**コネクタ（DB-9）であり、マウス、ジョイスティック、トラックボール、パドル、ライトペンを接続できる。**ゲームポート0**が左側、**ゲームポート1**が右側。構造的には同一だが、ゲームポート0はAgnusのライトペン（LP）入力にも接続されている点が異なる。

<div class="register-block">
<div class="reg-title">ゲームポート ピン配置（9ピンD-sub）</div>
<div class="reg-row"><span class="reg-field">ピン1</span><span class="reg-val">前進 / マウスVパルス（入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン2</span><span class="reg-val">後退 / マウスHパルス（入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン3</span><span class="reg-val">左 / マウスVQパルス / パドル左ボタン（入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン4</span><span class="reg-val">右 / マウスHQパルス / パドル右ボタン（入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン5</span><span class="reg-val">パドル右ポテンショメータ / マウスボタン3（I/O）</span></div>
<div class="reg-row"><span class="reg-field">ピン6</span><span class="reg-val">ファイアボタン / マウスボタン1 / ライトペントリガー（I/O）</span></div>
<div class="reg-row"><span class="reg-field">ピン7</span><span class="reg-val">+5V（A1000では電流制限あり、A500/A2000では直結）</span></div>
<div class="reg-row"><span class="reg-field">ピン8</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">ピン9</span><span class="reg-val">パドル左ポテンショメータ / マウスボタン2（I/O）</span></div>
</div>

### 内部チップ接続

<div class="register-block">
<div class="reg-title">ゲームポート0 信号ルーティング</div>
<div class="reg-row"><span class="reg-field">ピン1–4</span><span class="reg-val">Denise — M0V、M0H、M1V、M1H（マルチプレクサ経由）</span></div>
<div class="reg-row"><span class="reg-field">ピン5</span><span class="reg-val">Paula — P0Y（アナログポテンショメータ入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン6</span><span class="reg-val">CIA-A PA6 および Agnus LP（ライトペン入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン9</span><span class="reg-val">Paula — P0X（アナログポテンショメータ入力）</span></div>
</div>

<div class="register-block">
<div class="reg-title">ゲームポート1 信号ルーティング</div>
<div class="reg-row"><span class="reg-field">ピン1–4</span><span class="reg-val">Denise — M0V、M0H、M1V、M1H（マルチプレクサ経由）</span></div>
<div class="reg-row"><span class="reg-field">ピン5</span><span class="reg-val">Paula — P1Y（アナログポテンショメータ入力）</span></div>
<div class="reg-row"><span class="reg-field">ピン6</span><span class="reg-val">CIA-A PA7</span></div>
<div class="reg-row"><span class="reg-field">ピン9</span><span class="reg-val">Paula — P1X（アナログポテンショメータ入力）</span></div>
</div>

すべてのボタンおよび方向信号は**アクティブLOW**である — 内部スイッチが押されると入力ピンをGNDに接続する（HIGH = 開、LOW = 閉）。アナログ入力（P0X、P0Y、P1X、P1Y）は+5Vと入力ピンの間に接続された**470 Kohmの可変抵抗**（ポテンショメータ）を受け付ける。

### 電源保護

A1000では、両ゲームポートの+5V供給は**電流リミッタ**で保護されており、連続短絡電流（400 mA）とピークサージ（700 mA）を分離する。両ポートの合計電流消費は**250 mA**を超えてはならない。この保護はA500とA2000モデルでは削除されている。

<!-- section: $CONN01 "拡張ポート" -->

拡張ポートは**86ピンのエッジコネクタ**であり、68000プロセッサの重要なバス信号と制御信号をすべて公開する。メモリ拡張、アクセラレータボード、その他のペリフェラルの接続が可能。A1000ではゲームポート付近にプラスチックカバーの下に配置されている。A500では本体底面にある。ピン間隔は0.1インチ（2.54 mm）。

A2000では構成が異なり、1つの86ピンコネクタと5つの100ピン**Zorroバス**スロットを拡張カード用に備えている。

<div class="register-block">
<div class="reg-title">拡張ポート ピン配置（86ピン、主要信号）</div>
<div class="reg-row"><span class="reg-field">1–4</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">5–6</span><span class="reg-val">+5V</span></div>
<div class="reg-row"><span class="reg-field">8</span><span class="reg-val">-5V</span></div>
<div class="reg-row"><span class="reg-field">9</span><span class="reg-val">拡張（A2000では28 MHz）</span></div>
<div class="reg-row"><span class="reg-field">10</span><span class="reg-val">+12V</span></div>
<div class="reg-row"><span class="reg-field">14</span><span class="reg-val">/C3 — 3.58 MHzクロック位相</span></div>
<div class="reg-row"><span class="reg-field">15</span><span class="reg-val">CDAC — 7.16 MHzクロック</span></div>
<div class="reg-row"><span class="reg-field">16</span><span class="reg-val">/C1 — 3.58 MHzクロック位相</span></div>
<div class="reg-row"><span class="reg-field">17</span><span class="reg-val">/OVR — オーバーライド（自動構成）</span></div>
<div class="reg-row"><span class="reg-field">18</span><span class="reg-val">XRDY — 拡張レディ</span></div>
<div class="reg-row"><span class="reg-field">19</span><span class="reg-val">/INT2 — レベル2割り込み（Paulaへ）</span></div>
<div class="reg-row"><span class="reg-field">22</span><span class="reg-val">/INT6 — レベル6割り込み（Paulaへ）</span></div>
<div class="reg-row"><span class="reg-field">21–59</span><span class="reg-val"><span class="highlight">A1–A23 — 68000アドレスバス</span></span></div>
<div class="reg-row"><span class="reg-field">63–86</span><span class="reg-val"><span class="highlight">PD0–PD15 — 68000データバス</span></span></div>
<div class="reg-row"><span class="reg-field">40–44</span><span class="reg-val">/IPL0–/IPL2 — 割り込み優先レベル</span></div>
<div class="reg-row"><span class="reg-field">31–35</span><span class="reg-val">FC0–FC2 — 68000ファンクションコード</span></div>
<div class="reg-row"><span class="reg-field">74</span><span class="reg-val">/AS — アドレスストローブ</span></div>
<div class="reg-row"><span class="reg-field">72</span><span class="reg-val">/UDS — アッパーデータストローブ</span></div>
<div class="reg-row"><span class="reg-field">70</span><span class="reg-val">/LDS — ローワーデータストローブ</span></div>
<div class="reg-row"><span class="reg-field">68</span><span class="reg-val">/R/W — リード/ライト</span></div>
<div class="reg-row"><span class="reg-field">66</span><span class="reg-val">/DTACK — データアクノリッジ</span></div>
<div class="reg-row"><span class="reg-field">53</span><span class="reg-val">/RES — システムリセット</span></div>
<div class="reg-row"><span class="reg-field">55</span><span class="reg-val">/HLT — ホルト</span></div>
<div class="reg-row"><span class="reg-field">46</span><span class="reg-val">/BERR — バスエラー</span></div>
<div class="reg-row"><span class="reg-field">60</span><span class="reg-val">/BR — バスリクエスト</span></div>
<div class="reg-row"><span class="reg-field">64</span><span class="reg-val">/BG — バスグラント</span></div>
<div class="reg-row"><span class="reg-field">62</span><span class="reg-val">/BGACK — バスグラントアクノリッジ</span></div>
<div class="reg-row"><span class="reg-field">48</span><span class="reg-val">/VPA — 有効ペリフェラルアドレス</span></div>
<div class="reg-row"><span class="reg-field">50</span><span class="reg-val">E — 68000 Eクロック（716 KHz）</span></div>
<div class="reg-row"><span class="reg-field">51</span><span class="reg-val">/VMA — 有効メモリアドレス</span></div>
</div>

### クロック信号

拡張ポートはいくつかのクロック基準を提供する。CDACは7.16 MHzで動作する。/C1と/C3は異なる位相の3.58 MHz信号である。A2000では28.64 MHzのマスター発振器もピン9で利用可能。これらのクロックと内部の7M、CCK、CCKQ信号の関係は位相図から導出できる。

### 構成信号

/OVR、XRDY、PALOPE（A500/A1000のみ）は拡張カードの**自動構成**プロトコルに使用される。「拡張」と記されたピンは将来の使用のために予約されている。A2000では一部がすでに割り当てられている（例：28 MHzクロック）。

<!-- section: $CONN01 "電源" -->

各Amigaコネクタは1つ以上の電源電圧を供給する。合計電流バジェットはモデルと同時に負荷されるポートの数に依存する。

<div class="register-block">
<div class="reg-title">A1000 コネクタあたりの最大電流（Commodore推奨値）</div>
<div class="reg-row"><span class="reg-field">RGB</span><span class="reg-val">+5V: 300 mA | +12V: 175 mA | -5V: 50 mA</span></div>
<div class="reg-row"><span class="reg-field">RS-232</span><span class="reg-val">+5V: 100 mA | +12V: 50 mA | -5V: 50 mA</span></div>
<div class="reg-row"><span class="reg-field">外部フロッピー</span><span class="reg-val">+5V: 270 mA | +12V: 160 mA</span></div>
<div class="reg-row"><span class="reg-field">Centronics</span><span class="reg-val">+5V: 100 mA</span></div>
<div class="reg-row"><span class="reg-field">拡張</span><span class="reg-val"><span class="highlight">+5V: 1000 mA</span> | +12V: 50 mA | -5V: 50 mA</span></div>
<div class="reg-row"><span class="reg-field">TVモジュレータ</span><span class="reg-val">+12V: 60 mA</span></div>
<div class="reg-row"><span class="reg-field">ゲームポート0</span><span class="reg-val">+5V: 125 mA</span></div>
<div class="reg-row"><span class="reg-field">ゲームポート1</span><span class="reg-val">+5V: 125 mA</span></div>
</div>

これらの値は**すべてのコネクタが同時に負荷された場合**を想定している。未使用のコネクタがあれば、その電流バジェットは他で利用可能。A1000の電源は短絡時に8A以上の電流を供給できるため、実験は慎重に行う必要がある。

**A500**はより小さな電源を持つ。電流消費の大きい拡張には外部電源が推奨される。**A2000**はIBMエミュレーションボードを含む複数の拡張カードをサポートするため、より大きな電源を持つ。

注：A500は負電圧レールに**-12V**を提供するが、A1000は**-5V**を提供する。
