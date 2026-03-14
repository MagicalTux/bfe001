---
title: "スプライト"
addr: "$DFF120"
description: "Amigaスプライトシステム — 各3色の8つのハードウェアスプライトチャンネル、15色アタッチモード、DMA駆動の位置制御。"
order: 8
---

<!-- section: $DFF120 "スプライトの概要" -->

**スプライト**は、プレイフィールドの表示とは独立して画面上のどこにでも配置できるグラフィックオブジェクトである。各スプライトの幅は16ピクセルで、高さは表示ウィンドウの範囲内で自由に設定できる。通常、スプライトはプレイフィールドの手前に表示され、背後のグラフィックスを隠す。マウスポインタはその典型的な例である。

Amigaは**8つのハードウェアスプライトチャンネル**（SPR0〜SPR7）を提供する。各スプライトは透明色を含む4つの値のうち3色を表示できる。2つのスプライトを**アタッチペア**として結合すると、透明色を含む最大15色を実現できる。

### スプライトのカラー構成

スプライトの各ラインは2つの16ビットデータワードで構成され、2つのミニビットプレーンのように機能する。各ピクセルの色は、両ワードの対応するビットの組み合わせで決定される。

- 両ワードの**ビット15**が最も左のピクセルの色を決定
- 両ワードの**ビット0**が最も右のピクセルの色を決定
- 2ビットの組み合わせで4つの値を選択：`00`＝透明、`01`/`10`/`11`＝3色

スプライトには専用のカラーレジスタがない。代わりに、**カラーパレットの上位半分**（レジスタ16〜31）を共有する。スプライトはペアでグループ化され、各ペアが3つの連続するカラーレジスタを使用する。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">スプライトペア</span>
<span class="bit-dir">データビット</span>
<span class="bit-name">カラーレジスタ</span>
<span class="bit-func">備考</span>
</div>
<div class="bit-row"><span class="bit-num">0 & 1</span><span class="bit-dir">00</span><span class="bit-name">—</span><span class="bit-func">透明</span></div>
<div class="bit-row"><span class="bit-num">0 & 1</span><span class="bit-dir">01</span><span class="bit-name">COLOR17</span><span class="bit-func">$DFF1A2</span></div>
<div class="bit-row"><span class="bit-num">0 & 1</span><span class="bit-dir">10</span><span class="bit-name">COLOR18</span><span class="bit-func">$DFF1A4</span></div>
<div class="bit-row"><span class="bit-num">0 & 1</span><span class="bit-dir">11</span><span class="bit-name">COLOR19</span><span class="bit-func">$DFF1A6</span></div>
<div class="bit-row"><span class="bit-num">2 & 3</span><span class="bit-dir">00</span><span class="bit-name">—</span><span class="bit-func">透明</span></div>
<div class="bit-row"><span class="bit-num">2 & 3</span><span class="bit-dir">01</span><span class="bit-name">COLOR21</span><span class="bit-func">$DFF1AA</span></div>
<div class="bit-row"><span class="bit-num">2 & 3</span><span class="bit-dir">10</span><span class="bit-name">COLOR22</span><span class="bit-func">$DFF1AC</span></div>
<div class="bit-row"><span class="bit-num">2 & 3</span><span class="bit-dir">11</span><span class="bit-name">COLOR23</span><span class="bit-func">$DFF1AE</span></div>
<div class="bit-row"><span class="bit-num">4 & 5</span><span class="bit-dir">00</span><span class="bit-name">—</span><span class="bit-func">透明</span></div>
<div class="bit-row"><span class="bit-num">4 & 5</span><span class="bit-dir">01</span><span class="bit-name">COLOR25</span><span class="bit-func">$DFF1B2</span></div>
<div class="bit-row"><span class="bit-num">4 & 5</span><span class="bit-dir">10</span><span class="bit-name">COLOR26</span><span class="bit-func">$DFF1B4</span></div>
<div class="bit-row"><span class="bit-num">4 & 5</span><span class="bit-dir">11</span><span class="bit-name">COLOR27</span><span class="bit-func">$DFF1B6</span></div>
<div class="bit-row"><span class="bit-num">6 & 7</span><span class="bit-dir">00</span><span class="bit-name">—</span><span class="bit-func">透明</span></div>
<div class="bit-row"><span class="bit-num">6 & 7</span><span class="bit-dir">01</span><span class="bit-name">COLOR29</span><span class="bit-func">$DFF1BA</span></div>
<div class="bit-row"><span class="bit-num">6 & 7</span><span class="bit-dir">10</span><span class="bit-name">COLOR30</span><span class="bit-func">$DFF1BC</span></div>
<div class="bit-row"><span class="bit-num">6 & 7</span><span class="bit-dir">11</span><span class="bit-name">COLOR31</span><span class="bit-func">$DFF1BE</span></div>
</div>

