---
title: "キーボード"
addr: "$KBD001"
description: "Amigaキーボードサブシステム — MOS 6500/1マイクロプロセッサ、キーマトリクススキャン、シリアルデータ転送プロトコル、ハンドシェイクメカニズム。"
order: 14
---

<!-- section: $KBD001 "キーボードの概要" -->

Amigaのキーボードは**インテリジェントキーボード**である。独自のマイクロプロセッサを内蔵し、すべてのキースキャン処理を行い、有効なキーコードのみをコンピュータに送信する。A500、A1000、A2000の各モデルで複数のキーボードバージョンが存在するが、違いは主にキーの数と物理的なレイアウトのみである。

キーボードは**RAWキーコード**のみを送信し、ASCII文字は送らない。オペレーティングシステムがキーマップテーブルを使用してRAWコードをASCIIに変換する。RAWキーコードの空間は以下のように構成されている：

<div class="register-block">
<div class="reg-title">RAWキーコード範囲</div>
<div class="reg-row"><span class="reg-field">$00–$3F</span><span class="reg-val">英数字・記号キー — キーボードの行順に配置</span></div>
<div class="reg-row"><span class="reg-field">$40–$4F</span><span class="reg-val">RETURN、TAB、BACKSPACEなどの制御キー</span></div>
<div class="reg-row"><span class="reg-field">$50–$5F</span><span class="reg-val">ファンクションキー（F1–F10）およびHELP</span></div>
<div class="reg-row"><span class="reg-field">$60–$67</span><span class="reg-val">修飾キー — 左右Shift、左右Alt、左右Amiga、Control</span></div>
</div>

キーボードプロセッサは**キーダウン**（押下）と**キーアップ**（解放）の状態を区別できる。キーコードは7ビット（$00–$7F）を占め、8ビット目が**キーアップ/ダウンフラグ**となる。ビット7が0ならキーは押されており、ビット7が1ならキーは離されている。複数キーの同時押しにも対応しており、一部の音楽ソフトウェアはこの機能を利用して和音を演奏する。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">位置</span>
<span class="bit-name">名前</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">7</span><span class="bit-dir">MSB</span><span class="bit-name">UP/DN</span><span class="bit-func">0 = キーダウン（押下）、1 = キーアップ（解放）</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir"></span><span class="bit-name">K6</span><span class="bit-func">キーコード ビット6</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir"></span><span class="bit-name">K5</span><span class="bit-func">キーコード ビット5</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir"></span><span class="bit-name">K4</span><span class="bit-func">キーコード ビット4</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir"></span><span class="bit-name">K3</span><span class="bit-func">キーコード ビット3</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir"></span><span class="bit-name">K2</span><span class="bit-func">キーコード ビット2</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir"></span><span class="bit-name">K1</span><span class="bit-func">キーコード ビット1</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">LSB</span><span class="bit-name">K0</span><span class="bit-func">キーコード ビット0</span></div>
</div>

### Caps Lockの例外

Caps Lockキーは特殊なケースで、キーボードはトグルスイッチを模擬する。Caps Lockを押すとアクティブになりLEDが点灯する。2度目に押されるまで**キーアップ**コードは送信されず、2度目の押下時にキーアップコードが送信されてLEDが消灯する。つまり、アップ/ダウンフラグは物理的なキー位置ではなくロック状態を追跡する。

<!-- section: $KBD001 "キーボードの電子回路" -->

### MOS 6500/1マイクロプロセッサ

キーボードの中核は**MOS 6500/1**であり、6502 CPUコアをベースにしたシングルチップマイクロコンピュータである。独立して動作するために必要なすべてを統合している：

<div class="register-block">
<div class="reg-title">MOS 6500/1 内部リソース</div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">6502コア</span></div>
<div class="reg-row"><span class="reg-field">ROM</span><span class="reg-val">2 KB — キーボードスキャンプログラムを格納</span></div>
<div class="reg-row"><span class="reg-field">RAM</span><span class="reg-val">64バイト スタティックRAM</span></div>
<div class="reg-row"><span class="reg-field">I/O</span><span class="reg-val">4つの双方向8ビットポート（PA、PB、PC、PD）</span></div>
<div class="reg-row"><span class="reg-field">タイマー</span><span class="reg-val">16ビットカウンタ（外部クロック入力付き）</span></div>
<div class="reg-row"><span class="reg-field">クロック</span><span class="reg-val">オンチップ発振器 — 3 MHz水晶、1.5 MHz内部クロック（2分周）</span></div>
</div>

