---
title: "Amigaモデル一覧"
addr: "$A01000"
description: "Amigaハードウェアの全ラインナップ — A1000からA4000まで、チップセット世代、CPU、各モデルの変遷。"
order: 0
---

<!-- section: $A01000 "Amigaファミリー" -->

1985年から1994年の間に、Commodoreはホームコンピュータ、プロフェッショナルワークステーション、マルチメディアプレイヤー、ゲーム機にわたる十数種のAmigaモデルを生産した。多様性にもかかわらず、すべて同じ基本アーキテクチャを共有する：Motorola 680x0 CPUにグラフィックス、サウンド、DMAを処理するカスタムチップセットを組み合わせた構成。

ハードウェアは3世代のチップセットを経て進化した：

**OCS**（Original Chip Set、1985年）— Agnus、Denise、Paula。基礎：4096色パレット、画面上32色（HAMで4096色）、4チャネル8ビットオーディオ、ハードウェアスプライト、Copper、Blitter。

**ECS**（Enhanced Chip Set、1990年）— Fat Agnus / Super Agnus、Super Denise、Paula。段階的改良：1 MB Chip RAMアドレッシング、プロダクティビティ表示モード（1280×200等）、スプライト改良。

**AGA**（Advanced Graphics Architecture、1992年）— Alice、Lisa、Paula。大幅アップグレード：24ビットパレット（1670万色）、画面上最大256色（HAM8で262,144色）、2 MB Chip RAM、データパス拡大。

<!-- section: $A01000 "A1000 (1985)" -->

オリジナル。1985年7月にリンカーンセンターでデビー・ハリーのデモとともに発表されたA1000は、Jay Minerのビジョンの実現だった。

<div class="register-block">
<div class="reg-title">Amiga 1000</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1985年7月（米国）— $1,295</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.16 MHz（NTSC）</span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">OCS — Agnus (8361)、Denise (8362)、Paula (8364)</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">256 KB（512 KBまで拡張可能）</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val"><span class="highlight">WOM（Write-Once Memory）— 起動時にフロッピーからロード</span></span></div>
<div class="reg-row"><span class="reg-field">拡張</span><span class="reg-val">86ピンサイドカーバス</span></div>
<div class="reg-row"><span class="reg-field">ストレージ</span><span class="reg-val">3.5" DDフロッピー（880 KB）</span></div>
<div class="reg-row"><span class="reg-field">形状</span><span class="reg-val">セパレートキーボード付きデスクトップ</span></div>
</div>

A1000はROMを持たないユニークな設計だった——代わりに$F80000の8 KBブートストラップROMがフロッピーからKickstartを256 KBのWOM（Write-Once Memory）にロードした。書き込み後、WOMはリセットまで読み取り専用となった。これにより異なるKickstartバージョンをロードできたが、起動時間が長くなった。

オリジナルのAgnusは512 KBのChip RAMしかアドレスできなかった。A1000は256 KBで出荷され、内部的に512 KBまで拡張可能だった。フロントパネルの「ドーターボード」コネクタ（元々Amigaエンジニアリングチームの署名入り）が内部拡張パスを提供した。

A1000は1987年にA500とA2000に置き換えられて生産終了した。

<!-- section: $A01000 "A500 (1987)" -->

史上最も成功したAmiga。A500はAmigaを何百万もの家庭に届けた。特にヨーロッパではAtari STと並んでホームコンピュータ市場を支配した。

<div class="register-block">
<div class="reg-title">Amiga 500</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1987年1月 — $699（米国）、£499（英国）</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz（PAL）/ 7.16 MHz（NTSC）</span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">OCS — Fat Agnus (8375)、Denise (8362)、Paula (8364)</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val"><span class="highlight">512 KB（Fat Agnusアップグレードで1 MB）</span></span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">1.2または1.3 ROM（256 KB）</span></div>
<div class="reg-row"><span class="reg-field">拡張</span><span class="reg-val">86ピンサイド拡張 + トラップドアスロット（512 KB Slow RAM）</span></div>
<div class="reg-row"><span class="reg-field">ストレージ</span><span class="reg-val">3.5" DDフロッピー（880 KB）</span></div>
<div class="reg-row"><span class="reg-field">形状</span><span class="reg-val">キーボード一体型オールインワン</span></div>
</div>

