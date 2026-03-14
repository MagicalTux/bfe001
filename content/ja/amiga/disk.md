---
title: "ディスクコントローラ"
addr: "$DFF020"
description: "Amigaフロッピーディスクコントローラ — DMA転送、MFMエンコーディング、同期ワード検出、Paulaのディスクレジスタプログラミング。"
order: 13
---

<!-- section: $DFF020 "ディスクコントローラの概要" -->

Amigaのフロッピーディスクコントローラは、Paulaカスタムチップに内蔵された専用回路である。専用DMAチャネルを通じて、ディスクドライブとChip RAM間の低レベルデータストリームを処理する。コントローラは生のエンコード済みデータ（MFMまたはGCR）の読み書きを管理し、信頼性の高いセクタ位置検出のためにハードウェア同期ワード検出機能を備える。

ディスクハードウェアは2つの機能領域に分かれる：

1. **ドライブ制御信号** — モーターのオン/オフ、ヘッドステップ、サイド選択、ドライブ選択 — CIA-Bのポートラインで管理（CIAドキュメントで解説済み）。
2. **データ転送** — ドライブヘッドとメモリ間のエンコード済みビットストリーム。PaulaのディスクコントローラとそのDMAチャネルで管理。

コントローラは1回のDMA操作でトラック全体を読み書きできる。標準的なAmigaトラックはそれぞれ512バイトの11セクタ（トラックあたり5632データバイト）を含むが、MFMエンコーディングのオーバーヘッド、ギャップ、ヘッダのため、生のエンコード済みトラックはかなり大きくなる。

### ディスクコントローラレジスタ

<div class="register-block">
<div class="reg-title">ディスクDMAおよび制御レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF020</span><span class="reg-val">DSKPTH — ディスクDMAポインタ（上位ワード、ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF022</span><span class="reg-val">DSKPTL — ディスクDMAポインタ（下位ワード、ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF024</span><span class="reg-val">DSKLEN — ディスクDMA転送長と制御（書き込み専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF01A</span><span class="reg-val">DSKBYTR — ディスクデータバイトとステータス（読み取り専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF07E</span><span class="reg-val">DSKSYNC — ディスク同期ワード（書き込み専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF026</span><span class="reg-val">DSKDAT — ディスクDMAデータ書き込み（書き込み専用）</span></div>
<div class="reg-row"><span class="reg-field">$DFF008</span><span class="reg-val">DSKDAT — ディスクDMAデータ読み取り（読み取り専用、早期読み取りレジスタ）</span></div>
</div>

<!-- section: $DFF020 "DMAポインタと長さ" -->

ディスクDMA転送を開始する前に、バッファアドレスと転送長を設定する必要がある。

### DSKPTH / DSKPTL — DMAポインタ（$DFF020 / $DFF022、書き込み）

このレジスタペアは、ディスクデータの読み書き先となる19ビットChip RAMアドレスを保持する。ポインタは上位ワードを先に、2回の個別のワード書き込みでロードされる：

```asm
    move.l #DiskBuffer,$DFF020    ; 1回のMOVE.LでDSKPTHとDSKPTLをロード
```

バッファはChip RAM内に配置する必要がある（OCSでは最初の512 KB、ECSでは1 MB、AGAでは2 MB）。

### DSKLEN — 転送長と制御（$DFF024、書き込み）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">DMAEN</span><span class="bit-name">ディスクDMAを有効化（有効にするには2回の書き込みが必要）</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">14</span><span class="bit-dir">WRITE</span><span class="bit-name">1で書き込み、0で読み取り</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13–0</span><span class="bit-dir">LENGTH</span><span class="bit-name">転送するワード数（最大16383）</span><span class="bit-func"></span></div>
</div>

### LENGTHフィールド

下位14ビットは、ディスクとメモリ間で転送する16ビットワードの数を指定する。

### WRITEビット

WRITEが1にセットされると、コントローラは書き込みモードで動作し、データはメモリからディスクに流れる。クリアされている場合、データはディスクからメモリに流れる（読み取りモード）。安全のため、WRITEは実際の書き込み操作を意図する場合のみセットすべきである。

### DMAEN — DMA有効化（ダブルライト保護）

DMAENビットはディスクDMAのマスターイネーブルである。重要な安全対策として、**DMA転送を実際に開始するにはDMAENを連続して2回1に書き込む**必要がある。これにより、誤った1回の書き込みで潜在的に破壊的なディスク操作がトリガーされることを防ぐ。DSKLENへの単発の誤書き込みでは転送を開始できない。