スプライトはパレットエントリ16〜31をプレイフィールドと共有するため、プレイフィールドが16色以上を使用する場合にカラーの競合が発生する。`00`の組み合わせは常に透明であり、スプライトの背後にあるものが透けて見える。

<!-- section: $DFF120 "スプライトDMAとデータ構造" -->

AmigaのスプライトプログラミングはDMAコントローラが大部分の処理を行うため、比較的容易である。スプライトを表示するには、Chip RAMに**スプライトデータリスト**を準備し、対応するDMAチャンネルをそのアドレスに向ける。

8つのスプライトにはそれぞれ専用のDMAチャンネルがある。ただし、各チャンネルはラスタライン1本あたり**2ワードしか読めない**。これが、スプライトの幅が16ピクセルに制限され、4色までしか使えない根本的な理由である。毎ライン2ワードのデータが読み込まれるため、スプライトの高さは表示ウィンドウによってのみ制限される。

### スプライトデータリストの形式

スプライトデータリストはワードペアの連続である。各ペアは2つの**コントロールワード**（位置定義）か、2つの**データワード**（1ラインのピクセルデータ）のいずれかを含む。

<div class="addr-map">
<div class="addr-row">
<span class="addr-label">Start</span>
<span class="addr-body"><span class="addr-name">第1・第2コントロールワード</span><span class="addr-size">位置と高さ</span></span>
</div>
<div class="addr-row">
<span class="addr-label">Start+4</span>
<span class="addr-body"><span class="addr-name">第1・第2データワード — ライン1</span><span class="addr-size">ピクセルデータ</span></span>
</div>
<div class="addr-row">
<span class="addr-label">Start+8</span>
<span class="addr-body"><span class="addr-name">第1・第2データワード — ライン2</span><span class="addr-size">ピクセルデータ</span></span>
</div>
<div class="addr-row">
<span class="addr-label">...</span>
<span class="addr-body"><span class="addr-name">第1・第2データワード — ラインN</span><span class="addr-size">ピクセルデータ</span></span>
</div>
<div class="addr-row addr-highlight">
<span class="addr-label">Start+4*(N+1)</span>
<span class="addr-body"><span class="addr-name">$0000, $0000 — スプライトデータ終端</span><span class="addr-size">ターミネータ</span></span>
</div>
</div>

DMAコントローラはまずコントロールワードを読み取り、SPRxPOSおよびSPRxCTLレジスタに格納し、ビームが開始ライン（VSTART）に到達するまで待機する。その後、各ラインで2つのデータワードを読み込み、DENISEが正しい水平位置に16ピクセルを表示する。終了ライン（VSTOP）に達すると、次の2ワードが新しいコントロールワードとして解釈される。両方ともゼロであれば、DMAチャンネルは次のフレームまでアイドル状態になる。