A500は**Fat Agnus**（8375）を導入し、最大1 MBのChip RAMをアドレスできるようになった（標準では512 KBのみ搭載）。またKickstartをマスクROMに移し、A1000のフロッピー起動要件を排除した。

底面のトラップドア拡張スロットは512 KB RAMカードを受け入れたが、この「Slow RAM」は$C00000のチップバス上にあった——CPUのみアクセス可能だがDMA競合の影響を受け、真のFast RAMより遅くカスタムチップからもアクセスできなかった。

A500はAmigaのゲームとデモシーンの黄金時代を定義したプラットフォームだった。約600万台が販売された。

<!-- section: $A01000 "A2000 (1987)" -->

A500のプロフェッショナル版。同じコアハードウェアを内部拡張可能なビッグボックスデスクトップケースに収めた。

<div class="register-block">
<div class="reg-title">Amiga 2000</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1987年3月 — $1,495</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz</span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">OCS（後期リビジョン：ECS）</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">512 KB（ECSで1 MBまたは2 MBまで拡張可能）</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">1.2または1.3（後に2.04）</span></div>
<div class="reg-row"><span class="reg-field">拡張</span><span class="reg-val"><span class="highlight">Zorro IIスロット×5、ISA（PCブリッジボード）×2、CPUスロット×1、ビデオスロット×1</span></span></div>
<div class="reg-row"><span class="reg-field">ストレージ</span><span class="reg-val">3.5" DDフロッピー + 内蔵ハードドライブベイ</span></div>
<div class="reg-row"><span class="reg-field">形状</span><span class="reg-val">セパレートキーボード付きデスクトップ</span></div>
</div>

A2000の特徴はその**拡張アーキテクチャ**だった。Zorro IIスロットにより真のオートコンフィグ周辺機器——ハードドライブコントローラ、RAMボード、イーサネットカード、ビデオキャプチャデバイス——が使えた。専用CPUスロットは68020、68030、さらには68040プロセッサのアクセラレータボードを受け入れ、マシンを根本的に変身させた。

**ビデオスロット**によりゲンロックやビデオプロダクションハードウェアが使用可能になり、A2000は放送ビデオ作業の標準プラットフォームとなった——天気予報のグラフィックス、TVタイトリング、Video Toasterで有名。

**A2500**は68020または68030アクセラレータをプリインストールした派生モデル。

<!-- section: $A01000 "A3000 (1990)" -->

Commodoreの最も技術的に野心的なAmiga。完全な32ビット設計でアーキテクチャを前進させた。

<div class="register-block">
<div class="reg-title">Amiga 3000</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1990年6月 — $3,295（25 MHzモデル）</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val"><span class="highlight">Motorola 68030 @ 16または25 MHz + 68882 FPU</span></span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">ECS — Super Agnus、Super Denise、Paula</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">1 MB（2 MBまで拡張可能）</span></div>
<div class="reg-row"><span class="reg-field">Fast RAM</span><span class="reg-val">オンボード1 MB（16 MBまで拡張可能）</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">2.04（ROM）</span></div>
<div class="reg-row"><span class="reg-field">拡張</span><span class="reg-val">Zorro III（32ビット）スロット×4、CPUスロット×1</span></div>
<div class="reg-row"><span class="reg-field">ストレージ</span><span class="reg-val">3.5" DDフロッピー + 内蔵SCSIコントローラ</span></div>
<div class="reg-row"><span class="reg-field">形状</span><span class="reg-val">デスクトップ / タワー（A3000T）</span></div>
</div>

