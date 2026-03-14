---
title: "Amiga テクニカルリファレンス — BFE001"
description: "Amigaのカスタムチップセット、メモリマップ、CIAレジスタの詳細 — $BFE001の裏にあるハードウェア。"
---

<!-- section: $DFF000 "チップセットアーキテクチャ" -->

Amigaの強さはカスタムチップセットにあった――3つの専用チップが
グラフィックス、サウンド、DMAをCPUから独立して処理していた。Motorola 68000が
コードを実行する一方で、カスタムチップがマシンを動かしていた。

<div class="pcb" id="pcb-board">
    <!-- Mounting holes -->
    <div class="pcb-hole" style="top:3%;left:1.5%"></div>
    <div class="pcb-hole" style="top:3%;right:1.5%"></div>
    <div class="pcb-hole" style="bottom:14%;left:1.5%"></div>
    <div class="pcb-hole" style="bottom:14%;right:1.5%"></div>
    <!-- Silkscreen -->
    <div class="pcb-silk" style="top:4%;left:4%;font-size:11px;color:rgba(220,210,180,0.25)">COMMODORE</div>
    <div class="pcb-silk" style="top:12%;left:4%;font-size:14px;color:rgba(220,210,180,0.3)">AMIGA A500</div>
    <div class="pcb-silk" style="top:4%;right:4%;font-size:9px">REV 6A · 1987</div>
    <!-- Bus traces (SVG) -->
    <svg class="pcb-traces" viewBox="0 0 1000 500" preserveAspectRatio="none">
        <line x1="570" y1="260" x2="520" y2="260" data-bus="cpu"/>
        <line x1="440" y1="250" x2="380" y2="250" data-bus="agnus"/>
        <line x1="440" y1="230" x2="280" y2="170" data-bus="rom"/>
        <line x1="350" y1="300" x2="440" y2="330" data-bus="denise"/>
        <line x1="350" y1="300" x2="440" y2="390" data-bus="paula"/>
        <line x1="280" y1="260" x2="180" y2="300" data-bus="ram"/>
        <line x1="520" y1="230" x2="720" y2="170" data-bus="cia-a"/>
        <line x1="520" y1="260" x2="720" y2="290" data-bus="cia-b"/>
        <line x1="750" y1="145" x2="750" y2="50" data-bus="cia-a"/>
        <line x1="440" y1="400" x2="40" y2="270" data-bus="floppy"/>
        <line x1="500" y1="330" x2="920" y2="380" data-bus="video"/>
        <line x1="500" y1="400" x2="920" y2="430" data-bus="audio"/>
    </svg>
    <!-- Chips -->
    <div class="pcb-chip" data-id="cpu" style="left:57%;top:22%;width:6%;height:32%">
        <span class="pcb-notch"></span>
        <span class="pcb-chip-label">68000</span>
        <span class="pcb-chip-sublabel">CPU</span>
    </div>
    <div class="pcb-chip plcc" data-id="agnus" style="left:28%;top:22%;width:14%;height:24%">
        <span class="pcb-pin-lr left"></span>
        <span class="pcb-pin-lr right"></span>
        <span class="pcb-chip-label">AGNUS</span>
        <span class="pcb-chip-sublabel">8375</span>
    </div>
    <div class="pcb-chip" data-id="gary" style="left:45%;top:22%;width:8%;height:12%">
        <span class="pcb-chip-label">GARY</span>
        <span class="pcb-chip-sublabel">5719</span>
    </div>
    <div class="pcb-chip" data-id="denise" style="left:44%;top:40%;width:9%;height:12%">
        <span class="pcb-chip-label">DENISE</span>
        <span class="pcb-chip-sublabel">8362</span>
    </div>
    <div class="pcb-chip" data-id="paula" style="left:44%;top:56%;width:9%;height:12%">
        <span class="pcb-chip-label">PAULA</span>
        <span class="pcb-chip-sublabel">8364</span>
    </div>
    <div class="pcb-chip star" data-id="cia-a" style="left:72%;top:14%;width:8%;height:12%">
        <span class="pcb-chip-label">CIA-A</span>
        <span class="pcb-chip-sublabel">$BFE001</span>
    </div>
    <div class="pcb-chip" data-id="cia-b" style="left:72%;top:30%;width:8%;height:12%">
        <span class="pcb-chip-label">CIA-B</span>
        <span class="pcb-chip-sublabel">$BFD000</span>
    </div>
    <div class="pcb-chip" data-id="rom" style="left:20%;top:14%;width:7%;height:14%">
        <span class="pcb-chip-label">ROM</span>
        <span class="pcb-chip-sublabel">KS 1.3</span>
    </div>
    <!-- RAM array -->
    <div class="pcb-ram" data-id="ram" style="left:6%;top:30%;width:16%;height:38%">
        <div class="pcb-ram-chip"></div><div class="pcb-ram-chip"></div>
        <div class="pcb-ram-chip"></div><div class="pcb-ram-chip"></div>
        <div class="pcb-ram-chip"></div><div class="pcb-ram-chip"></div>
        <div class="pcb-ram-chip"></div><div class="pcb-ram-chip"></div>
        <div class="pcb-ram-chip"></div><div class="pcb-ram-chip"></div>
        <div class="pcb-ram-chip"></div><div class="pcb-ram-chip"></div>
        <div class="pcb-ram-chip"></div><div class="pcb-ram-chip"></div>
        <div class="pcb-ram-chip"></div><div class="pcb-ram-chip"></div>
        <span class="pcb-ram-label">CHIP<br>RAM</span>
    </div>
    <!-- Connectors -->
    <div class="pcb-conn" data-id="kbd" style="left:28%;top:2%;width:22%;height:6%">
        <span class="pcb-conn-label">KEYBOARD</span>
    </div>
    <div class="pcb-conn" data-id="power" style="left:2%;top:6%;width:4%;height:6%">
        <span class="pcb-conn-label">PWR</span>
    </div>
    <div class="pcb-conn" data-id="floppy" style="left:2%;top:20%;width:3%;height:14%">
        <span class="pcb-conn-label" style="writing-mode:vertical-lr;font-size:6px">FDD</span>
    </div>
    <div class="pcb-conn" data-id="joy0" style="left:86%;top:4%;width:7%;height:6%">
        <span class="pcb-conn-label">JOY 0</span>
    </div>
    <div class="pcb-conn" data-id="joy1" style="left:86%;top:13%;width:7%;height:6%">
        <span class="pcb-conn-label">JOY 1</span>
    </div>
    <div class="pcb-conn" data-id="serial" style="left:88%;top:40%;width:7%;height:6%">
        <span class="pcb-conn-label">SER</span>
    </div>
    <div class="pcb-conn" data-id="parallel" style="left:88%;top:50%;width:7%;height:6%">
        <span class="pcb-conn-label">PAR</span>
    </div>
    <div class="pcb-conn" data-id="video" style="left:88%;top:62%;width:7%;height:6%">
        <span class="pcb-conn-label">VIDEO</span>
    </div>
    <div class="pcb-conn" data-id="audio" style="left:88%;top:72%;width:7%;height:5%">
        <span class="pcb-conn-label">AUD</span>
    </div>
    <div class="pcb-conn" data-id="exp" style="left:18%;top:82%;width:55%;height:5%">
        <span class="pcb-conn-label">EXPANSION / TRAPDOOR</span>
    </div>
    <!-- Info bar -->
    <div class="pcb-info" id="pcb-info">
        <span class="pcb-info-hint" id="pcb-hint">コンポーネントにホバーして詳細を表示</span>
        <span class="pcb-info-name" id="pcb-name" style="display:none"></span>
        <span class="pcb-info-addr" id="pcb-addr" style="display:none"></span>
        <span class="pcb-info-desc" id="pcb-desc" style="display:none"></span>
    </div>