### コントロールワードのレイアウト

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ワード</span>
<span class="bit-dir">ビット</span>
<span class="bit-name">名前</span>
<span class="bit-func">機能</span>
</div>
<div class="bit-row"><span class="bit-num">第1 (SPRxPOS)</span><span class="bit-dir">15–8</span><span class="bit-name">E7–E0</span><span class="bit-func">VSTART — 垂直開始位置（ビット7–0）</span></div>
<div class="bit-row"><span class="bit-num">第1 (SPRxPOS)</span><span class="bit-dir">7–0</span><span class="bit-name">H8–H1</span><span class="bit-func">HSTART — 水平開始位置（ビット8–1）</span></div>
<div class="bit-row"><span class="bit-num">第2 (SPRxCTL)</span><span class="bit-dir">15–8</span><span class="bit-name">L7–L0</span><span class="bit-func">VSTOP — 垂直終了位置（ビット7–0）</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">第2 (SPRxCTL)</span><span class="bit-dir">7</span><span class="bit-name">AT</span><span class="bit-func">アタッチ制御ビット（15色モード用）</span></div>
<div class="bit-row"><span class="bit-num">第2 (SPRxCTL)</span><span class="bit-dir">6–4</span><span class="bit-name">—</span><span class="bit-func">未使用（0）</span></div>
<div class="bit-row"><span class="bit-num">第2 (SPRxCTL)</span><span class="bit-dir">3</span><span class="bit-name">E8</span><span class="bit-func">VSTARTビット8</span></div>
<div class="bit-row"><span class="bit-num">第2 (SPRxCTL)</span><span class="bit-dir">2</span><span class="bit-name">L8</span><span class="bit-func">VSTOPビット8</span></div>
<div class="bit-row"><span class="bit-num">第2 (SPRxCTL)</span><span class="bit-dir">1</span><span class="bit-name">H0</span><span class="bit-func">HSTARTビット0</span></div>
</div>

水平・垂直の位置指定にはそれぞれ9ビットが使われる。垂直方向の解像度は1ラスタライン、水平方向の解像度はローレゾリューション1ピクセルで、プレイフィールドの解像度モードとは無関係である。

開始位置はスプライトの**左上隅**を定義する。VSTOPはスプライトの*後の*最初のライン（最終ライン+1）である。したがって、スプライトの高さは`VSTOP - VSTART`ラインとなる。

### 例：3色スプライト

```asm
Start:
dc.w $A05A,$A800   ; HSTART=$B4, VSTART=$A0, VSTOP=$A8
dc.w %0000000000000000,%0000001111000000  ; ライン1
dc.w %0000000000000000,%0000110000110000  ; ライン2
dc.w %0000000110000000,%0001100110011000  ; ライン3
dc.w %0000001111000000,%0011001001001100  ; ライン4
dc.w %0000001111000000,%0011001001001100  ; ライン5
dc.w %0000000110000000,%0001100110011000  ; ライン6
dc.w %0000000000000000,%0000110000110000  ; ライン7
dc.w %0000000000000000,%0000001111000000  ; ライン8
dc.w 0,0             ; スプライトデータ終端
```

<!-- section: $DFF120 "アタッチスプライト（15色モード）" -->

3色では不十分な場合、2つのスプライトを**アタッチ**して15色＋透明のスプライトを構成できる。各スプライトの2つのデータワードが結合され、ピクセルあたり4ビットの値となり、15のカラーレジスタを参照する（値`0000`は透明）。

アタッチを有効にするには、ペアの**奇数番号のスプライト**のSPRxCTLの**ATビット**（ビット7）をセットする。例えば、スプライト1にATをセットすると、そのデータビットがスプライト0のデータビットと結合される。

4ビットカラー値のビット順序：

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット位置</span>
<span class="bit-dir">重み</span>
<span class="bit-name">ソース</span>
<span class="bit-func">説明</span>
</div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">MSB</span><span class="bit-name">奇数スプライト、データワード2</span><span class="bit-func">最上位カラービット</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir"></span><span class="bit-name">奇数スプライト、データワード1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir"></span><span class="bit-name">偶数スプライト、データワード2</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">LSB</span><span class="bit-name">偶数スプライト、データワード1</span><span class="bit-func">最下位カラービット</span></div>
</div>

