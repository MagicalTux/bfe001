---
title: "メモリ構成"
addr: "$000000"
description: "Amigaのメモリマップ — Chip RAM、Fast RAM、CIAとカスタムチップのアドレス空間、ROM、A1000 WOM。"
order: 4
---

<!-- section: $000000 "メモリマップ" -->

Amigaの68000は24ビットアドレスバスを持ち、16 MBのアドレス空間（$000000–$FFFFFF）を提供する。この空間はRAM、カスタムチップレジスタ、CIAレジスタ、拡張スペース、ROMに分割される。Gary（アドレスデコーダチップ）が各バスサイクルでどのデバイスが応答するかを決定する。

<div class="addr-map">
    <div class="addr-row"><div class="addr-label">$000000</div><div class="addr-body"><div class="addr-name">Chip RAM</div><div class="addr-size">512 KB ($000000–$07FFFF) — Fat Agnusで1 MBまで拡張可能</div></div></div>
    <div class="addr-row"><div class="addr-label">$080000</div><div class="addr-body"><div class="addr-name">Chip RAM（拡張）</div><div class="addr-size">512 KB ($080000–$0FFFFF) — 1 MB Agnusのみ</div></div></div>
    <div class="addr-row"><div class="addr-label">$100000</div><div class="addr-body"><div class="addr-name">予約</div><div class="addr-size">$100000–$1FFFFF — 標準A500では未使用</div></div></div>
    <div class="addr-row"><div class="addr-label">$200000</div><div class="addr-body"><div class="addr-name">Fast RAM（拡張）</div><div class="addr-size">$200000–$9FFFFF — トラップドアまたはZorro経由で最大8 MB</div></div></div>
    <div class="addr-row"><div class="addr-label">$A00000</div><div class="addr-body"><div class="addr-name">予約 / 拡張</div><div class="addr-size">$A00000–$BEFFFF — I/O拡張スペース</div></div></div>
    <div class="addr-row addr-highlight"><div class="addr-label">$BFD000</div><div class="addr-body"><div class="addr-name">CIA-B (8520)</div><div class="addr-size">偶数アドレス: $BFD000, $BFD100, $BFD200, …</div></div></div>
    <div class="addr-row addr-highlight"><div class="addr-label">$BFE001</div><div class="addr-body"><div class="addr-name">CIA-A (8520)</div><div class="addr-size">奇数アドレス: $BFE001, $BFE101, $BFE201, …</div></div></div>
    <div class="addr-row"><div class="addr-label">$C00000</div><div class="addr-body"><div class="addr-name">Slow RAM（A500トラップドア）</div><div class="addr-size">$C00000–$D7FFFF — 512 KB「Slow」拡張RAM</div></div></div>
    <div class="addr-row"><div class="addr-label">$D80000</div><div class="addr-body"><div class="addr-name">予約</div><div class="addr-size">$D80000–$DFFFFF</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF000</div><div class="addr-body"><div class="addr-name">カスタムチップレジスタ</div><div class="addr-size">$DFF000–$DFF1FF — 約200レジスタ（Agnus、Denise、Paula）</div></div></div>
    <div class="addr-row"><div class="addr-label">$E00000</div><div class="addr-body"><div class="addr-name">予約 / 拡張ROM</div><div class="addr-size">$E00000–$E7FFFF</div></div></div>
    <div class="addr-row"><div class="addr-label">$E80000</div><div class="addr-body"><div class="addr-name">Autoconfig空間</div><div class="addr-size">$E80000–$EFFFFF — Zorro IIオートコンフィグレジスタ</div></div></div>
    <div class="addr-row"><div class="addr-label">$F00000</div><div class="addr-body"><div class="addr-name">予約</div><div class="addr-size">$F00000–$F7FFFF</div></div></div>
    <div class="addr-row"><div class="addr-label">$F80000</div><div class="addr-body"><div class="addr-name">予約 / 診断ROM</div><div class="addr-size">$F80000–$FBFFFF</div></div></div>
    <div class="addr-row"><div class="addr-label">$FC0000</div><div class="addr-body"><div class="addr-name">Kickstart ROM</div><div class="addr-size">$FC0000–$FFFFFF — 256 KB（Kickstart 1.2/1.3）</div></div></div>