</div>

<script>
(function() {
    var chips = {
        'cpu':    { name:'Motorola 68000', addr:'CPU · 7.09 MHz', desc:'16/32ビットCPU、24ビットアドレスバス（16 MB）。カスタムチップが他のすべてを処理する間、コードを実行する。DIP-64パッケージ。', bus:['cpu'] },
        'agnus':  { name:'Agnus (8375)', addr:'$DFF000', desc:'DMAコントローラ、Copperコプロセッサ、Blitterエンジン。全チップRAMアクセスを制御しDMAサイクルを管理。PLCC-84パッケージ。', bus:['agnus','ram'] },
        'gary':   { name:'Gary (5719)', addr:'アドレスデコーダ', desc:'CPUアクセスを適切なチップにルーティング——チップRAM、ファストRAM、ROM、CIA、カスタムレジスタ。静かなる交通整理役。', bus:['cpu','agnus','rom','cia-a','cia-b'] },
        'denise': { name:'Denise (8362)', addr:'$DFF000（共有）', desc:'映像出力：最大6ビットプレーン、8ハードウェアスプライト、衝突検出、HAMモードで4096色。', bus:['denise'] },
        'paula':  { name:'Paula (8364)', addr:'$DFF000（共有）', desc:'4チャンネル8ビットDMAオーディオ、フロッピーディスクコントローラ、シリアルポート、割り込み管理。音楽は自動再生。', bus:['paula','floppy','audio'] },
        'cia-a':  { name:'CIA-A (8520)', addr:'$BFE001 \u2605', desc:'キーボードシリアル入力、ゲームポート発射ボタン、電源LED（ビット1）、オーディオローパスフィルタ、オーバーレイビット、16ビットタイマー×2。', bus:['cia-a'] },
        'cia-b':  { name:'CIA-B (8520)', addr:'$BFD000', desc:'パラレルポートデータ、フロッピーディスク制御（モーター、方向、サイド、ステップ）、シリアルハンドシェイク、16ビットタイマー×2。', bus:['cia-b'] },
        'rom':    { name:'Kickstart ROM', addr:'$F80000', desc:'exec.library、intuition、graphicsを含むAmigaOSカーネル全体。256 KB（v1.3）。電源投入時にオーバーレイビットで$000000にマッピング。', bus:['rom'] },
        'ram':    { name:'チップRAM', addr:'$000000', desc:'標準512 KB（16×256Kbit DRAM）。CPUとカスタムチップの共有——グラフィックス、オーディオ、ディスクデータはここに格納。', bus:['ram'] },
        'kbd':    { name:'キーボード', addr:'CIA-Aシリアル', desc:'34ピンリボンケーブルで内蔵キーボードに接続。CIA-Aがハンドシェイク付きシリアルプロトコルでキーコードを読み取る。' },
        'floppy': { name:'フロッピードライブ', addr:'Paula + CIA-B', desc:'内蔵3.5インチ 880 KB DDドライブ。PaulaがディスクDMAを担当、CIA-Bがモーター・方向・サイド・ステップ信号を制御。' },
        'power':  { name:'電源入力', addr:'5V / 12V DC', desc:'外付け電源ブリック。A500のアイドル消費電力は約10W。内蔵電源なし——シンプルなDC入力コネクタ。' },
        'joy0':   { name:'ジョイスティック0', addr:'CIA-A / $DFF00A', desc:'DE-9コネクタ。CIA-A PRAビット6でアクティブローの発射ボタン。JOY0DAT（$DFF00A）で方向スイッチ。マウス互換。' },
        'joy1':   { name:'ジョイスティック1', addr:'CIA-A / $DFF00C', desc:'DE-9コネクタ。CIA-A PRAビット7でアクティブローの発射ボタン。JOY1DAT（$DFF00C）。プライマリゲームコントローラポート。' },
        'serial': { name:'シリアルポート', addr:'DB25 RS-232', desc:'Paulaが$DFF018/$DFF030でシリアルデータを処理。CIA-BがCTS/RTS/DSR/DTRハンドシェイクを管理。最大292 Kbaud。' },
        'parallel':{ name:'パラレルポート', addr:'CIA-A PRB', desc:'DB25 Centronics互換。CIA-AポートB（$BFE101）で8ビット双方向データ。CIA-BでアクティブローBUSY。' },
        'video':  { name:'ビデオ出力', addr:'DB23 RGB', desc:'Deniseからのアナログ RGB。PAL: 15 kHz水平 / 50 Hz垂直。Amigaモニタに直接接続。VGAにはスキャンダブラが必要。' },
        'audio':  { name:'オーディオ L/R', addr:'Paula DMA', desc:'ステレオRCA。チャンネル0+3=左、1+2=右。チップRAMからDMA駆動の8ビットオーディオ。CIA-Aビット1でローパスフィルタ切替。' },
        'exp':    { name:'拡張スロット', addr:'CPUバス', desc:'86ピンサイド拡張＋トラップドアスローRAMスロット。アクセラレータ、RAMカード、周辺機器用にフルCPUバスアクセスを提供。' }
    };
    var board = document.getElementById('pcb-board');
    var infoEl = document.getElementById('pcb-info');
    var hintEl = document.getElementById('pcb-hint');
    var nameEl = document.getElementById('pcb-name');
    var addrEl = document.getElementById('pcb-addr');
    var descEl = document.getElementById('pcb-desc');
    var traces = board.querySelectorAll('.pcb-traces line');
    var allItems = board.querySelectorAll('[data-id]');
    var pinned = null;

    function show(id) {
        var c = chips[id];
        if (!c) return;
        hintEl.style.display = 'none';
        nameEl.style.display = ''; nameEl.textContent = c.name;
        addrEl.style.display = ''; addrEl.textContent = c.addr;
        descEl.style.display = ''; descEl.textContent = c.desc;
        infoEl.classList.add('has-chip');
        traces.forEach(function(t) {
            t.classList.toggle('active', c.bus && c.bus.indexOf(t.dataset.bus) !== -1);
        });
    }

    function hide() {
        if (pinned) return;
        hintEl.style.display = '';
        nameEl.style.display = 'none';
        addrEl.style.display = 'none';
        descEl.style.display = 'none';
        infoEl.classList.remove('has-chip');
        traces.forEach(function(t) { t.classList.remove('active'); });
        allItems.forEach(function(el) { el.classList.remove('active'); });
    }

    allItems.forEach(function(el) {
        el.addEventListener('mouseenter', function() {
            if (!pinned) { show(el.dataset.id); el.classList.add('active'); }
        });
        el.addEventListener('mouseleave', function() {
            if (!pinned) { hide(); el.classList.remove('active'); }
        });
        el.addEventListener('click', function(e) {
            e.stopPropagation();
            if (pinned === el.dataset.id) {
                pinned = null; hide();
            } else {
                allItems.forEach(function(x) { x.classList.remove('active'); });
                pinned = el.dataset.id;
                el.classList.add('active');
                show(el.dataset.id);
            }
        });
    });

    board.addEventListener('click', function() {
        if (pinned) { pinned = null; hide(); }
    });
})();
</script>