4つのスプライトペア（0&1、2&3、4&5、6&7）はすべてアタッチスプライトを形成でき、このモードではCOLOR16〜COLOR31を使用する。アタッチが正しく動作するには、2つのスプライトの**位置コントロールワードが一致**している必要がある。位置が一致しない場合、自動的に通常の3色モードに戻る。

### 1つのDMAチャンネルで複数のスプライト

スプライトの表示が完了すると、そのDMAチャンネルは再び空く。2つのゼロワードで終端する代わりに、データリストに**新しいコントロールワード**を記述して同じチャンネルで2つ目のスプライトを表示できる。

制限が1つある：DMAチャンネルは新しいコントロールワードを読み込むために**1ライン分の空白**が必要である。最初のスプライトの最終ラインがラスタライン163の場合、コントロールワードはライン164で読み込まれ、2番目のスプライトはライン165から開始する。

この技法により、DMAチャンネルを**多重化**してフレームあたり8つ以上のスプライトを表示できる。ただし、同じチャンネルを共有するスプライトが垂直方向に重ならないこと（1ラインの間隔を含む）が条件となる。

<!-- section: $DFF120 "スプライトレジスタ" -->

各スプライトチャンネルにはポインタレジスタペアと4つの制御/データレジスタがある。ポインタレジスタはDMAの初期化に使用され、制御・データレジスタは表示中にDMAコントローラによって自動的に書き込まれる。

<div class="register-block">
<div class="reg-title">スプライトポインタレジスタ（ライトオンリー）</div>
<div class="reg-row"><span class="reg-field">$DFF120</span><span class="reg-val">SPR0PTH — スプライト0データポインタ（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF122</span><span class="reg-val">SPR0PTL — スプライト0データポインタ（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF124</span><span class="reg-val">SPR1PTH — スプライト1データポインタ（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF126</span><span class="reg-val">SPR1PTL — スプライト1データポインタ（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF128</span><span class="reg-val">SPR2PTH — スプライト2データポインタ（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF12A</span><span class="reg-val">SPR2PTL — スプライト2データポインタ（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF12C</span><span class="reg-val">SPR3PTH — スプライト3データポインタ（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF12E</span><span class="reg-val">SPR3PTL — スプライト3データポインタ（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF130</span><span class="reg-val">SPR4PTH — スプライト4データポインタ（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF132</span><span class="reg-val">SPR4PTL — スプライト4データポインタ（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF134</span><span class="reg-val">SPR5PTH — スプライト5データポインタ（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF136</span><span class="reg-val">SPR5PTL — スプライト5データポインタ（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF138</span><span class="reg-val">SPR6PTH — スプライト6データポインタ（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF13A</span><span class="reg-val">SPR6PTL — スプライト6データポインタ（ビット0–15）</span></div>
<div class="reg-row"><span class="reg-field">$DFF13C</span><span class="reg-val">SPR7PTH — スプライト7データポインタ（ビット16–18）</span></div>
<div class="reg-row"><span class="reg-field">$DFF13E</span><span class="reg-val">SPR7PTL — スプライト7データポインタ（ビット0–15）</span></div>
</div>

DMAコントローラはこれらをスプライトデータリストへのランニングポインタとして使用する。リスト全体の処理が終わると、ポインタは末尾を超えた位置に進んでいる。次のフレームで同じスプライトを再表示するには、垂直帰線期間中にポインタを**再ロード**する必要がある。これは通常Copperが行う。

```asm
; Copperリスト：毎フレームのスプライトポインタ再ロード
MOVE #StartSprite0H,SPR0PTH
MOVE #StartSprite0L,SPR0PTL
MOVE #StartSprite1H,SPR1PTH
MOVE #StartSprite1L,SPR1PTL
; ... 全8チャンネル分繰り返す ...
WAIT $FFFE    ; Copperリスト終端
```

