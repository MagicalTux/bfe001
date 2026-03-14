---
title: "Copperコプロセッサ"
addr: "$DFF080"
description: "Amiga Copperコプロセッサ — ディスプレイと同期してレジスタ操作を行うシンプルだが強力なプロセッサ。"
order: 6
---

<!-- section: $DFF080 "Copperの概要" -->

**Copper**（コプロセッサの略）は、Agnusチップに内蔵されたシンプルだが非常に強力なプロセッサである。その唯一の役割は、ビデオビーム位置を監視し、表示中の正確なタイミングでカスタムチップレジスタに値を書き込むことである。フレーム途中でカラーレジスタ、ビットプレーンポインタ、スプライトデータを変更することで、CPUだけでは不可能なエフェクトを実現する――画面分割、レインボーグラデーション、ライン単位のパレット切替、スプライトの多重化など。

Copperは独自のプログラムカウンタを持ち、Chip RAMに格納された**Copperリスト**を実行する。わずか3つの命令しか認識しないにもかかわらず、ディスプレイ全体を制御できる。

### 3つの命令

**MOVE** — 任意のカスタムチップレジスタに即値16ビットを書き込む（一部制限あり）。

**WAIT** — ビデオビームが指定された画面位置に到達するまで実行を停止する。

**SKIP** — ビームが指定位置を通過したかテストし、真であれば次の命令をスキップする。これによりCopperリスト内で条件分岐が可能になる。

各命令はちょうど2ワード（4バイト）。Copperリストはこれらの命令の連続で、到達不可能なビーム位置へのWAITで終端する。

```asm
WAIT (x1,y1)  ; ビーム位置 x1,y1 を待つ
MOVE #0,$180   ; 背景色レジスタに0を書き込む
MOVE #1,$181   ; カラーレジスタ1に1を書き込む
WAIT (x2,y2)  ; ビーム位置 x2,y2 を待つ
```

<!-- section: $DFF080 "Copperレジスタ" -->

Copperはカスタムチップアドレス空間のベースにあるいくつかのレジスタを使用する。すべて**ライトオンリー**。

<div class="register-block">
<div class="reg-title">Copperレジスタ</div>
<div class="reg-row"><span class="reg-field">$DFF080</span><span class="reg-val">COP1LCH — Copperリスト1ポインタ（上位ワード）</span></div>
<div class="reg-row"><span class="reg-field">$DFF082</span><span class="reg-val">COP1LCL — Copperリスト1ポインタ（下位ワード）</span></div>
<div class="reg-row"><span class="reg-field">$DFF084</span><span class="reg-val">COP2LCH — Copperリスト2ポインタ（上位ワード）</span></div>
<div class="reg-row"><span class="reg-field">$DFF086</span><span class="reg-val">COP2LCL — Copperリスト2ポインタ（下位ワード）</span></div>
<div class="reg-row"><span class="reg-field">$DFF088</span><span class="reg-val">COPJMP1 — <span class="highlight">ストローブ: COP1LCをプログラムカウンタにロード</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF08A</span><span class="reg-val">COPJMP2 — <span class="highlight">ストローブ: COP2LCをプログラムカウンタにロード</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF02E</span><span class="reg-val">COPCON — Copper制御（ビット0: Blitterレジスタ $040–$07E へのアクセスを許可）</span></div>
</div>

2つの`COPxLC`レジスタペアは、Chip RAM内のCopperリストの18ビットアドレスを保持する。ロングワードアドレスであるため、`MOVE.L`命令でロードする。`COPJMPx`レジスタは**ストローブ**レジスタ――任意の書き込みでアクションがトリガーされる（書き込まれた値は無関係）。

各垂直帰線期間（ライン0）の開始時に、ハードウェアは自動的に`COP1LC`をプログラムカウンタにロードする。これにより、Copperは毎フレーム同じリストを再生する。

<!-- section: $DFF080 "命令エンコーディング" -->