**Agnus**（後にFat Agnus、さらにAlice）はカスタムチップセットの頭脳だった。
DMAを制御し――他のすべてのチップがCPUに負担をかけずにデータを
読み取れるようメモリアクセスを管理していた。Copper（映像ビームに同期して
ハードウェアレジスタを書き換えるプログラマブルコプロセッサ）とBlitter
（高速メモリコピー、線描画、塗りつぶし操作のためのハードウェアブロック転送
エンジン）を内蔵していた。

**Denise**（AGAチップセットではLisa）はすべての映像出力を担当していた：
ビットプレーングラフィックス、ハードウェアスプライト、衝突検出、カラーパレット。
Amigaはプレーナーグラフィックスモデルを採用していた――ピクセルをパックされた値として
格納するのではなく、ピクセルの色の各ビットが別々のビットプレーンから来ていた。
これによりスクロールや特定のエフェクトが非常に効率的だった。

**Paula**は4つの独立した8ビットオーディオチャンネル（ハードウェアボリュームと
周期制御付き）、ディスクドライブアクセス、シリアルI/O、割り込み管理を担当していた。
MOD音楽が機能したのは、PaulaのDMAチャンネルがチップRAMから波形サンプルを
連続的に読み取る一方でCPUが他の処理を行えたからだ――音楽は文字通り
自分自身で演奏していた。