6500/1に必要なのは5V電源と水晶発振子のみ。Amigaキーボードは**3 MHz水晶**を使用し、内部クロック周波数は**1.5 MHz**となる。

**556デュアルタイマー**ICが6500/1のRESET信号を生成する。

### キーマトリクス

キーは2つのグループに分けられている：

**直接接続キー:** 7つの修飾キー（左Shift、右Shift、左Alt、右Alt、左Amiga、右Amiga、Control）はポートPBの下位ビットに直接接続されている。

**マトリクスキー:** その他すべてのキーは**6列×15行のマトリクス**に配置されている。6列はポートAの信号PA2–PA7に接続され、入力モードに設定される。15行はポートCとDで駆動される（PD7ピンは後期バージョンでは未接続）。

### スキャンプロセス

6500/1がキーボードをスキャンする際、各行をLOWに駆動して有効化する。ポートCおよびDの出力は内部プルアップ抵抗のない**オープンコレクタ**型であり、1に設定された出力は実質的に非アクティブ（ハイインピーダンス）となる。行がLOWに駆動されると、プロセッサは6つの列入力を読み取る。列入力には**内部プルアップ抵抗**があるため、押されていないキーはHIGHを読み取る。

各キーは交差点で列と行を接続する。アクティブな行のキーが押されると、対応する列入力がLOWに引き下げられる。すべての行をスキャンした後、プロセッサはすべてのキーの完全な状態を把握する。前回のスキャンから状態が変化していれば、対応するキーコードがAmigaに送信される。

<!-- section: $KBD001 "シリアルデータ転送" -->

### 物理的接続

キーボードはAmigaと**4線ケーブル**で接続される。2本はキーボード電子回路の5V電源用。データ転送には残りの2本を使用する：

<div class="register-block">
<div class="reg-title">キーボード信号線</div>
<div class="reg-row"><span class="reg-field">KDAT</span><span class="reg-val">キーボードデータ線 — CIA-A SP（シリアルポート）入力に接続</span></div>
<div class="reg-row"><span class="reg-field">KCLK</span><span class="reg-val">キーボードクロック線 — CIA-A CNT（カウンタ）入力に接続</span></div>
</div>

### 転送プロトコル

データ転送は**単方向** — キーボードからAmigaへの一方向のみ。6500/1がKDATラインにデータビットを載せ、KCLKにLOWクロックパルスを伴わせる。

**ビットあたりのタイミング：**
- KCLKのLOWパルス幅: **20マイクロ秒**
- クロックパルス間隔: **40マイクロ秒**
- 1ビットあたりの合計時間: **60マイクロ秒**
- 1バイト（8ビット）の完全な転送時間: **480マイクロ秒**
- 実効ボーレート: **16,666ボー**

データ信号は**アクティブLOW**（LOW = 論理1、HIGH = 論理0）である。

### ビット送信順序

ビットは標準的なMSBファースト順（7-6-5-4-3-2-1-0）では送信**されない**。代わりに、バイトは送信前に**1ビット左にローテート**され、送信順序は**6-5-4-3-2-1-0-7**となる。キーアップ/ダウンフラグ（ビット7）は常に最後に送信される。

例えば、'J'のRAWキーコードは`10100110`である。左ローテート後、`01001101`として送信される。

このローテーションにより、同期エラーが発生した場合でも最後に受信されるビットは常にアップ/ダウンフラグとなる。再同期中にキーボードは1のみを送信するため、化けたバイトは常に**キーアップ**コードとして解釈される。これは無害である。偽のキーダウンの方がはるかに問題となる。

### CIAシフトレジスタでの受信

CIA-Aのシフトレジスタは、KCLKからのクロックパルスごとに1ビットを取り込む。8パルス後に完全なバイトが利用可能となり、CIAは**レベル2割り込み**を生成する。オペレーティングシステムの割り込みハンドラは以下を実行する：

1. シリアルデータレジスタを読み取る
2. バイトを反転し右にローテートして元のキーコードを復元する
3. ハンドシェイクパルスを送信する
4. キーコードを処理する

<!-- section: $KBD001 "ハンドシェイクプロトコル" -->

8ビットすべてを送信した後、キーボードはAmigaからの**ハンドシェイク**を待つ。Amigaは、KDATラインを約**75マイクロ秒**LOWに引き下げることで確認応答する。これにより、キーボードはバイトが受信されたことを認識し、次のコードを送信できる。

### 同期回復