3つの命令はすべて同じ2ワード（32ビット）フォーマットを共有する。第1ワードの最下位ビットがMOVEとWAIT/SKIPを区別し、第2ワードの最下位ビットがWAITとSKIPを区別する。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num"></span>
<span class="bit-dir">ビット</span>
<span class="bit-name">MOVE</span>
<span class="bit-func">WAIT / SKIP</span>
</div>
<div class="bit-row"><span class="bit-num">BW1</span><span class="bit-dir">15–9</span><span class="bit-name">x</span><span class="bit-func">VP7–VP1（垂直ビーム位置）</span></div>
<div class="bit-row"><span class="bit-num">BW1</span><span class="bit-dir">8–1</span><span class="bit-name">RA8–RA1</span><span class="bit-func">VP0, HP8–HP2（水平ビーム位置）</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">BW1</span><span class="bit-dir">0</span><span class="bit-name">0</span><span class="bit-func">1（WAIT/SKIPとMOVEを区別）</span></div>
<div class="bit-row"><span class="bit-num">BW2</span><span class="bit-dir">15</span><span class="bit-name">DW15</span><span class="bit-func">BFD（Blitter Finish Disable）</span></div>
<div class="bit-row"><span class="bit-num">BW2</span><span class="bit-dir">14–1</span><span class="bit-name">DW14–DW1</span><span class="bit-func">VM6–VM0, HM8–HM2（マスクビット）</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">BW2</span><span class="bit-dir">0</span><span class="bit-name">DW0</span><span class="bit-func">WAIT=0 / SKIP=1</span></div>
</div>

**凡例:** RA = レジスタアドレス、DW = データワード、VP = 垂直位置、HP = 水平位置、VM = 垂直マスク、HM = 水平マスク、BFD = Blitter Finish Disable。

<!-- section: $DFF080 "MOVE命令" -->

MOVE命令は**第1ワードのビット0 = 0**で識別される。レジスタアドレスはビット8–1（9ビットだがビット0は常に0でワード境界アドレス）。第2ワードは書き込む16ビット値。

**レジスタ制限:** 通常、Copperは`$040`未満のレジスタにアクセスできない。`COPCON`（$DFF02E）のビット0をセットすると、`$040`–`$07E`（Blitterレジスタ）へのアクセスも許可される。`$000`–`$03E`のレジスタは常にアクセス禁止――これによりCopperが自身やDMAコントローラを再プログラムすることを防ぐ。

<!-- section: $DFF080 "WAIT命令" -->

WAIT命令は**第1ワードのビット0 = 1**かつ**第2ワードのビット0 = 0**で識別される。ビデオビームが指定位置に到達または通過するまでCopperを停止させる。すでにその位置を通過している場合は、即座に実行を続行する。

**垂直解像度:** 8ビット（VP0–VP7）でライン0–255をアドレス指定。PALは313ラインあるため、256行以降にアクセスするには2つの連続WAITが必要――まずライン255まで、次に目標ライン（ハードウェアは垂直カウンタのビット8を無視する）。

**水平解像度:** 7ビット（HP2–HP8）で1ラインあたり128ポジション。HP0とHP1は使用不可のため、水平位置は低解像度ピクセル4個ごとに量子化される。

**マスクビット:** マスク（VM、HM）は、どの位置ビットが比較に参加するかを制御する。マスクと位置の両方にセットされたビットのみがチェックされる。これにより「16ライン毎にトリガー」のようなパターンが可能になる。

**BFD（Blitter Finish Disable）:** BFD=0のとき、Copperは毎回のWAITでBlitterが現在の操作を完了するのを待ってからビーム位置をチェックする。BFD=1にするとBlitterの状態を無視する。

<!-- section: $DFF080 "SKIP命令" -->

SKIPはWAITと同じエンコーディングだが、**第2ワードのビット0 = 1**。ビーム位置が指定位置以上かテストし、真であれば次の命令をスキップする。これにより条件分岐が可能になる――例えば、スキップされる命令を`COPJMP`にして別のCopperリストにジャンプできる。

マスクとBFDビットはWAITと同じ動作をする。

<!-- section: $DFF080 "Copperリストの構造" -->

Copperリストは、Chip RAM内のMOVE、WAIT、SKIP命令の線形シーケンスである。開始アドレスは`COP1LC`に格納される（セカンダリリストの場合は`COP2LC`）。

### リストの終端

リストは到達不可能なビーム位置へのWAITで終端しなければならない。標準的なターミネータは：

```asm
DC.W $FFFF,$FFFE    ; 位置 ($FF,$FE) を待つ — 到達不可能
```

`$E4`を超える水平位置は到達不可能なので、この命令は次の垂直帰線が`COP1LC`を再ロードするまでCopperをアイドル状態に保つ。

### 2つのCopperリスト

Copperは2つのリストポインタをサポートする。これはディスプレイ変更のダブルバッファリングや画面分割によく使われる――`COP1LC`が上部を処理し、`COPJMP2`へのMOVEで`COP2LC`のリストに切り替えて下部を処理する。

<!-- section: $DFF080 "Copper割り込み" -->

Copperは`INTREQ`レジスタに書き込むことで割り込みをトリガーできる：