**CIA 8520**（×2）――Complex Interface Adapter。CIA-Aは`$BFE001`に配置され、
キーボード、ゲームポートの発射ボタン、電源LED、オーディオローパスフィルタを
担当していた。CIA-Bは`$BFD000`に配置され、パラレルポート、シリアル制御線、
ディスクドライブ信号を担当していた。各CIAには2つの16ビットタイマーと
24ビット時刻カウンタが内蔵されていた。

<!-- section: $000000 "メモリマップ" -->

68000の24ビットアドレスバスは16 MBのアドレス空間を提供していた。すべての
ハードウェアには固有の場所があった。メモリアドレスへの書き込みは、マップ上の
位置に応じてデータの格納、グラフィックスの描画、サウンドの再生、
電源LEDの切り替えのいずれかを行った。

<div class="addr-map">
    <div class="addr-row">
        <span class="addr-label">$000000</span>
        <div class="addr-body">
            <div class="addr-name">チップRAM</div>
            <div class="addr-size">最大 2 MB</div>
            <div class="addr-comment">CPUとカスタムチップの共有; グラフィックス、オーディオ、ディスクデータ</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$200000</span>
        <div class="addr-body">
            <div class="addr-name">ファストRAM / Zorro II</div>
            <div class="addr-size">最大 8 MB</div>
            <div class="addr-comment">CPU専用、DMA競合なし</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$A00000</span>
        <div class="addr-body">
            <div class="addr-name addr-size">予約領域</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$BFD000</span>
        <div class="addr-body">
            <div class="addr-name">CIA-B レジスタ</div>
            <div class="addr-comment">パラレル、ディスク、シリアル制御</div>
        </div>
    </div>
    <div class="addr-row addr-highlight">
        <span class="addr-label">$BFE001</span>
        <div class="addr-body">
            <div class="addr-name">CIA-A レジスタ ★</div>
            <div class="addr-comment">キーボード、LED、フィルタ、ゲームポート</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$C00000</span>
        <div class="addr-body">
            <div class="addr-name">スローRAM</div>
            <div class="addr-size">512 KB（A500 トラップドア）</div>
            <div class="addr-comment">バス共有だがDMAアクセスなし</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$D00000</span>
        <div class="addr-body">
            <div class="addr-name addr-size">予約 / 拡張</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$DFF000</span>
        <div class="addr-body">
            <div class="addr-name">カスタムチップレジスタ</div>
            <div class="addr-size">約256レジスタ</div>
            <div class="addr-comment">Agnus, Denise, Paula — ビットプレーン、スプライト、色、DMA</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$E80000</span>
        <div class="addr-body">
            <div class="addr-name addr-size">Autoconfig / Zorro II ID</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$F80000</span>
        <div class="addr-body">
            <div class="addr-name">Kickstart ROM</div>
            <div class="addr-size">256–512 KB</div>
            <div class="addr-comment">exec.library, intuition, graphics — Amiga OSカーネル全体</div>
        </div>
    </div>