</div>

<!-- section: $000000 "Chip RAMとFast RAM" -->

Amigaのメモリマップで最も重要な区別は**Chip RAM**と**Fast RAM**の違いである。

**Chip RAM**（$000000–$07FFFF、Fat Agnusでは$0FFFFF）は68000とカスタムチップの両方からアクセス可能。カスタムチップが必要とするすべてのデータ――ビットプレーングラフィックス、スプライト、オーディオサンプル、Copperリスト、Blitterソース――はChip RAMに存在しなければならない。AgnusがDMAアドレスを生成し、メモリの下位部分しかアドレスできないためである。

**Fast RAM**（$200000以上）は68000からのみアクセス可能。カスタムチップからは見えない。利点は、68000がDMAとの競合なしにフルスピードでアクセスできること――だから「fast」。DMAアクセスを必要としないプログラムやデータ構造は、利用可能であればFast RAMに配置すべきである。

**Slow RAM**（$C00000–$D7FFFF）はA500のトラップドア拡張RAM。「fast」アドレス範囲にあるにもかかわらず、チップバス上にあるためDMA競合の影響を受ける――真のFast RAMより遅いがカスタムチップからもアクセスできない。両方の世界の最悪だが、拡張なしよりはましである。

<!-- section: $000000 "CIAアドレス空間" -->

2つのCIA 8520チップはメモリマップ上で独特な位置を占める。CIA-Aは**$BFE001**から始まる**奇数**バイトアドレスに、CIA-Bは**$BFD000**から始まる**偶数**アドレスに配置される。この珍しい配置は、Garyがアドレスをデコードしてデータバスの異なるハーフにCIAを接続する方法の副作用である。

各CIAは16のレジスタを持ち、256バイト間隔で配置される（A0–A3とA8–A11のみデコード）：

<div class="register-block">
<div class="reg-title">CIA-Aレジスタアドレス</div>
<div class="reg-row"><span class="reg-field">$BFE001</span><span class="reg-val">PRA — ポートAデータ（ファイアボタン、オーバーレイ、LED）</span></div>
<div class="reg-row"><span class="reg-field">$BFE101</span><span class="reg-val">PRB — ポートBデータ（パラレルポートデータ）</span></div>
<div class="reg-row"><span class="reg-field">$BFE201</span><span class="reg-val">DDRA — データ方向レジスタA</span></div>
<div class="reg-row"><span class="reg-field">$BFE301</span><span class="reg-val">DDRB — データ方向レジスタB</span></div>
<div class="reg-row"><span class="reg-field">$BFE401</span><span class="reg-val">TALO — タイマーA下位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFE501</span><span class="reg-val">TAHI — タイマーA上位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFE601</span><span class="reg-val">TBLO — タイマーB下位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFE701</span><span class="reg-val">TBHI — タイマーB上位バイト</span></div>
<div class="reg-row"><span class="reg-field">$BFE801</span><span class="reg-val">TODLO — TODカウンタ下位（1/10秒）</span></div>
<div class="reg-row"><span class="reg-field">$BFE901</span><span class="reg-val">TODMID — TODカウンタ中位（秒）</span></div>
<div class="reg-row"><span class="reg-field">$BFEA01</span><span class="reg-val">TODHI — TODカウンタ上位（分）</span></div>
<div class="reg-row"><span class="reg-field">$BFEC01</span><span class="reg-val">SDR — シリアルデータレジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFED01</span><span class="reg-val">ICR — 割り込み制御レジスタ</span></div>
<div class="reg-row"><span class="reg-field">$BFEE01</span><span class="reg-val">CRA — 制御レジスタA</span></div>
<div class="reg-row"><span class="reg-field">$BFEF01</span><span class="reg-val">CRB — 制御レジスタB</span></div>
</div>