```asm
MOVE #$8010,INTREQ    ; SET/CLR + COPPER割り込みビット
```

`INTREQ`のビット4はCopperに専用である。これはレベル3割り込みを生成し、68000がサービスできる。主な用途は**ラスタ割り込み**――特定の画面位置に到達したことをCPUに通知し、ディスプレイに同期したフレーム単位の更新を可能にする。

<!-- section: $DFF080 "Copper DMA" -->

CopperはDMAチャネルを通じて命令をフェッチする。**偶数バスサイクル**を使用し、BlitterとMC68000の両方より高い優先度を持つ。各命令は2回のDMAサイクル（ワードごとに1回）を必要とする。WAIT命令はビーム位置をチェックするために追加の1サイクルが必要で、待機フェーズ中Copperはバスを解放する。

`DMACON`（$DFF096）の`COPEN`ビット（ビット7）がCopper DMAを有効にする。クリアするとCopperは実行を停止しバスを解放する。セットすると現在のプログラムカウンタから実行を再開する――そのため`COPxLC`レジスタは事前に適切に初期化しておく必要がある。

### 初期化シーケンス

```asm
    LEA $DFF000,A5                    ; カスタムチップベースアドレス
    MOVE.W #$0080,DMACON(A5)          ; Copper DMAを無効化
    MOVE.L #COPPERLIST,COP1LCH(A5)   ; Copperリストアドレスを設定
    CLR.W COPJMP1(A5)                ; アドレスをプログラムカウンタにロード
    MOVE.W #$8080,DMACON(A5)          ; Copper DMAを有効化
```

<!-- section: $DFF080 "例: カラーバー" -->

このサンプルプログラムは、特定のビーム位置で背景色を変更してカラフルな水平バンドを生成するCopperリストをインストールする。

```asm
;*** Copperリスト カラーバーの例 ***

; カスタムチップレジスタオフセット
INTENA  = $9A    ; 割り込みイネーブル（書き込み）
DMACON  = $96    ; DMA制御（書き込み）
COLOR00 = $180   ; 背景色レジスタ
COP1LC  = $80    ; Copperリスト1ポインタ
COPJMP1 = $88    ; ストローブ: リスト1にジャンプ
CIAAPRA = $BFE001  ; CIA-A ポートA（マウス左ボタン）

Start:
    move.l 4,a6                       ; ExecBase
    moveq  #CLsize,d0
    moveq  #2,d1                      ; MEMF_CHIP
    jsr    -198(a6)                    ; AllocMem
    move.l d0,CLadr
    beq.s  .done

    ; CopperリストをChip RAMにコピー
    lea    CLstart,a0
    move.l CLadr,a1
    moveq  #CLsize-1,d0
.copy:
    move.b (a0)+,(a1)+
    dbf    d0,.copy

    ; Copperをインストールして開始
    jsr    -132(a6)                    ; Forbid
    lea    $DFF000,a5
    move.w #$03A0,DMACON(a5)          ; DMAを無効化
    move.l CLadr,COP1LC(a5)
    clr.w  COPJMP1(a5)
    move.w #$8280,DMACON(a5)          ; Copper + ビットプレーンDMAを有効化

    ; マウス左ボタンを待つ
.wait:
    btst   #6,CIAAPRA
    bne.s  .wait

    ; システムCopperリストを復元
    move.l #0,a1
    clr.l  d0
    jsr    -552(a6)                    ; OpenLibrary "graphics.library"
    move.l d0,a4
    move.l 38(a4),COP1LC(a5)          ; StartListを復元
    clr.w  COPJMP1(a5)
    move.w #$83E0,DMACON(a5)
    jsr    -138(a6)                    ; Permit
    move.l CLadr,a1
    moveq  #CLsize,d0
    jsr    -210(a6)                    ; FreeMem

.done:
    clr.l  d0
    rts

CLadr:  DC.L 0

; Copperリストデータ
CLstart:
    DC.W COLOR00,$000F                ; 青
    DC.W $780F,$FFFE                  ; WAITライン $78
    DC.W COLOR00,$00F0                ; 緑
    DC.W $D70F,$FFFE                  ; WAITライン $D7
    DC.W COLOR00,$0F00                ; 赤
    DC.W $FFFF,$FFFE                  ; リスト終端
CLend:
CLsize = CLend-CLstart
```

WAIT命令は水平位置として`$0F`（水平帰線開始）を使用しているため、色の変更は可視領域外で行われ、クリーンな遷移となる。水平位置を`$00`に変更すると、画面の右端で色の切替が見えるようになる。