A3000は**Zorro III**を導入した——68030のフル帯域幅を活用できる32ビット拡張バス。また内蔵**SCSI**コントローラ（Amiga初）、ハードウェア**フリッカーフィクサー**（15 kHz出力をVGAモニタ用に31 kHzにスキャンダブリング）を搭載し、OS大改訂版の**AmigaOS 2.0**とともに出荷された。

ECSチップセットは**Super Denise**をもたらし、640×480インターレースやスーパーハイレゾ（1280×200）などのプロダクティビティモードを追加。**Super Agnus**は2 MB Chip RAMの完全アドレッシングに対応した。

**A3000T**（タワー版、1991年）はドライブベイと拡張スペースを増やした。希少な**A3000UX**バリアントはAmiga Unix（Amix）を実行し、AmigaをUnixワークステーションとして認定した。

<!-- section: $A01000 "A500+とA600 (1991–1992)" -->

OCSとAGAを橋渡しする2つの過渡的モデル。

<div class="register-block">
<div class="reg-title">Amiga 500+</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1991年11月</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz</span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">ECS — Fat Agnus (8375)、Super Denise (8373)、Paula</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">1 MB</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">2.04</span></div>
</div>

A500+はマイナーリビジョン——基本的にECSとKickstart 2.04を搭載したA500。短命で、数か月後にA600に置き換えられた。

<div class="register-block">
<div class="reg-title">Amiga 600</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1992年3月 — £399</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz</span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">ECS</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">1 MB（2 MBまで拡張可能）</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">2.05</span></div>
<div class="reg-row"><span class="reg-field">新機能</span><span class="reg-val"><span class="highlight">PCMCIAスロット、IDEインターフェース、小型フォームファクター</span></span></div>
</div>

A600は物議を醸した。A500より小型で安価だったが、テンキーとサイド拡張バスを削除した。主な追加は**PCMCIA Type IIスロット**（メモリカードとネットワーク用）と内蔵**IDEインターフェース**（2.5"ハードドライブ用）。しかしCPUスロットやZorro拡張がなく、アップグレードの行き止まりとなった。

コミュニティの多くはA600を失策と感じた——リソースはAGAマシンの早期市場投入に向けるべきだったと。

<!-- section: $A01000 "A1200 (1992)" -->

A500のAGA後継機——多くの人にとって最後の偉大なAmiga。

<div class="register-block">
<div class="reg-title">Amiga 1200</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1992年10月 — £399</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val"><span class="highlight">Motorola 68EC020 @ 14 MHz（32ビット、MMUなし）</span></span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">AGA — Alice、Lisa、Paula</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">2 MB</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">3.0（512 KB ROM）</span></div>
<div class="reg-row"><span class="reg-field">拡張</span><span class="reg-val">PCMCIA Type IIスロット、150ピントラップドア（CPUアクセラレータ）</span></div>
<div class="reg-row"><span class="reg-field">ストレージ</span><span class="reg-val">3.5" DDフロッピー + 内蔵IDE（2.5" HD）</span></div>
<div class="reg-row"><span class="reg-field">形状</span><span class="reg-val">キーボード一体型オールインワン</span></div>
</div>

A1200は世代を超えた飛躍だった。**AGAチップセット**がもたらしたもの：

- **24ビットカラーパレット** — 1670万色が利用可能（4096色から拡大）
- 標準モードで画面上**最大256色**を同時表示
- **HAM8** — 画面上262,144色（HAM6の4096色から拡大）
- **2 MB Chip RAM**、ビットプレーンデータ用の32ビットフェッチ拡張
- **Alice**がAgnusを置き換え、DMA改善と32ビットアドレッシング
- **Lisa**がDeniseを置き換え、拡張カラー機能

**68EC020**はコスト削減版68020——完全な32ビットデータ・アドレスバスだがMMUなし。14 MHzで動作し、ほとんどのタスクでA500の68000の約2.5倍の速度だった。