正しいデータ転送には送信側と受信側の同期が必要であり、ビット位置が一致していなければならない。Amigaがキーボード送信中に電源オフされたり、Amiga動作中にキーボードが接続されたりすると、同期の喪失が発生する。Amiga自体は同期エラーを検出する手段を持たない。この責任は完全にキーボード側にある。

各バイト転送後、キーボードは最大**145ミリ秒**間ハンドシェイク信号を待つ。この時間内にハンドシェイクが届かない場合、キーボードは転送エラーと判断し、特別な**再同期モード**に入る：

1. キーボードはKDATを1にセットし、単一のクロックパルスを送信する
2. 再び145ミリ秒間ハンドシェイクを待つ
3. ハンドシェイクが受信されるまで繰り返し、同期の回復を確認する

再同期中、キーボードは1ビットのみを送信する。最後に受信されるビットは常にアップ/ダウンフラグ（左ローテーションによる）であるため、誤って受信されたバイトは常に**キーアップ**イベントとして解釈され、安全に無視できる。

<!-- section: $KBD001 "特殊コード" -->

キーボードは$F9–$FEの範囲の予約キーコードを使用して、いくつかの特殊な状態をAmigaに通知する：

<div class="register-block">
<div class="reg-title">特殊キーボードコード</div>
<div class="reg-row"><span class="reg-field">$F9</span><span class="reg-val"><span class="highlight">前回のキーコードが無効</span> — 再同期完了後に送信される。Amigaは前回の送信が失われたことを認識する。その後、キーボードは以前失われたコードを再送信する。</span></div>
<div class="reg-row"><span class="reg-field">$FA</span><span class="reg-val"><span class="highlight">キーボードバッファオーバーフロー</span> — キーボードには10文字のバッファがある。満杯になると$FAでAmigaに警告し、バッファを空けなければキーコードが失われる可能性があることを通知する。</span></div>
<div class="reg-row"><span class="reg-field">$FC</span><span class="reg-val"><span class="highlight">セルフテスト失敗</span> — 電源投入時にキーボードはセルフテストを実行する（Caps Lock LEDの短い点滅で確認可能）。テストが失敗した場合、キーボードは$FCを送信しLEDを点灯させたままロックアップする。</span></div>
<div class="reg-row"><span class="reg-field">$FD</span><span class="reg-val"><span class="highlight">電源投入キーストリーム開始</span> — 電源投入時に押されていたキーのコードシーケンスの開始を示す。</span></div>
<div class="reg-row"><span class="reg-field">$FE</span><span class="reg-val"><span class="highlight">電源投入キーストリーム終了</span> — 電源投入キーシーケンスの終了を示す。キーが押されていなかった場合、$FDと$FEが連続して送信される。</span></div>
</div>

### 電源投入シーケンス

Amigaの電源投入時、キーボードプロセッサはセルフテストを実行する（Caps Lock LEDが短く点灯）。完了後、電源投入中に押されていたキーのコードを**$FD**（開始）と**$FE**（終了）で挟んで送信する。キーが押されていなかった場合は、$FDの直後に$FEのみが送信される。

<!-- section: $KBD001 "キーボードリセット" -->

キーボードはAmiga全体の**ハードウェアリセット**をトリガーできる。**両方のAmigaキー**と**Controlキー**を同時に押すと、キーボードプロセッサはKCLKラインを約**0.5秒間**LOWに保持する。これによりRESET信号が生成され、完全なシステム再起動が強制される。リセットは3つのキーのいずれかが離されたときにトリガーされる。

リセットシーケンス中、Caps Lock LEDが視覚的確認として点滅する。

KCLK信号はCIA-AのCNTピンに接続されているため、理論的にはCIAを適切にプログラミングすることでソフトウェアからリセットをトリガーすることも可能である。ただし、推奨される手法ではない。

<div class="register-block">
<div class="reg-title">リセットキーの組み合わせ</div>
<div class="reg-row"><span class="reg-field">キー</span><span class="reg-val">Control + 左Amiga + 右Amiga（3つ同時に保持）</span></div>
<div class="reg-row"><span class="reg-field">信号</span><span class="reg-val">KCLKが約500ミリ秒間LOWに保持</span></div>
<div class="reg-row"><span class="reg-field">CIA接続</span><span class="reg-val">KCLK → CIA-A CNTピン</span></div>
<div class="reg-row"><span class="reg-field">効果</span><span class="reg-val"><span class="highlight">完全なシステムリセット — キー解放時にトリガー</span></span></div>
</div>