</div>

電源投入時、CIA-Aのオーバーレイビットがアドレス`$000000`にROMをマッピングし、
CPUがリセットベクタを読めるようにする。Kickstartが最初に行うことの一つが
このビットをクリアすることで、チップRAMを`$000000`に入れ替え、ROMを`$F80000`に
戻す。`exec.library`のジャンプテーブルアーキテクチャにより、ライブラリベースからの
固定された負のオフセットでOS関数を呼び出すことができた――何度も夜を費やして
探究した仕組みだ。

<!-- section: $BFE001 "CIA-Aレジスタ" -->

このサイトの名前の由来となったレジスタ。CIA-Aは`$BFE001`から始まる奇数
アドレスに配置されている（2つのCIAは奇数/偶数アドレスデコーディングを使って
同じ領域を競合なく共有している）。

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">方向</span>
        <span class="bit-name">名前</span>
        <span class="bit-func">$BFE001 — PRA — ポートレジスタA</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/FIR1</span>
        <span class="bit-func">ゲームポート1 発射ボタン（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/FIR0</span>
        <span class="bit-func">ゲームポート0 発射ボタン（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/RDY</span>
        <span class="bit-func">ディスクレディ（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/TK0</span>
        <span class="bit-func">ディスクトラックゼロ（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/WPRO</span>
        <span class="bit-func">ディスク書き込み禁止（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/CHNG</span>
        <span class="bit-func">ディスクチェンジ（アクティブロー）</span>
    </div>
    <div class="bit-row bit-highlight">
        <span class="bit-num">1</span>
        <span class="bit-dir">出力</span>
        <span class="bit-name">LED</span>
        <span class="bit-func">電源LED / オーディオローパスフィルタ</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">出力</span>
        <span class="bit-name">OVL</span>
        <span class="bit-func">オーバーレイ — 1: ROMを$000000に, 0: RAM</span>
    </div>