さらに、DMACONレジスタのグローバルディスクDMA有効化ビット（DSKEN、ビット4）もセットされている必要がある。

<!-- section: $DFF020 "DMA初期化シーケンス" -->

ディスクDMA操作を開始するための正しい手順は、データ破損を防ぐために厳密なシーケンスに従う：

### 読み取り操作

```asm
    ; 1. 以前のDMAを無効化
    move.w #$4000,$DFF024      ; DSKLENのDMAENをクリア

    ; 2. グローバルディスクDMAの有効化を確認
    move.w #$8010,$DFF096      ; DMACONのDSKEN（ビット4）をセット

    ; 3. バッファアドレスを設定
    move.l #DiskBuffer,$DFF020 ; DSKPTH/DSKPTL

    ; 4. DSKLENに希望する長さとDMAENを書き込む
    move.w #$8000+NumWords,$DFF024  ; DMAEN=1、WRITE=0、LENGTH=NumWords

    ; 5. 同じ値を再度書き込む（ダブルライトで有効化）
    move.w #$8000+NumWords,$DFF024

    ; 6. DSKBLK割り込みを待つ（DMA完了）
    ;    ...（INTREQRのビット1をポーリングまたは割り込みハンドラを使用）...

    ; 7. 安全対策としてDMAを無効化
    move.w #$4000,$DFF024      ; DMAENをクリア
```

### 書き込み操作

```asm
    ; 1. 以前のDMAを無効化
    move.w #$4000,$DFF024      ; DMAENをクリア

    ; 2. グローバルディスクDMAを有効化
    move.w #$8010,$DFF096      ; DMACONのDSKENをセット

    ; 3. バッファアドレスを設定（Chip RAM内のソースデータ）
    move.l #WriteBuffer,$DFF020

    ; 4. DSKLENにDMAEN + WRITE + 長さを書き込む
    move.w #$C000+NumWords,$DFF024  ; DMAEN=1、WRITE=1、LENGTH=NumWords

    ; 5. 同じ値を再度書き込む
    move.w #$C000+NumWords,$DFF024

    ; 6. DSKBLK割り込みを待つ
    ;    ...（重要：書き込み中にDMAを途中で停止しないこと！）...

    ; 7. DMAを無効化
    move.w #$4000,$DFF024
```

**警告：** 書き込み操作を完了前に中断すると、現在のトラックのデータが破壊される可能性がある。書き込み後のDMA無効化はDSKBLK割り込みを必ず待ってから行うこと。

### DSKBLK割り込み

コントローラがLENGTHで指定されたワード数を転送し終えると、**DSKBLK**（Disk Block Finished）割り込みを発生させる — INTREQ/INTENのビット1。これはDMA操作の完了を通知し、バッファに有効なデータが含まれること（読み取りの場合）、またはデータがディスクに完全に書き込まれたこと（書き込みの場合）を示す。

<!-- section: $DFF020 "DSKBYTR — ディスクステータス" -->

DSKBYTRレジスタはディスクコントローラのリアルタイムステータスを提供し、データストリームのバイトレベルCPUポーリングも可能にする（ただし通常の転送方法はDMA）。

<div class="register-block">
<div class="reg-title">ディスクバイトおよびステータスレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF01A</span><span class="reg-val">DSKBYTR — ディスクデータバイトとステータス（読み取り専用）</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">BYTEREADY</span><span class="bit-name">ビット7–0に完全なデータバイトが利用可能になるとセット</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">14</span><span class="bit-dir">DMAON</span><span class="bit-name">ディスクDMAがアクティブ（DSKLENのDMAENとDMACONのDSKENが両方セット）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">DSKWRITE</span><span class="bit-name">DSKLENのWRITEビットを反映（1 = 書き込みモード）</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">12</span><span class="bit-dir">WORDEQUAL</span><span class="bit-name">現在のディスクワードがDSKSYNC値と一致</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11–8</span><span class="bit-dir">—</span><span class="bit-name">未使用</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–0</span><span class="bit-dir">DATA</span><span class="bit-name">ディスクからの現在のデータバイト</span><span class="bit-func"></span></div>
</div>

### CPUポーリングモード

BYTEREADYとDATAを使用して、CPUはDMAなしでディスクデータを1バイトずつ読み取れる。ディスクから完全なバイトが到着するたびにBYTEREADYがセットされる。DSKBYTRを読み取るとBYTEREADYがクリアされる。この方法はDMAよりはるかに遅く、実際にはめったに使われないが、診断用や特殊用途のコードには利用できる。