トラップドアスロットは68030、68040、さらには68060プロセッサとFast RAMのアクセラレータボードを受け入れ、A1200を1990年代後半まで現役に保った。Blizzard 1260アクセラレータ搭載のA1200は、当時の多くのPCを凌駕できた。

<!-- section: $A01000 "A4000 (1992)" -->

フラグシップAGAデスクトップ——Commodore最後のハイエンドAmiga。

<div class="register-block">
<div class="reg-title">Amiga 4000</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1992年10月</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val"><span class="highlight">Motorola 68040 @ 25 MHz（A4000/040）または68EC030 @ 25 MHz（A4000/030）</span></span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">AGA — Alice、Lisa、Paula</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">2 MB</span></div>
<div class="reg-row"><span class="reg-field">Fast RAM</span><span class="reg-val">オンボード4–16 MB（SIMMスロット）</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">3.0 / 3.1（512 KB ROM）</span></div>
<div class="reg-row"><span class="reg-field">拡張</span><span class="reg-val">Zorro IIIスロット×4、ISA×3、CPUスロット×1、ビデオスロット×1</span></div>
<div class="reg-row"><span class="reg-field">ストレージ</span><span class="reg-val">3.5" DD/HDフロッピー + 内蔵IDE（3.5" HD）</span></div>
<div class="reg-row"><span class="reg-field">形状</span><span class="reg-val">デスクトップ / タワー（A4000T）</span></div>
</div>

A4000はAGAチップセットに68040プロセッサを組み合わせた——オンチップFPUと命令/データキャッシュを持つ初のAmiga。ビデオプロダクションと3Dレンダリングを対象とした本格的なワークステーションクラスのマシンだった。

**A4000/030**バリアントはコスト削減オプションとして25 MHzの68EC030を使用。両バージョンともプロフェッショナル周辺機器用のZorro III拡張を備えていた。

**A4000T**（タワー、1994年）はCommodore最後のAmiga製品で、ドライブベイを増やしアクセスしやすい内部レイアウトを採用した。1994年4月のCommodore倒産前にごく少数のみが生産された。

<!-- section: $A01000 "CDTVとCD32 (1991–1993)" -->

既存のAmigaハードウェアをベースにした2つのマルチメディア/コンソール派生モデル。

<div class="register-block">
<div class="reg-title">CDTV (Commodore Dynamic Total Vision)</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1991年3月 — $999</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz</span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val">OCS</span></div>
<div class="reg-row"><span class="reg-field">RAM</span><span class="reg-val">1 MB Chip RAM</span></div>
<div class="reg-row"><span class="reg-field">メディア</span><span class="reg-val">CD-ROMドライブ + オプションフロッピー</span></div>
<div class="reg-row"><span class="reg-field">形状</span><span class="reg-val">赤外線リモコン付きセットトップボックス（黒）</span></div>
</div>

CDTVはVCRスタイルのケースにCD-ROMドライブを搭載したA500で、マルチメディアリビングルームデバイスとして位置付けられた。市場を数年先取りしており、販売は振るわなかった——消費者はまだマルチメディアプレイヤーの用途を理解していなかった。

<div class="register-block">
<div class="reg-title">Amiga CD32</div>
<div class="reg-row"><span class="reg-field">発売</span><span class="reg-val">1993年9月 — £299</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68EC020 @ 14 MHz</span></div>
<div class="reg-row"><span class="reg-field">チップセット</span><span class="reg-val"><span class="highlight">AGA — A1200と同じ</span></span></div>
<div class="reg-row"><span class="reg-field">RAM</span><span class="reg-val">2 MB Chip RAM</span></div>
<div class="reg-row"><span class="reg-field">メディア</span><span class="reg-val">2倍速CD-ROMドライブ</span></div>
<div class="reg-row"><span class="reg-field">追加</span><span class="reg-val">Akikoチップ（チャンキーからプラナーへの変換）</span></div>
<div class="reg-row"><span class="reg-field">形状</span><span class="reg-val">ゲームパッド付きゲーム機</span></div>
</div>