<div class="register-block">
<div class="reg-title">スプライト制御・データレジスタ</div>
<div class="reg-row"><span class="reg-field">SPRxPOS</span><span class="reg-val">第1コントロールワード — VSTART（ビット15–8）、HSTART（ビット7–0）</span></div>
<div class="reg-row"><span class="reg-field">SPRxCTL</span><span class="reg-val">第2コントロールワード — VSTOP（ビット15–8）、AT/E8/L8/H0（ビット7–0）</span></div>
<div class="reg-row"><span class="reg-field">SPRxDATA</span><span class="reg-val">現在のラインの第1データワード（下位ビットプレーン）</span></div>
<div class="reg-row"><span class="reg-field">SPRxDATB</span><span class="reg-val">現在のラインの第2データワード（上位ビットプレーン）</span></div>
</div>

SPRxCTLに値が書き込まれると（DMAまたは68000によって）、DENISEはそのスプライトの出力をビームがVSTARTに到達するまで無効にする。SPRxDATAへの書き込みでスプライト出力が再有効化される。

各スプライトDMAチャンネルの個別有効/無効制御は**存在しない**。DMAコンのSPRENビット（ビット5）が全8チャンネルを同時に有効化する。

```asm
MOVE.W #$8220,$DFF096   ; DMAコンでSPREN + DMAENをセット
```

使用しないスプライトチャンネルも初期化が必要で、SPRxPTを2つのゼロワードを含むメモリ位置に向ける。

<!-- section: $DFF120 "スプライト・プレイフィールド優先度" -->

スプライトとプレイフィールドが重なった場合、優先度システムがどの要素を手前に表示するかを決定する。スプライト同士では番号が優先度を決め、**スプライト0が最高優先度**を持つ。

スプライト対プレイフィールドの優先度では、スプライトはペアでグループ化される：0&1、2&3、4&5、6&7。プレイフィールドはこれら4ペアの間の任意の位置に挿入でき、各プレイフィールドに**5つの優先度位置**がある。

<div class="addr-map">
<div class="addr-row addr-highlight">
<span class="addr-label">位置 0</span>
<span class="addr-body"><span class="addr-name">PLF &gt; SPR0&1 &gt; SPR2&3 &gt; SPR4&5 &gt; SPR6&7</span><span class="addr-size">プレイフィールドが最前面</span></span>
</div>
<div class="addr-row">
<span class="addr-label">位置 1</span>
<span class="addr-body"><span class="addr-name">SPR0&1 &gt; PLF &gt; SPR2&3 &gt; SPR4&5 &gt; SPR6&7</span><span class="addr-size"></span></span>
</div>
<div class="addr-row">
<span class="addr-label">位置 2</span>
<span class="addr-body"><span class="addr-name">SPR0&1 &gt; SPR2&3 &gt; PLF &gt; SPR4&5 &gt; SPR6&7</span><span class="addr-size"></span></span>
</div>
<div class="addr-row">
<span class="addr-label">位置 3</span>
<span class="addr-body"><span class="addr-name">SPR0&1 &gt; SPR2&3 &gt; SPR4&5 &gt; PLF &gt; SPR6&7</span><span class="addr-size"></span></span>
</div>
<div class="addr-row addr-highlight">
<span class="addr-label">位置 4</span>
<span class="addr-body"><span class="addr-name">SPR0&1 &gt; SPR2&3 &gt; SPR4&5 &gt; SPR6&7 &gt; PLF</span><span class="addr-size">プレイフィールドが最背面</span></span>
</div>
</div>