### WORDEQUAL

WORDEQUALビットは、受信ディスクデータがDSKSYNCに格納された同期ワードと一致するたびに、短時間（約2マイクロ秒）セットされる。セット時間が非常に短いため、ポーリングで捕捉するのは困難であり、DSKSYNC割り込み（INTREQビット12）の方が信頼性が高い。

<!-- section: $DFF020 "同期ワードとDSKSYNC" -->

**DSKSYNC**レジスタにより、ディスクコントローラはデータストリームの任意の地点ではなく特定の位置からDMA転送を開始できる。

<div class="register-block">
<div class="reg-title">ディスク同期レジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF07E</span><span class="reg-val">DSKSYNC — ディスク同期パターンワード（書き込み専用）</span></div>
</div>

ディスクDMAが開始されると、コントローラはディスクからデータを読み取るが、直ちにメモリには書き込まない。代わりに、受信する各ワードをDSKSYNCの値と比較する。一致が見つかった場合のみ実際のメモリ転送が開始される。このメカニズムにより、コントローラはデータブロックの先頭にある特定の同期マーカーにロックオンできる。

### 標準Amiga同期ワード

AmigaDOSが使用する標準MFM同期ワードは**$4489**である。これは通常のMFMエンコード済みデータには出現しない自己同期パターンであり、セクタ境界の信頼性の高いマーカーとなる。

```asm
    move.w #$4489,$DFF07E      ; 標準Amiga MFM同期ワードを設定
```

### DSKSYNC割り込み

ディスクデータがDSKSYNCと一致すると、コントローラは**DSKSYNC割り込み** — INTREQ/INTENのビット12 — を生成する。これはDMA転送が開始されたかどうかに関係なく発生する。ソフトウェアはこの割り込みを使用して、実際にデータを読み取ることなくセクタの開始を検出できる。

ADKCONのWORDSYNCビット（ビット10）をセットして同期ワードマッチングを有効にする必要がある。WORDSYNCがクリアされている場合、コントローラは同期ワードを待たずに即座にDMAを開始する。

<!-- section: $DFF020 "MFMエンコーディングとADKCON" -->

データは生のバイナリ形式ではフロッピーディスクに書き込めない。ドライブの読み取り回路がクロック同期を維持するのに十分な信号遷移を保証するエンコーディング方式で、まずエンコードする必要がある。Amigaは2つのエンコーディング方式をサポートする：

- **MFM**（Modified Frequency Modulation）— AmigaDOSおよびほとんどのAmigaソフトウェアが使用する標準エンコーディング。
- **GCR**（Group Code Recording）— 他のシステムで使用される代替エンコーディング。

エンコーディングモードと関連パラメータはADKCONレジスタのビットで設定される。

<div class="register-block">
<div class="reg-title">ADKCONディスク関連ビット</div>
<div class="reg-row"><span class="reg-field">$DFF09E</span><span class="reg-val">ADKCON — オーディオ/ディスク制御（書き込み専用、ビット15 = SET/CLR）</span></div>
<div class="reg-row"><span class="reg-field">$DFF010</span><span class="reg-val">ADKCONR — オーディオ/ディスク制御リードバック（読み取り専用）</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">SET/CLR</span><span class="bit-name">1 = ビットをセット、0 = ビットをクリア（書き込み専用）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">PRECOMP1</span><span class="bit-name">プリコンペンセーション選択（上位ビット）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">PRECOMP0</span><span class="bit-name">プリコンペンセーション選択（下位ビット）</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">12</span><span class="bit-dir">MFMPREC</span><span class="bit-name">エンコーディングモード：0 = GCR、1 = MFM</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">WORDSYNC</span><span class="bit-name">同期ワードマッチングの有効化（1 = 有効）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">MSBSYNC</span><span class="bit-name">MSB同期の有効化（GCRモード）</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">8</span><span class="bit-dir">FAST</span><span class="bit-name">ディスククロックレート：1 = 2 us/bit（MFM）、0 = 4 us/bit（GCR）</span><span class="bit-func"></span></div>
</div>

### プリコンペンセーション