CD32は本質的にキーボードなしのA1200をゲーム機として製品化したもの。世界初の32ビットCD-ROMコンソールで、3DOやPlayStationに先駆けて市場に投入された。チャンキーからプラナーへのピクセルフォーマット変換をハードウェアで実行するカスタム**Akiko**チップを搭載——チャンキーピクセルフォーマットを使用するPCゲームの移植に有用だった。

CD32はヨーロッパでは好調に売れたが、Cad Trackの特許訴訟により米国市場からはブロックされた。1994年4月のCommodore倒産で生産終了。

<!-- section: $A01000 "チップセット比較" -->

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">機能</span>
<span class="bit-dir">OCS (1985)</span>
<span class="bit-name">ECS (1990)</span>
<span class="bit-func">AGA (1992)</span>
</div>
<div class="bit-row"><span class="bit-num">最大Chip RAM</span><span class="bit-dir">512 KB</span><span class="bit-name">2 MB</span><span class="bit-func">2 MB</span></div>
<div class="bit-row"><span class="bit-num">カラーパレット</span><span class="bit-dir">4,096</span><span class="bit-name">4,096</span><span class="bit-func">1670万</span></div>
<div class="bit-row"><span class="bit-num">画面上の色数</span><span class="bit-dir">32 (HAM: 4096)</span><span class="bit-name">32 (HAM: 4096)</span><span class="bit-func">256 (HAM8: 262,144)</span></div>
<div class="bit-row"><span class="bit-num">ローレゾ</span><span class="bit-dir">320×256</span><span class="bit-name">320×256</span><span class="bit-func">320×256</span></div>
<div class="bit-row"><span class="bit-num">ハイレゾ</span><span class="bit-dir">640×256</span><span class="bit-name">640×256</span><span class="bit-func">640×256</span></div>
<div class="bit-row"><span class="bit-num">スーパーハイレゾ</span><span class="bit-dir">—</span><span class="bit-name">1280×256</span><span class="bit-func">1280×256</span></div>
<div class="bit-row"><span class="bit-num">プロダクティビティ</span><span class="bit-dir">—</span><span class="bit-name">640×480i</span><span class="bit-func">640×480i</span></div>
<div class="bit-row"><span class="bit-num">ビットプレーン</span><span class="bit-dir">6</span><span class="bit-name">6</span><span class="bit-func">8</span></div>
<div class="bit-row"><span class="bit-num">スプライト</span><span class="bit-dir">8（3色）</span><span class="bit-name">8（3色）</span><span class="bit-func">8（15色）</span></div>
<div class="bit-row"><span class="bit-num">スプライト幅</span><span class="bit-dir">16px</span><span class="bit-name">16px</span><span class="bit-func">16/32/64px</span></div>
<div class="bit-row"><span class="bit-num">フェッチ幅</span><span class="bit-dir">16ビット</span><span class="bit-name">16ビット</span><span class="bit-func">32/64ビット</span></div>
<div class="bit-row"><span class="bit-num">Agnus</span><span class="bit-dir">8361/8370</span><span class="bit-name">8372/8375</span><span class="bit-func">Alice (8374)</span></div>
<div class="bit-row"><span class="bit-num">Denise</span><span class="bit-dir">8362</span><span class="bit-name">8373</span><span class="bit-func">Lisa (4203)</span></div>
<div class="bit-row"><span class="bit-num">Paula</span><span class="bit-dir">8364</span><span class="bit-name">8364</span><span class="bit-func">8364</span></div>
</div>

Paulaは3世代すべてで変更されなかった。オーディオサブシステム——4チャネルの8ビットDMA駆動PCM——はハードウェアでアップグレードされることがなく、競合プラットフォームで16ビットオーディオが標準になるにつれ、これは増大する制約となった。