これは**BPLCON2**レジスタ（$DFF104、ライトオンリー）で制御される。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func">説明</span>
</div>
<div class="bit-row"><span class="bit-num">15–7</span><span class="bit-dir">—</span><span class="bit-name">未使用</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">6</span><span class="bit-dir">PF2PRI</span><span class="bit-name">PF2優先度</span><span class="bit-func">セットすると、プレイフィールド2がプレイフィールド1の手前に表示</span></div>
<div class="bit-row"><span class="bit-num">5–3</span><span class="bit-dir">PF2P2–PF2P0</span><span class="bit-name">PF2スプライト優先度</span><span class="bit-func">スプライトペア間のプレイフィールド2の位置（0–4）</span></div>
<div class="bit-row"><span class="bit-num">2–0</span><span class="bit-dir">PF1P2–PF1P0</span><span class="bit-name">PF1スプライト優先度</span><span class="bit-func">スプライトペア間のプレイフィールド1の位置（0–4）</span></div>
</div>

デュアルプレイフィールドモードでは、各プレイフィールドをスプライトペア間に独立して配置できる。PF2PRIビットがどちらのプレイフィールドを手前にするかを制御し、プレイフィールド間の優先度はスプライト・プレイフィールド間の優先度に優先する。シングルプレイフィールドモードではPF1Pxのみが有効で、PF2PRIとPF2Pxは効果がない。

<!-- section: $DFF120 "衝突検出" -->

Amigaハードウェアは、スプライト同士やスプライトとプレイフィールド間の衝突を自動的に検出できる。衝突は、2つの要素の不透明ピクセルが同じ画面位置で重なったときに発生する。

### CLXDAT — 衝突データレジスタ（$DFF00E、リードオンリー）

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">衝突の対象</span>
<span class="bit-name"></span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">SPR4(5) と SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">SPR2(3) と SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">SPR2(3) と SPR4(5)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11</span><span class="bit-dir">SPR0(1) と SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">SPR0(1) と SPR4(5)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">SPR0(1) と SPR2(3)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">プレイフィールド2 と SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">プレイフィールド2 と SPR4(5)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">プレイフィールド2 と SPR2(3)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">プレイフィールド2 と SPR0(1)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">プレイフィールド1 と SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">プレイフィールド1 と SPR4(5)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">プレイフィールド1 と SPR2(3)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">プレイフィールド1 と SPR0(1)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">プレイフィールド1 と プレイフィールド2</span><span class="bit-name"></span><span class="bit-func"></span></div>
</div>

### CLXCON — 衝突制御レジスタ（$DFF098、ライトオンリー）

CLXCONレジスタは衝突検出に参加する要素を制御する。ENSPxビットは奇数番号スプライトの衝突テストを有効にする（偶数番号スプライトは常に参加）。ENBPx/MVBPxビットはプレイフィールド衝突をトリガーするビットプレーンカラーの組み合わせを選択する。

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">ビット</span>
<span class="bit-dir">名前</span>
<span class="bit-name">機能</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">15</span><span class="bit-dir">ENSP7</span><span class="bit-name">スプライト7の衝突を有効化</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">ENSP5</span><span class="bit-name">スプライト5の衝突を有効化</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">ENSP3</span><span class="bit-name">スプライト3の衝突を有効化</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">ENSP1</span><span class="bit-name">スプライト1の衝突を有効化</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11–6</span><span class="bit-dir">ENBP6–ENBP1</span><span class="bit-name">ビットプレーン比較を有効化</span><span class="bit-func">参加するビットプレーンを選択</span></div>
<div class="bit-row"><span class="bit-num">5–0</span><span class="bit-dir">MVBP6–MVBP1</span><span class="bit-name">ビットプレーンの一致値</span><span class="bit-func">衝突に必要なビット値</span></div>
</div>

ENBPxビットがセットされると、対応するビットプレーンのピクセル値がMVBPxビットと一致する必要がある。ENBPxがクリアの場合、そのビットプレーンは比較から除外される（任意の値で一致）。すべてのENBPxを0にすると、プレイフィールドのすべてのピクセルが衝突をトリガーできる。