フロッピーディスクの内側トラックにデータを書き込む際、高いビット密度により隣接する磁気遷移が互いにシフトする可能性がある。プリコンペンセーションはこの効果を打ち消すために書き込みパルスのタイミングを調整する。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット14</span>
<span class="bit-dir">ビット13</span>
<span class="bit-name">プリコンペンセーション時間</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">0</span><span class="bit-name">なし（無効）</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">1</span><span class="bit-name">140ナノ秒</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">0</span><span class="bit-name">280ナノ秒</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">1</span><span class="bit-name">560ナノ秒</span><span class="bit-func"></span></div>
</div>

### 標準MFM設定

通常のAmigaディスク操作（880 KB 倍密度ディスク）では、標準ADKCON設定は：

```asm
    ; MFMモード、WORDSYNC、FASTクロックレートを有効化
    move.w #$9500,$DFF09E      ; SET + MFMPREC + WORDSYNC + FAST
```

これによりMFMエンコーディングが設定され、セクタ整列読み取り用の同期ワード検出が有効になり、倍密度メディアでのMFMに適した2マイクロ秒/ビットのクロックレートが選択される。

### MFMエンコーディングの原理

MFMエンコーディングでは、各データビットにクロックビットが伴う。クロックビットは、現在のデータビットと前のデータビットの両方が0の場合にのみ1にセットされる。これにより、エンコード済みビットストリーム内で3つ以上の連続するゼロが発生しないことが保証され、ドライブハードウェアによる確実なクロック回復が維持される。

エンコーディングルール：

- データビット**1**は**01**としてエンコード（クロックビット不要）。
- データビット**1**の後のデータビット**0**は**00**としてエンコード（クロックビットなし）。
- データビット**0**の後のデータビット**0**は**10**としてエンコード（クロックビットが挿入される）。

つまり、各データバイトはディスク上で16ビットになる（8データビット + 8クロックビットがインターリーブされる）。ソフトウェアがMFMのエンコード/デコードを実行する必要がある。コントローラはエンコード済みビットストリームの物理的な読み書きのみを処理する。

<!-- section: $DFF020 "データレジスタ" -->

ディスクコントローラは、DMAチャネルとディスクドライブ間の実際のバイトレベル転送に専用データレジスタを使用する。

### DSKDAT — 書き込みデータ（$DFF026、書き込み）

このレジスタはディスクに書き込むデータを保持する。DMA書き込み操作中、DMAコントローラはディスククロックで決定されるレートでメモリからこのレジスタを自動的に充填する。

### DSKDAT — 読み取りデータ（$DFF008、読み取り）

このレジスタはディスクから読み取ったデータを保持する。これは**早期読み取りレジスタ**であり、DMAコントローラ専用でCPUからは読み取れない。CPUによるディスクデータへのバイトレベルアクセスにはDSKBYTRを使用するか、より一般的にはDMAにデータをメモリバッファに直接転送させる。

### 完全な読み取り例

以下のコードはドライブ0のディスクから1つの生MFMトラックを読み取る：

```asm
ReadTrack:
    lea    $DFF000,a5

    ; 以前のDMAを無効化
    move.w #$4000,DSKLEN(a5)       ; DMAENをクリア

    ; バッファポインタを設定
    move.l #TrackBuffer,DSKPTH(a5)

    ; 同期ワードを設定
    move.w #$4489,DSKSYNC(a5)      ; 標準AmigaDOS同期ワード

    ; MFM + 同期用にADKCONを設定
    move.w #$7F00,ADKCON(a5)       ; まずディスクビットをクリア
    move.w #$9500,ADKCON(a5)       ; MFMPREC + WORDSYNC + FASTをセット

    ; ディスクDMAを有効化
    move.w #$8010,DMACON(a5)       ; DSKENをセット

    ; DMA読み取り開始：0x1900ワード = 6400ワード = 12800バイト（1トラック）
    move.w #$8000+$1900,DSKLEN(a5) ; 1回目の書き込み
    move.w #$8000+$1900,DSKLEN(a5) ; 2回目の書き込み（DMAを有効化）

    ; 完了を待つ
.wait:
    btst   #1,INTREQR+1(a5)       ; DSKBLKビットをテスト
    beq.s  .wait

    ; 後処理
    move.w #$4000,DSKLEN(a5)       ; ディスクDMAを無効化
    move.w #$0002,INTREQ(a5)       ; DSKBLK割り込みをクリア
    rts
```

バッファには同期ワード$4489の最初の出現から始まる生のMFMエンコード済みトラックデータが格納される。ソフトウェアはその後セクタヘッダを特定し、MFMデータをデコードして512バイトのセクタを抽出する必要がある。