CIAは6800互換同期バスプロトコル（VPA̅/VMA̅/Eクロック経由）を使用するため、各CIAアクセスには約1.4 µsかかる――カスタムチップレジスタアクセスの約5倍遅い。

<!-- section: $000000 "カスタムチップレジスタ空間" -->

カスタムチップレジスタは**$DFF000–$DFF1FF**を占める――約200の個別レジスタを含む512バイト。多くはライトオンリー（68000は値を書き込めるが読み返せない）で、読み取りレジスタは同じアドレスで異なる機能を持つ。

レジスタはこの空間内でサブシステムごとに構成される：

<div class="register-block">
<div class="reg-title">レジスタ空間レイアウト</div>
<div class="reg-row"><span class="reg-field">$000–$03E</span><span class="reg-val">システム: BLTDDAT, DMACONR, VPOSR, VHPOSR, DSKDAT, JOYxDAT, CLXDAT, POTINP, SERDATR, DSKBYTR, INTENAR, INTREQR</span></div>
<div class="reg-row"><span class="reg-field">$040–$07E</span><span class="reg-val">Blitter: BLTCONx, BLTxPT, BLTxMOD, BLTxDAT, BLTSIZE, BLTxFWM/LWM</span></div>
<div class="reg-row"><span class="reg-field">$080–$08E</span><span class="reg-val">Copper: COPxLC, COPJMPx, COPCON, DIWSTRT, DIWSTOP</span></div>
<div class="reg-row"><span class="reg-field">$090–$09E</span><span class="reg-val">制御: DDFSTRx, DMACON, CLXCON, INTENA, INTREQ</span></div>
<div class="reg-row"><span class="reg-field">$0A0–$0DF</span><span class="reg-val">オーディオ: AUDxLCH/L, AUDxLEN, AUDxPER, AUDxVOL, AUDxDAT（×4チャネル）</span></div>
<div class="reg-row"><span class="reg-field">$0E0–$0FE</span><span class="reg-val">ビットプレーンポインタ: BPLxPTH/L（×6プレーン）</span></div>
<div class="reg-row"><span class="reg-field">$100–$10E</span><span class="reg-val">ビットプレーン制御: BPLCONx, BPLxMOD</span></div>
<div class="reg-row"><span class="reg-field">$110–$11E</span><span class="reg-val">ビットプレーンデータ: BPLxDAT</span></div>
<div class="reg-row"><span class="reg-field">$120–$17E</span><span class="reg-val">スプライト: SPRxPT, SPRxPOS, SPRxCTL, SPRxDATA, SPRxDATB（×8）</span></div>
<div class="reg-row"><span class="reg-field">$180–$1BE</span><span class="reg-val">カラー: COLOR00–COLOR31</span></div>
</div>

Copperは$040未満のレジスタへのアクセスが制限されている（DMAコントローラや自身の再プログラミングを防ぐため）。COPCONのビット0をセットすると、$040–$07E（Blitterレジスタ）へのアクセスも許可される。

<!-- section: $000000 "ROMとオーバーレイ" -->

AmigaのOS、**Kickstart**は**$FC0000–$FFFFFF**のROMに存在する（A500では256 KB）。電源投入時、68000はアドレス$000000からリセットベクタを読む。この時点でRAMはゴミデータを含んでいるため、Garyが**ROMオーバーレイ**を有効にし、Kickstart ROMを一時的に$000000にミラーする。CIA-Aのビット0（OVL）への最初の書き込みでオーバーレイは無効になり、$000000はChip RAMに戻り、ROMは$FC0000でのみ見える。

### A1000 WOM（Write-Once Memory）

A1000にはROMがなかった――代わりに**WOM**（Write-Once Memory）、$FC0000に256 KBのライトワンスRAMがあった。電源投入時、$F80000の小さなブートストラップROM（8 KB）がフロッピーディスクからKickstartをWOMにロードした。書き込み後、WOMは次のリセットまで変更できなかった。これによりA1000は異なるディスクからブートするだけで異なるKickstartバージョンを使用できた――後のモデルではよりシンプルで高速なマスクROMアプローチのために廃止された柔軟性である。