</div>

ビット1が主役。ローにするとパワーLEDが点灯し、オーディオローパスフィルタが
有効になる。Amiga 500ではハードな切り替えだった。Amiga 2000以降では
LEDがディミングし――フィルタは約3.3 kHz以上の周波数を滑らかに減衰させた。
ゲームではトンネルを通過するような効果をシミュレートするために切り替えていた：
こもった音はソフトウェア処理ではなく、ハードウェアフィルタによるものだった。

命令`MOVE.b #$02,$BFE001`はビット1をセットし他のすべてをクリアする
――LEDを消灯し、フィルタを無効にする。他のビットに影響を与えずにLEDだけを
切り替えるには、レジスタを読み取り、ビット1をXORし、書き戻す：
`BCHG #1,$BFE001`。

<div class="register-block">
    <div class="reg-title">CIA-A その他のレジスタ</div>
    <div class="reg-row">
        <span class="reg-field">$BFE101</span>
        <span class="reg-val">PRB — ポートB: パラレルポートデータ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE201</span>
        <span class="reg-val">DDRA — データ方向レジスタA（1 = 出力, 0 = 入力）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE301</span>
        <span class="reg-val">DDRB — データ方向レジスタB</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE401</span>
        <span class="reg-val">TALO — タイマーA ローバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE501</span>
        <span class="reg-val">TAHI — タイマーA ハイバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE601</span>
        <span class="reg-val">TBLO — タイマーB ローバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE701</span>
        <span class="reg-val">TBHI — タイマーB ハイバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE801</span>
        <span class="reg-val">イベントカウンタ（TOD） — ローバイト（1/10秒）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE901</span>
        <span class="reg-val">イベントカウンタ（TOD） — ミドルバイト（秒）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEA01</span>
        <span class="reg-val">イベントカウンタ（TOD） — ハイバイト（分）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFED01</span>
        <span class="reg-val">ICR — 割り込み制御レジスタ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEE01</span>
        <span class="reg-val">CRA — 制御レジスタA（タイマーAモード、シリアルポート）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEF01</span>
        <span class="reg-val">CRB — 制御レジスタB（タイマーBモード、TODアラーム）</span>
    </div>
</div>

キーボードはCIA-Aのシリアルポート機構を通じて読み取られた。キー押下ごとに
8ビットのスキャンコードが生成され、KDAT線を介してシリアルに送信され、
KCLKでクロックされた。CIAがビットをシフトインし、1バイト受信完了時に
割り込みを発生させた。Amigaのキーボードは独自のマイクロコントローラを搭載し、
ハンドシェイクプロトコルを実行していた――ホストは次のキーコードが送信される前に
各キーコードを確認応答する必要があった。
