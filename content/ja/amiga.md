---
title: "Amiga テクニカルリファレンス — BFE001"
description: "Amigaのカスタムチップセット、メモリマップ、CIAレジスタ、DMA、Copper、Blitter、オーディオ、シリアル、ディスクの完全なハードウェアリファレンス。"
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
    <!-- Bus traces — generated dynamically by JS -->
    <svg class="pcb-traces" viewBox="0 0 100 100" preserveAspectRatio="none"></svg>
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
        'cpu':    { name:'Motorola 68000', addr:'CPU · 7.09 MHz', desc:'16/32ビットCPU、24ビットアドレスバス（16 MB）。カスタムチップが他のすべてを処理する間、コードを実行する。DIP-64パッケージ。' },
        'agnus':  { name:'Agnus (8375)', addr:'$DFF000', desc:'DMAコントローラ、Copperコプロセッサ、Blitterエンジン。全チップRAMアクセスを制御しDMAサイクルを管理。PLCC-84パッケージ。' },
        'gary':   { name:'Gary (5719)', addr:'アドレスデコーダ', desc:'CPUアクセスを適切なチップにルーティング——チップRAM、ファストRAM、ROM、CIA、カスタムレジスタ。静かなる交通整理役。' },
        'denise': { name:'Denise (8362)', addr:'$DFF000（共有）', desc:'映像出力：最大6ビットプレーン、8ハードウェアスプライト、衝突検出、HAMモードで4096色。' },
        'paula':  { name:'Paula (8364)', addr:'$DFF000（共有）', desc:'4チャンネル8ビットDMAオーディオ、フロッピーディスクコントローラ、シリアルポート、割り込み管理。音楽は自動再生。' },
        'cia-a':  { name:'CIA-A (8520)', addr:'$BFE001 \u2605', desc:'キーボードシリアル入力、ゲームポート発射ボタン、電源LED（ビット1）、オーディオローパスフィルタ、オーバーレイビット、16ビットタイマー×2。' },
        'cia-b':  { name:'CIA-B (8520)', addr:'$BFD000', desc:'パラレルポートデータ、フロッピーディスク制御（モーター、方向、サイド、ステップ）、シリアルハンドシェイク、16ビットタイマー×2。' },
        'rom':    { name:'Kickstart ROM', addr:'$F80000', desc:'exec.library、intuition、graphicsを含むAmigaOSカーネル全体。256 KB（v1.3）。電源投入時にオーバーレイビットで$000000にマッピング。' },
        'ram':    { name:'チップRAM', addr:'$000000', desc:'標準512 KB（16×256Kbit DRAM）。CPUとカスタムチップの共有——グラフィックス、オーディオ、ディスクデータはここに格納。' },
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

    var wires = [
        ['gary','cpu','cpu'], ['gary','agnus','agnus'], ['gary','rom','rom'],
        ['gary','cia-a','cia-a'], ['gary','cia-b','cia-b'],
        ['agnus','denise','denise'], ['agnus','paula','paula'], ['agnus','ram','ram'],
        ['cia-a','kbd','cia-a'], ['paula','floppy','floppy'],
        ['denise','video','video'], ['paula','audio','audio'],
        ['cia-a','joy0','cia-a'], ['cia-a','joy1','cia-a'],
        ['cia-b','serial','cia-b'], ['cia-b','parallel','cia-b']
    ];

    var board = document.getElementById('pcb-board');
    var svg = board.querySelector('.pcb-traces');
    var infoEl = document.getElementById('pcb-info');
    var hintEl = document.getElementById('pcb-hint');
    var nameEl = document.getElementById('pcb-name');
    var addrEl = document.getElementById('pcb-addr');
    var descEl = document.getElementById('pcb-desc');
    var allItems = board.querySelectorAll('[data-id]');
    var pinned = null;

    function getCenter(id) {
        var el = board.querySelector('[data-id="'+id+'"]');
        if (!el) return null;
        var bw = board.offsetWidth, bh = board.offsetHeight;
        var r = el.getBoundingClientRect(), br = board.getBoundingClientRect();
        return {
            x: ((r.left - br.left + r.width/2) / bw) * 100,
            y: ((r.top - br.top + r.height/2) / bh) * 100
        };
    }

    function drawTraces() {
        while (svg.firstChild) svg.removeChild(svg.firstChild);
        wires.forEach(function(w) {
            var a = getCenter(w[0]), b = getCenter(w[1]);
            if (!a || !b) return;
            var line = document.createElementNS('http://www.w3.org/2000/svg','line');
            line.setAttribute('x1', a.x); line.setAttribute('y1', a.y);
            line.setAttribute('x2', b.x); line.setAttribute('y2', b.y);
            line.dataset.bus = w[2];
            svg.appendChild(line);
        });
    }

    drawTraces();
    window.addEventListener('resize', drawTraces);

    function busesFor(id) {
        var tags = [];
        wires.forEach(function(w) {
            if (w[0] === id || w[1] === id) tags.push(w[2]);
        });
        return tags;
    }

    function show(id) {
        var c = chips[id];
        if (!c) return;
        hintEl.style.display = 'none';
        nameEl.style.display = ''; nameEl.textContent = c.name;
        addrEl.style.display = ''; addrEl.textContent = c.addr;
        descEl.style.display = ''; descEl.textContent = c.desc;
        infoEl.classList.add('has-chip');
        var buses = busesFor(id);
        svg.querySelectorAll('line').forEach(function(t) {
            var match = false;
            wires.forEach(function(w) {
                if (w[2] === t.dataset.bus && (w[0] === id || w[1] === id)) match = true;
            });
            t.classList.toggle('active', match);
        });
        wires.forEach(function(w) {
            if (w[0] === id || w[1] === id) {
                var other = w[0] === id ? w[1] : w[0];
                var el = board.querySelector('[data-id="'+other+'"]');
                if (el) el.classList.add('connected');
            }
        });
    }

    function hide() {
        if (pinned) return;
        hintEl.style.display = '';
        nameEl.style.display = 'none';
        addrEl.style.display = 'none';
        descEl.style.display = 'none';
        infoEl.classList.remove('has-chip');
        svg.querySelectorAll('line').forEach(function(t) { t.classList.remove('active'); });
        allItems.forEach(function(el) { el.classList.remove('active'); el.classList.remove('connected'); });
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
                allItems.forEach(function(x) { x.classList.remove('active'); x.classList.remove('connected'); });
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

<!-- section: $BFD000 "CIA-Bレジスタ" -->

CIA-Bは`$BFD000`から始まる偶数アドレスに配置されている。CIA-Aがユーザー側
（キーボード、LED、ジョイスティックボタン）を向いていたのに対し、CIA-Bは
ペリフェラル側――パラレルポート、フロッピードライブ制御、シリアルハンドシェイク
を担当していた。

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">方向</span>
        <span class="bit-name">名前</span>
        <span class="bit-func">$BFD000 — PRA — ポートレジスタA</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/DTR</span>
        <span class="bit-func">シリアルDTR（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/RTS</span>
        <span class="bit-func">シリアルRTS（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/CD</span>
        <span class="bit-func">シリアルキャリアディテクト（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/CTS</span>
        <span class="bit-func">シリアルCTS（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/DSR</span>
        <span class="bit-func">シリアルDSR（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">出力</span>
        <span class="bit-name">/SEL</span>
        <span class="bit-func">外部フロッピードライブ選択（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">1</span>
        <span class="bit-dir">出力</span>
        <span class="bit-name">/SIDE</span>
        <span class="bit-func">ディスクサイド選択（0 = 上面、1 = 下面）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">出力</span>
        <span class="bit-name">/STEP</span>
        <span class="bit-func">ディスクステップパルス（アクティブロー）</span>
    </div>
</div>

CIA-BのポートB（`$BFD100`）はパラレルポートの8本のデータ線を搭載していた
――ビットごとに直接マッピングされていた。`$BFD300`のデータ方向レジスタが
各線の入力/出力を制御し、ポートを完全な双方向に対応させていた。

<div class="register-block">
    <div class="reg-title">CIA-B その他のレジスタ</div>
    <div class="reg-row">
        <span class="reg-field">$BFD100</span>
        <span class="reg-val">PRB — ポートB: パラレルポートデータ（8本の双方向データ線、直接マッピング）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD200</span>
        <span class="reg-val">DDRA — データ方向レジスタA</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD300</span>
        <span class="reg-val">DDRB — データ方向レジスタB</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD400</span>
        <span class="reg-val">TALO — タイマーA ローバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD500</span>
        <span class="reg-val">TAHI — タイマーA ハイバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD600</span>
        <span class="reg-val">TBLO — タイマーB ローバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD700</span>
        <span class="reg-val">TBHI — タイマーB ハイバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD800</span>
        <span class="reg-val">イベントカウンタ（TOD） — ローバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD900</span>
        <span class="reg-val">イベントカウンタ（TOD） — ミドルバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFDA00</span>
        <span class="reg-val">イベントカウンタ（TOD） — ハイバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFDD00</span>
        <span class="reg-val">ICR — 割り込み制御レジスタ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFDE00</span>
        <span class="reg-val">CRA — 制御レジスタA</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFDF00</span>
        <span class="reg-val">CRB — 制御レジスタB</span>
    </div>
</div>

2つのCIAはアドレス範囲`$A00000`〜`$BFFFFF`を巧妙なトリックで共有していた：
CIA-Aは奇数アドレスのみに応答し（68000の下位データバイトにバイト整列）、
CIA-Bは偶数アドレスのみに応答した。アドレスデコーダのGaryがアドレス線A12を
使って両者を選択し、チップセレクト信号を直接駆動していた。これにより、
両方に同時にアクセスすることは不可能だった。

各CIAの2つの16ビットタイマーは、ワンショットまたは連続モードで動作し、
システムクロックパルスや外部イベントをカウントし、アンダーフロー時に割り込みを
発生させることができた。24ビット時刻カウンタは電源周波数（PAL 50 Hz、
NTSC 60 Hz）でクロックされ、タイマーを消費せずにリアルタイムの計時を提供した。

<!-- section: $DFF096 "DMAシステム" -->

AmigaのDMAシステムがその性能の鍵だった。68000は一度に一つのことしかできなかったが、
カスタムチップはCPUの介入なしに25のDMAチャンネルを通じて独立にチップRAMの
読み書きを行えた――すべてAgnusが管理していた。

各走査線は227.5カラークロック（PAL）に分割されていた。各カラークロックには
偶数サイクルと奇数サイクルの2つのバスサイクルが含まれていた。Agnusはこれらの
サイクルを固定優先順位で異なるDMAチャンネルに割り当てた。CPUは残りの
サイクルを使用した。

<div class="register-block">
    <div class="reg-title">DMAチャンネル</div>
    <div class="reg-row">
        <span class="reg-field">ディスク</span>
        <span class="reg-val">走査線あたり3サイクル — フロッピーデータの読み書き</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">オーディオ</span>
        <span class="reg-val">4チャンネル、アクティブ時各1サイクル — 波形サンプルの取得</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ビットプレーン</span>
        <span class="reg-val">最大6チャンネル（ビットプレーンごとに1つ） — 表示データの取得</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">スプライト</span>
        <span class="reg-val">8チャンネル、スプライト行で各2サイクル — スプライト画像データの取得</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Copper</span>
        <span class="reg-val">命令ワードあたり1サイクル — Copperプログラム命令の取得</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Blitter</span>
        <span class="reg-val">最大4チャンネル（A、B、C、D） — バルクメモリ操作</span>
    </div>
</div>

優先順位（最高から）：ディスク、オーディオ、スプライト、ビットプレーン、
Copper、Blitter、CPU。実際には、通常のロー解像度表示でCPUは約40〜60%の
バスサイクルを使用できた。6ビットプレーンのハイレゾ表示は非常に多くのサイクルを
消費し、CPUは事実上枯渇した。

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">R/W</span>
        <span class="bit-name">名前</span>
        <span class="bit-func">DMACON $096 (W) / DMACONR $002 (R) — DMA制御</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">15</span>
        <span class="bit-dir">W</span>
        <span class="bit-name">SET/CLR</span>
        <span class="bit-func">1 = 指定ビットをセット、0 = 指定ビットをクリア</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">14</span>
        <span class="bit-dir">R</span>
        <span class="bit-name">BBUSY</span>
        <span class="bit-func">Blitterビジー（読み取り専用）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">13</span>
        <span class="bit-dir">R</span>
        <span class="bit-name">BZERO</span>
        <span class="bit-func">Blitterゼロ — 全出力ビットが0（読み取り専用）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">10</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">BLTPRI</span>
        <span class="bit-func">BlitterのCPUに対する優先権（ブリッターナスティ）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">9</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">DMAEN</span>
        <span class="bit-func">マスターDMAイネーブル — クリアで全DMA停止</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">8</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">BPLEN</span>
        <span class="bit-func">ビットプレーンDMAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">COPEN</span>
        <span class="bit-func">Copper DMAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">BLTEN</span>
        <span class="bit-func">Blitter DMAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">SPREN</span>
        <span class="bit-func">スプライトDMAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">DSKEN</span>
        <span class="bit-func">ディスクDMAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">AUD3EN</span>
        <span class="bit-func">オーディオチャンネル3 DMAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">AUD2EN</span>
        <span class="bit-func">オーディオチャンネル2 DMAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">1</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">AUD1EN</span>
        <span class="bit-func">オーディオチャンネル1 DMAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">AUD0EN</span>
        <span class="bit-func">オーディオチャンネル0 DMAイネーブル</span>
    </div>
</div>

DMAOCONはカスタムチップレジスタ全体で使われるSET/CLR方式を採用していた：
ビット15をセットして書き込むと指定ビットがオンになり、ビット15をクリアして
書き込むと指定ビットがオフになった。これにより、先にレジスタを読む必要がある
リード・モディファイ・ライトの競合状態を回避できた。例えば、
`MOVE.w #$8380,$DFF096`はマスターDMA、ビットプレーンDMA、Copper DMAを
他に影響を与えずに有効にした。

<!-- section: $DFF09A "割り込み" -->

Amigaは68000の7つの優先レベルにマッピングされた14の割り込みソースを持っていた。
Paulaが割り込み要求レジスタとイネーブルレジスタを管理し、すべてのソースを
CPUのIPL線に統合していた。

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">Level</span>
        <span class="bit-name">名前</span>
        <span class="bit-func">INTREQ $09C (W) / INTREQR $01E (R) — 割り込み要求</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">15</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">SET/CLR</span>
        <span class="bit-func">1 = 指定ビットをセット、0 = 指定ビットをクリア</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">14</span>
        <span class="bit-dir">6</span>
        <span class="bit-name">INTEN</span>
        <span class="bit-func">マスター割り込みイネーブル（割り込み発生にはセット必須）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">13</span>
        <span class="bit-dir">6</span>
        <span class="bit-name">EXTER</span>
        <span class="bit-func">CIA-B割り込み（CIA-Bの/INTからアクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">12</span>
        <span class="bit-dir">5</span>
        <span class="bit-name">DSKSYN</span>
        <span class="bit-func">ディスク同期ワード一致（DSKSYNCレジスタ）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">11</span>
        <span class="bit-dir">5</span>
        <span class="bit-name">RBF</span>
        <span class="bit-func">シリアルポート受信バッファフル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">10</span>
        <span class="bit-dir">4</span>
        <span class="bit-name">AUD3</span>
        <span class="bit-func">オーディオチャンネル3ブロック完了</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">9</span>
        <span class="bit-dir">4</span>
        <span class="bit-name">AUD2</span>
        <span class="bit-func">オーディオチャンネル2ブロック完了</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">8</span>
        <span class="bit-dir">4</span>
        <span class="bit-name">AUD1</span>
        <span class="bit-func">オーディオチャンネル1ブロック完了</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">4</span>
        <span class="bit-name">AUD0</span>
        <span class="bit-func">オーディオチャンネル0ブロック完了</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">3</span>
        <span class="bit-name">BLIT</span>
        <span class="bit-func">Blitter完了</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">3</span>
        <span class="bit-name">VERTB</span>
        <span class="bit-func">垂直ブランク（フレーム開始）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">3</span>
        <span class="bit-name">COPER</span>
        <span class="bit-func">Copper（Copperプログラムからトリガー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">2</span>
        <span class="bit-name">PORTS</span>
        <span class="bit-func">CIA-A割り込み（キーボード、TOD、タイマー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">1</span>
        <span class="bit-name">SOFT</span>
        <span class="bit-func">ソフトウェア割り込み（CPUによりセット）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">1</span>
        <span class="bit-dir">1</span>
        <span class="bit-name">DSKBLK</span>
        <span class="bit-func">ディスクブロック完了（DMA転送完了）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">1</span>
        <span class="bit-name">TBE</span>
        <span class="bit-func">シリアルポート送信バッファ空</span>
    </div>
</div>

INTENA（`$09A`書き込み、`$01C`読み取り）も同じレイアウトだった。割り込みが
実際にCPUに届くには、要求ビット*と*イネーブルビットの両方がセットされ、
さらにマスターイネーブル（ビット14）もセットされている必要があった。
割り込みハンドラは復帰前にINTREQの要求ビットをクリアしなければならず、
さもないと割り込みが即座に再トリガーされた。

レベル3（INT3）が主力だった――垂直ブランクは50 Hz（PAL）または60 Hz（NTSC）で
毎フレーム実行され、ゲームロジック、音楽再生、OSスケジューリングの標準的な
ハートビートとなった。Copper割り込みはプログラムが画面上の特定位置でコードを
トリガーすることを可能にし、分割画面エフェクトに不可欠だった。

<!-- section: $DFF080 "Copper" -->

CopperはAgnus内蔵のシンプルだが非常に強力なコプロセッサだった。命令は
MOVE、WAIT、SKIPの3つだけで、カスタムチップレジスタへの書き込みしかできなかった。
しかし映像ビームと同期して実行されるため、画面上の任意の位置でハードウェア状態を
変更することができた。

<div class="register-block">
    <div class="reg-title">Copper命令（各32ビット）</div>
    <div class="reg-row">
        <span class="reg-field">MOVE</span>
        <span class="reg-val">カスタムチップレジスタに値を書き込む。第1ワード：レジスタアドレス（下位9ビット、ビット0は常に0）。第2ワード：データ値。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">WAIT</span>
        <span class="reg-val">ビームが指定位置に到達するまで停止。第1ワード：VP[7:0] HP[7:1] 1。第2ワード：VPM[7:0] HPM[7:1] 0。マスクビットが比較精度を定義。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">SKIP</span>
        <span class="reg-val">ビームが指定位置を通過していたら次の命令をスキップ。WAITと同形式だが第2ワードのビット0 = 1。</span>
    </div>
</div>

CopperリストはチップRAMに格納されたプログラムだった。毎フレーム、Copperは
COP1LC（`$080`/`$082`）のアドレスからリスタートした。COPJMP1（`$088`）への
書き込みは強制的に即時リスタートさせた。COP2LC/COPJMP2は第2のエントリポイント
を提供し、通常は垂直ブランク割り込みでフレーム間のリスト切り替えに使用された。

典型的なCopperリストはディスプレイを設定した：ビットプレーンポインタのロード、
カラー設定、スクロールレジスタの構成。しかし真の魔法は画面途中の変更にあった。
特定のビーム位置を待ってから新しい値を書き込むことで、Copperは静的な
レジスタ設定では不可能なエフェクトを生み出せた――グラデーション空、分割解像度、
走査線ごとのカラーサイクリング、さらにはCPU時間をまったく消費しない
ラスターバーまで。

`WAIT $FFDFFFFE`命令は慣例的なリスト終了マーカーだった。ビーム位置V=255
H=223を待つが、PALでは可視領域を超えていた。Copperはそこで次の垂直ブランクが
リスタートさせるまで停滞した。

Copperが`$07F`より上のレジスタ（カラーレジスタやスプライトレジスタ）に
書き込むには、COPCON（`$02E`）のビット1――「Copper danger」ビット――をセット
する必要があった。これによりCopperがDMACONやディスクポインタなどの重要な
レジスタを誤って変更することを防いでいた。

<!-- section: $DFF100 "プレイフィールド" -->

Amigaはプレーナーグラフィックスモデルを採用していた。各ピクセルをパックされた
カラー値として格納する代わりに、画像は最大6つの**ビットプレーン**に分割された
――各ビットプレーンが各ピクセルのカラーインデックスの1ビットを保持した。
これによりスクロールや特定のBlitter操作が非常に効率的になったが、
ピクセル操作はより複雑になった。

<div class="register-block">
    <div class="reg-title">表示モード（BPLCON0 $100）</div>
    <div class="reg-row">
        <span class="reg-field">ロー解像度</span>
        <span class="reg-val">320×256（PAL）/ 320×200（NTSC）、最大6ビットプレーン = 64色（HAM）または32色</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ハイレゾ</span>
        <span class="reg-val">640×256 / 640×200、最大4ビットプレーン = 16色（HIRESビット15 = 1）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">HAM</span>
        <span class="reg-val">Hold-and-Modify — 6ビットプレーン、上位2ビットでhold/modify-R/modify-G/modify-Bを選択、下位4ビット = 値。画面上4096色。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">EHB</span>
        <span class="reg-val">Extra Half-Brite — 6ビットプレーン、プレーン6が32色パレットの半輝度版を選択。64色。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">デュアルPF</span>
        <span class="reg-val">Dual Playfield — 2つの独立した3ビットプレーンレイヤー（奇数プレーン = PF1、偶数プレーン = PF2）。透過付き8+8色。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">インターレース</span>
        <span class="reg-val">フィールド交互により垂直解像度を倍増（PAL 512ライン）。BPLCON0のLACEビット2。</span>
    </div>
</div>

ディスプレイウィンドウが可視領域を定義した。DIWSTRT（`$08E`）が開始位置、
DIWSTOP（`$090`）が終了位置を設定し、両方ともビーム座標で指定した。
標準的なPAL値は320×256表示で`$2C81`（開始）と`$2CC1`（終了）だった。

データフェッチタイミングはディスプレイウィンドウとは別だった。DDFSTRT（`$092`）
とDDFSTOP（`$094`）がAgnusのビットプレーンデータ読み取り開始・停止を制御した。
標準ロー解像度：DDFSTRT = `$0038`、DDFSTOP = `$00D0`。
ハイレゾ：DDFSTRT = `$003C`、DDFSTOP = `$00D4`。

各ビットプレーンにはポインタペア（BPL1PTH/Lの`$0E0`/`$0E2`から
BPL6PTH/Lの`$0EC`/`$0EE`まで）があり、チップRAM内のデータを指していた。
各走査線の後、ポインタにモジュロ値が加算された――BPL1MOD（`$108`）が
奇数ビットプレーン用、BPL2MOD（`$10A`）が偶数ビットプレーン用。モジュロを
表示幅に設定するとビットプレーンが自然にラップし、より大きな値を設定すると
行をスキップしてスムーズな垂直スクロールが可能になった。

水平スクロールはBPLCON1（`$102`）で実現された。プレイフィールド1と
プレイフィールド2に独立した4ビットの遅延値を保持していた。データフェッチ開始と
ビットプレーンポインタの調整と組み合わせることで、CPU負荷ゼロのピクセル精度
スムーズスクロールが可能になった――ハードウェアがすべてを処理した。

<div class="register-block">
    <div class="reg-title">カラーパレット — COLOR00〜COLOR31（$180〜$1BE）</div>
    <div class="reg-row">
        <span class="reg-field">フォーマット</span>
        <span class="reg-val">12ビットRGB: ---- RRRR GGGG BBBB。各コンポーネント0〜15。例：$0F00 = 赤、$0FFF = 白。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">COLOR00</span>
        <span class="reg-val">背景色 — ボーダーおよび全プレイフィールド/スプライトの背後に表示</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">スプライト色</span>
        <span class="reg-val">スプライト0〜1はCOLOR17〜19、スプライト2〜3はCOLOR21〜23、スプライト4〜5はCOLOR25〜27、スプライト6〜7はCOLOR29〜31を使用</span>
    </div>
</div>

<!-- section: $DFF120 "スプライト" -->

Amigaには8つのハードウェアスプライトがあり、各スプライトは幅16ピクセル、
高さは任意で、3色+透過をサポートした。プレイフィールドとは完全に独立しており、
DMAが設定されると、ビットプレーン表示の上（または後ろ）にDeniseが描画した
――CPUオーバーヘッドはゼロだった。

各スプライトにはポインタペア（SPR0PTH/Lの`$120`/`$122`から
SPR7PTH/Lの`$13E`/`$13F`まで）があり、チップRAM内のスプライトデータを指していた。
データフォーマットはシンプルだった：

<div class="register-block">
    <div class="reg-title">スプライトデータフォーマット（チップRAM内）</div>
    <div class="reg-row">
        <span class="reg-field">ワード0</span>
        <span class="reg-val">SPRxPOS — 垂直開始位置（ハイバイトにV7〜V0）、水平開始位置（ローバイトにH8〜H1）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ワード1</span>
        <span class="reg-val">SPRxCTL — 垂直終了位置（ハイバイトにV7〜V0）、制御ビット（attach、V8、H0）がローバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ワード2+3</span>
        <span class="reg-val">最初の画像ライン：DATAワード（プレーン0）+ DATBワード（プレーン1）。16ピクセル、ピクセルあたり2ビット。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">...</span>
        <span class="reg-val">追加各ラインごとにDATA/DATBペアを繰り返し</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">最終ペア</span>
        <span class="reg-val">2つのゼロワード（$0000 $0000）がスプライトデータの終了を示す</span>
    </div>
</div>

スプライトはペア（0+1、2+3、4+5、6+7）で**アタッチ**できた。アタッチ時、
2つのスプライトのビットプレーンを結合して4ビットプレーンの単一スプライトとなり、
15色+透過を実現した――代わりに8つの独立スプライトが4つに減少した。

プレイフィールドに対するスプライトの優先度はBPLCON2（`$104`）で制御された。
各スプライトペアは2つのプレイフィールドの前面、間、または背面に配置できた。
マウスポインタは伝統的にスプライト0だった。

<!-- section: $DFF040 "Blitter" -->

BlitterはAgnus内蔵のハードウェアブロック転送エンジンで、3つの操作のために設計
されていた：バルクメモリコピー（論理演算付き）、エリアフィル、線描画。
チップRAMの矩形領域を68000では不可能な速度で操作した。

Blitterには4つのDMAチャンネルがあった：**A**、**B**、**C**がデータソース、
**D**が出力先。処理される各ワードについて、3つのソース値がプログラマブルな
論理関数（*ミンターム*）を通じて結合され、出力が生成された。この単一の
メカニズムで、単純なコピーからクッキーカットスプライト描画まですべてを処理した。

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">名前</span>
        <span class="bit-func">BLTCON0 $040 — Blitter制御レジスタ0</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">15–12</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">ASH</span>
        <span class="bit-func">ソースAのシフト値（0〜15ビット右シフト）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">11</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">USEA</span>
        <span class="bit-func">DMAチャンネルAイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">10</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">USEB</span>
        <span class="bit-func">DMAチャンネルBイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">9</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">USEC</span>
        <span class="bit-func">DMAチャンネルCイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">8</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">USED</span>
        <span class="bit-func">DMAチャンネルDイネーブル（出力）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7–0</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">LF7–LF0</span>
        <span class="bit-func">ミンターム論理関数 — A、B、Cの256通りのブール組み合わせ</span>
    </div>
</div>

ミンタームバイトはブール関数を真理値表としてエンコードした：各ビット位置が
A、B、C入力の組み合わせに対応した。例えば、`$F0` = Aをコピー（D=A）、
`$CA` = クッキーカット（D=AC+BC̄、つまりCがセットの箇所はA、クリアの箇所はB）、
`$00` = ゼロクリア。

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">名前</span>
        <span class="bit-func">BLTCON1 $042 — Blitter制御レジスタ1</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">15–12</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">BSH</span>
        <span class="bit-func">ソースBのシフト値（0〜15ビット右シフト）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">EFE</span>
        <span class="bit-func">排他的フィルイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">IFE</span>
        <span class="bit-func">包含的フィルイネーブル</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">FCI</span>
        <span class="bit-func">フィルキャリー入力（初期フィル状態）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">1</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">DESC</span>
        <span class="bit-func">降順モード（右下から左上へ処理）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">LINE</span>
        <span class="bit-func">線描画モードイネーブル</span>
    </div>
</div>

Blitterの**エリアフィル**モードはラインごとに右から左へ動作した。キャリービットを
追跡し、入力のセットビットに遭遇するたびにトグルした――境界ピクセルペア間の
空間を塗りつぶした。包含的フィル（IFE）は出力に境界ピクセルを残し、
排他的フィル（EFE）はそれらを除去した。これにより塗りつぶしポリゴンの描画が
簡単になった：線描画モードでアウトラインを描き、単一のBlitterパスで塗りつぶす。

**線描画**はハードウェアBresenhamアルゴリズムを使用した。画面は8つのオクタント
（0〜7）に分割され、プログラマが線がどのオクタントに属するかを計算し、
BLTCON1のSUD/SUL/AULビットを設定した。Blitterは最大1024ピクセルの線を描画でき、
破線やテクスチャ線用の16ビット繰り返しパターンをオプションで使用できた。

BLTSIZE（`$058`）が操作を開始した：ビット15〜6が高さ（行数）、ビット5〜0が
幅（ワード単位）。BLTSIZEへの書き込みがBlitterをトリガーした――Blitter設定
シーケンスで常に最後に書き込むレジスタだった。

<!-- section: $DFF0A0 "オーディオ" -->

Paulaは4つの独立した8ビットPCMオーディオチャンネルをハードウェアDMA付きで
提供していた。チャンネル0と3は左スピーカー、チャンネル1と2は右スピーカーに
ルーティングされた。各チャンネルはチップRAMから波形サンプルを連続的に読み取り、
DACに供給した――音楽はCPUの介入なしに自動再生された。

<div class="register-block">
    <div class="reg-title">オーディオチャンネルレジスタ（×4 — チャンネル0を表示）</div>
    <div class="reg-row">
        <span class="reg-field">AUD0LC</span>
        <span class="reg-val">$0A0/$0A2 — チップRAM内の波形データポインタ（ハイ/ローワード）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">AUD0LEN</span>
        <span class="reg-val">$0A4 — ワード単位の長さ（DMAがこのワード数を転送後、AUD0LCから再開）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">AUD0PER</span>
        <span class="reg-val">$0A6 — 周期（サンプルレート）。小さいほど高ピッチ。周期 = クロック / (2 × 周波数)。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">AUD0VOL</span>
        <span class="reg-val">$0A8 — ボリューム：0（無音）〜64（最大）。6ビットリニアスケール。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">AUD0DAT</span>
        <span class="reg-val">$0AA — オーディオデータレジスタ（2サンプル、ハイバイトを先に再生）。非DMA再生用。</span>
    </div>
</div>

チャンネル1〜3はオフセット`$0B0`、`$0C0`、`$0D0`に同じレジスタを持っていた。

周期レジスタが再生レートを設定した。PALシステムクロックは3.546895 MHz。
サンプルレート8000 Hzの場合：周期 = 3546895 / (2 × 8000) ≈ 222。
最小実用周期は約124（28.6 kHz）だった――これ以下ではオーディオDMAが
バスサイクルを消費しすぎてディスプレイが枯渇した。

Paulaは**チャンネルモジュレーション**もサポートしていた：1つのチャンネルが
ペア内の次のチャンネルの周期やボリュームを変調できた（0→1、2→3）。
ADKCON（`$09E`）のビット4〜7がこれを制御した。周期変調はFMライクな合成エフェクト
を可能にし、ボリューム変調は振幅変調（トレモロ）を可能にした。これらのモードは
トラッカー音楽では稀にしか使われなかったが、一部のデモエフェクトで登場した。

チャンネルがバッファの再生を完了すると（AUDxLENワードを使い果たすと）、
オーディオ割り込み（INTREQのAUD0〜AUD3）がトリガーされた。割り込みハンドラは
次のバッファを設定でき、ダブルバッファリングのストリーミングオーディオを可能にした。
MODプレーヤーはこの仕組みを利用していた：各チャンネルの割り込みが次のサンプルセット
を指し、CPUはミキシングとエフェクトの適用に時間を費やした。

<!-- section: $DFF030 "シリアルポート" -->

PaulaにはシリアルLI通信用のUART（Universal Asynchronous Receiver/Transmitter）
が内蔵されていた。DB25 RS-232コネクタがデータ線を搬送し、CIA-Bがハンドシェイク
信号（DTR、RTS、CTS、DSR、CD）を処理した。

<div class="register-block">
    <div class="reg-title">UARTレジスタ</div>
    <div class="reg-row">
        <span class="reg-field">SERDAT $030</span>
        <span class="reg-val">送信データ（書き込み）。下位8ビットまたは9ビット = データ、上位ビット = ストップビット（1にセット）。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">SERDATR $018</span>
        <span class="reg-val">受信データ（読み取り）。ビット14: RBF（バッファフル）、ビット13: TBE（送信空）、ビット12: TSRE（シフトレジスタ空）、ビット11: RXDレベル、ビット0〜7: 受信データ。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">SERPER $032</span>
        <span class="reg-val">ボーレートとモード。ビット15: LONG（1 = 9ビットデータ）。ビット14〜0: 周期値。</span>
    </div>
</div>

ボーレートはSERPERを使って以下の式で設定された：

SERPER = (clock / baud_rate) − 1、ここでclock = 3,579,545 Hz。

9600ボー：SERPER = (3579545 / 9600) − 1 ≈ 372 = `$0174`。
4800ボー：SERPER ≈ 745 = `$02E9`。

UARTは2つの割り込みを生成した：TBE（送信バッファ空、INTREQビット0、レベル1）は
次のバイトの準備完了時、RBF（受信バッファフル、INTREQビット11、レベル5）は
バイト到着時に発生した。rawモード（ハンドシェイク無視）では、Amigaは約292 Kbaud
に達することができた――標準RS-232レートをはるかに超えていた。

<!-- section: $DFF020 "ディスクコントローラ" -->

Paulaのディスクコントローラは、DMAを使ってチップRAMとフロッピードライブ間で
生のMFMエンコードデータを転送した。CIA-Bが機械的信号（モーター、ステップ、方向、
サイド選択）を制御し、Paulaがデータストリームを処理した。

<div class="register-block">
    <div class="reg-title">ディスクレジスタ</div>
    <div class="reg-row">
        <span class="reg-field">DSKPTH/L</span>
        <span class="reg-val">$020/$022 — チップRAM内のディスクDMAバッファポインタ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKLEN $024</span>
        <span class="reg-val">ビット15: DMAEN（DMA開始）、ビット14: WRITE（1 = 書き込みモード）、ビット13〜0: ワード単位の転送長</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKSYNC $07E</span>
        <span class="reg-val">同期ワード — DMAは転送前にこのパターンを待つ（AmigaDOSでは通常$4489）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKBYTR $01A</span>
        <span class="reg-val">ディスクバイトとステータス（読み取り）。ビット15: BYTEREADY、ビット14: DMAON、ビット13: DSKWRITE、ビット12: WORDEQUAL、ビット7〜0: 生データバイト。</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKDAT $026</span>
        <span class="reg-val">ディスクDMA書き込みデータレジスタ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKDAT $008</span>
        <span class="reg-val">ディスクDMA読み取りデータレジスタ（先読み、CPUアクセス不可）</span>
    </div>
</div>

トラックを読むためのプログラミングシーケンス：

1. 書き込み操作が進行中でないことを確認（DSKLENを確認）
2. DSKPTH/Lをバッファアドレスに設定
3. DSKLENに転送長とDMAENセットを書き込む
4. DSKLENに再度書き込む（安全策：DMA開始に2回の書き込みが必要）
5. DSKBLK割り込み（INTREQビット1）を待つ
6. DSKLENをクリアしてDMAを停止

書き込みシーケンスにはさらなる安全策があった：WRITEビットをセットしたDSKLENを
2回書き込まないと書き込みが始まらなかった。これにより、1回の誤書き込みによる
ディスク破壊を防いだ。

ADKCON（`$09E`）はMFM/GCRエンコーディング選択、プリコンペンセーションタイミング、
およびDSKSYNC内の同期ワードを待ってから転送を開始するWORDSYNCモードを制御した。
標準Amiga 3.5インチDDドライブはディスクあたり880 KBを格納：80トラック × 2面 ×
11セクタ × 512バイト。

<!-- section: $KBD001 "キーボード" -->

Amiga 500のキーボードには独自の6500/1マイクロコントローラが内蔵されていた。
キーマトリックスを独立にスキャンし、2線プロトコルでCIA-Aに8ビットスキャンコードを
シリアル送信した：KDAT（データ）とKCLK（クロック）。

プロトコルはアクティブロー、MSBファーストだった。完全な1バイトを送信した後、
キーボードはホストがKDATを最低85マイクロ秒ローに引くことで確認応答するのを待った。
このハンドシェイクなしでは、キーボードは次のキーコードを送信しなかった
――組み込みのフロー制御を提供していた。

スキャンコードのフォーマット：ビット7 = 0でキー押下、1でキーリリース。
ビット6〜0がキーIDをエンコード。生コードは位置ベースであり、ASCIIではなかった
――OSのキーマップが文字に変換した。

特殊な3キーの組み合わせ（Ctrl + Amiga + Amiga）は/KBRESET線をアサートして
ハードリセットをトリガーした。この線はシステムリセット回路に直接配線されていた。
これはすべてのAmigaユーザーが本能的に知っていた悪名高い「三本指の敬礼」だった。

キーボードは電源投入時キーストリームも送信した：起動時のコードシーケンスで、
キーボードの種類と言語レイアウトを識別した。システムはタイムアウト期間内に
このストリームをチェックすることでキーボードの存在を検出できた。

<!-- section: $CONN01 "コネクタ" -->

Amiga 500の背面パネルには、それぞれ特定の目的を持つコネクタが並んでいた：

<div class="register-block">
    <div class="reg-title">ビデオ — DB23メス（アナログRGB）</div>
    <div class="reg-row">
        <span class="reg-field">ピン3,4,5</span>
        <span class="reg-val">赤、緑、青 — Deniseからのアナログ0〜0.7V</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン10</span>
        <span class="reg-val">/CSYNC — 複合同期</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン11</span>
        <span class="reg-val">/HSYNC — 水平同期</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン12</span>
        <span class="reg-val">/VSYNC — 垂直同期</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">タイミング</span>
        <span class="reg-val">水平15.625 kHz（PAL）、垂直50 Hz。Amigaモニタに直接接続可能。VGAにはスキャンダブラが必要。</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">ジョイスティック / マウス — DE-9オス（×2）</div>
    <div class="reg-row">
        <span class="reg-field">ピン1〜4</span>
        <span class="reg-val">上、下、左、右（直接アクティブなデジタルスイッチ、JOY0DAT $00A / JOY1DAT $00Cで読み取り）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン6</span>
        <span class="reg-val">発射ボタン — アクティブロー、CIA-A PRAビット6（ポート0）またはビット7（ポート1）で読み取り</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン5</span>
        <span class="reg-val">中ボタン / pot X — アクティブローデジタルまたは比例アナログ入力（POT0DAT / POT1DATで読み取り）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン9</span>
        <span class="reg-val">右ボタン / pot Y — ピン5と同様</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン7</span>
        <span class="reg-val">+5V電源供給（最大50 mA）</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">シリアル — DB25オス（RS-232）</div>
    <div class="reg-row">
        <span class="reg-field">ピン2</span>
        <span class="reg-val">TXD — 送信データ（Paulaから）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン3</span>
        <span class="reg-val">RXD — 受信データ（Paulaへ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン4〜6,8,20</span>
        <span class="reg-val">ハンドシェイク信号（RTS、CTS、DSR、CD、DTR） — CIA-BポートAが管理</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン7</span>
        <span class="reg-val">信号グラウンド</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">パラレル — DB25メス（Centronics互換）</div>
    <div class="reg-row">
        <span class="reg-field">ピン2〜9</span>
        <span class="reg-val">データビット0〜7 — CIA-AポートB（$BFE101）経由で双方向</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン1</span>
        <span class="reg-val">/STROBE — アクティブロー出力パルス</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン11</span>
        <span class="reg-val">BUSY — アクティブロー、CIA-B Flag入力で直接読み取り</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン12</span>
        <span class="reg-val">POUT — 用紙切れ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン13</span>
        <span class="reg-val">/SEL — プリンタ選択</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">フロッピー — DB23オス（アクティブロー制御、Amiga専用ピン配置）</div>
    <div class="reg-row">
        <span class="reg-field">ピン2</span>
        <span class="reg-val">/CHNG — ディスクチェンジ（CIA-A PRAビット2へ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン3</span>
        <span class="reg-val">/INDEX — 1回転につき1回</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン5</span>
        <span class="reg-val">/TK0 — トラックゼロ（CIA-A PRAビット4へ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン6</span>
        <span class="reg-val">/WPRO — ライトプロテクト（CIA-A PRAビット3へ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン7</span>
        <span class="reg-val">/RDY — ドライブレディ（CIA-A PRAビット5へ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン15,18,19</span>
        <span class="reg-val">/MTR、/DIR、/STEP — モーター、方向、ステップ（CIA-Bから）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン13</span>
        <span class="reg-val">/SEL0 — ドライブ0選択（アクティブロー、CIA-B PRAビット3から）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">ピン14</span>
        <span class="reg-val">/SIDE — サイド選択（CIA-B PRAビット2から）</span>
    </div>
</div>

底面の86ピン拡張コネクタは68000のアドレスバスとデータバス、制御信号への完全な
アクセスを提供した。アクセラレータボード、RAM拡張、CPUバスへの直接アクセスが
必要なあらゆるアドオンへのゲートウェイだった。それはAmigaの最大の強みであり
限界でもあった――完全にオープン、完全に無防備、完全に信頼ベース。

<!-- section: $DFF1FE "カスタムチップレジスタマップ" -->

すべてのカスタムチップレジスタは`$DFF000`から始まる512バイトのブロックに
格納されていた。オフセット（例：DMAOCONの`$096`）がこのベースアドレスに加算された。
レジスタは読み取り専用（R）、書き込み専用（W）、またはストローブ（S ——
任意の値の書き込みでアクションがトリガーされる）のいずれかだった。一部のレジスタ
ペアは読み取りと書き込みで異なる値のためにアドレスを共有していた。

<div class="register-block">
    <div class="reg-title">DMA &amp; 制御</div>
    <div class="reg-row">
        <span class="reg-field">$002 R</span>
        <span class="reg-val">DMACONR — DMA制御読み取り</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$096 W</span>
        <span class="reg-val">DMACON — DMA制御書き込み（SET/CLR）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$01C R</span>
        <span class="reg-val">INTENAR — 割り込みイネーブル読み取り</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$09A W</span>
        <span class="reg-val">INTENA — 割り込みイネーブル書き込み（SET/CLR）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$01E R</span>
        <span class="reg-val">INTREQR — 割り込み要求読み取り</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$09C W</span>
        <span class="reg-val">INTREQ — 割り込み要求書き込み（SET/CLR）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$010 R</span>
        <span class="reg-val">ADKCONR — オーディオ/ディスク制御読み取り</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$09E W</span>
        <span class="reg-val">ADKCON — オーディオ/ディスク制御書き込み（SET/CLR）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$004 R</span>
        <span class="reg-val">VPOSR — ビーム位置（V8とフレームID）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$006 R</span>
        <span class="reg-val">VHPOSR — ビーム位置（V7〜V0、H8〜H1）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$02A W</span>
        <span class="reg-val">VPOSW — ビーム位置書き込み（ゲンロック同期用）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$02C W</span>
        <span class="reg-val">VHPOSW — ビーム位置書き込み</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$02E W</span>
        <span class="reg-val">COPCON — Copper制御（ビット1 = Copper danger）</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Copper</div>
    <div class="reg-row">
        <span class="reg-field">$080 W</span>
        <span class="reg-val">COP1LCH — Copperリスト1ポインタ（ハイ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$082 W</span>
        <span class="reg-val">COP1LCL — Copperリスト1ポインタ（ロー）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$084 W</span>
        <span class="reg-val">COP2LCH — Copperリスト2ポインタ（ハイ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$086 W</span>
        <span class="reg-val">COP2LCL — Copperリスト2ポインタ（ロー）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$088 S</span>
        <span class="reg-val">COPJMP1 — Copperをリスト1でリスタート</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$08A S</span>
        <span class="reg-val">COPJMP2 — Copperをリスト2でリスタート</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$08C W</span>
        <span class="reg-val">COPINS — Copper命令フェッチ（デバッグ）</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">ビットプレーン</div>
    <div class="reg-row">
        <span class="reg-field">$100 W</span>
        <span class="reg-val">BPLCON0 — ビットプレーン制御（プレーン数、HIRES、HAM、DPF）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$102 W</span>
        <span class="reg-val">BPLCON1 — スクロール値（PF1、PF2の水平遅延）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$104 W</span>
        <span class="reg-val">BPLCON2 — スプライト/プレイフィールド優先度</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$108 W</span>
        <span class="reg-val">BPL1MOD — 奇数ビットプレーンモジュロ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$10A W</span>
        <span class="reg-val">BPL2MOD — 偶数ビットプレーンモジュロ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$0E0–$0EE W</span>
        <span class="reg-val">BPL1PT〜BPL6PT — ビットプレーン1〜6ポインタ（ハイ/ローワードペア）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$110–$11E R</span>
        <span class="reg-val">BPL1DAT〜BPL6DAT — ビットプレーンデータ（DMAからラッチ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$08E W</span>
        <span class="reg-val">DIWSTRT — ディスプレイウィンドウ開始（V7〜V0、H7〜H0）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$090 W</span>
        <span class="reg-val">DIWSTOP — ディスプレイウィンドウ終了（V7〜V0、H7〜H0）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$092 W</span>
        <span class="reg-val">DDFSTRT — データフェッチ開始</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$094 W</span>
        <span class="reg-val">DDFSTOP — データフェッチ終了</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">スプライト（$120〜$17E）</div>
    <div class="reg-row">
        <span class="reg-field">$120–$13E W</span>
        <span class="reg-val">SPR0PT〜SPR7PT — スプライト0〜7 DMAポインタ（ハイ/ローペア）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$140–$17E W</span>
        <span class="reg-val">SPR0POS/CTL/DATA/DATB 〜 SPR7POS/CTL/DATA/DATB — 位置、制御、画像データ</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Blitter</div>
    <div class="reg-row">
        <span class="reg-field">$040 W</span>
        <span class="reg-val">BLTCON0 — Blitter制御0（シフト、イネーブル、ミンターム）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$042 W</span>
        <span class="reg-val">BLTCON1 — Blitter制御1（Bシフト、フィル、ラインモード）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$044 W</span>
        <span class="reg-val">BLTAFWM — ソースA最初のワードマスク</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$046 W</span>
        <span class="reg-val">BLTALWM — ソースA最後のワードマスク</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$048–$056 W</span>
        <span class="reg-val">BLTCPT、BLTBPT、BLTAPT、BLTDPT — ソースC/B/Aと出力先Dのポインタ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$058 W</span>
        <span class="reg-val">BLTSIZE — Blitter開始（高さ × 64 + ワード単位の幅）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$060–$066 W</span>
        <span class="reg-val">BLTCMOD、BLTBMOD、BLTAMOD、BLTDMOD — C/B/A/Dのモジュロ値</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$070–$076 W</span>
        <span class="reg-val">BLTCDAT、BLTBDAT、BLTADAT — C/B/Aのデータレジスタ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$000 R</span>
        <span class="reg-val">BLTDDAT — Blitter出力先データ（先読み）</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">オーディオ</div>
    <div class="reg-row">
        <span class="reg-field">$0A0–$0AE</span>
        <span class="reg-val">AUD0 — LC（ポインタ）、LEN（長さ）、PER（周期）、VOL（ボリューム）、DAT（データ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$0B0–$0BE</span>
        <span class="reg-val">AUD1 — 同レイアウト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$0C0–$0CE</span>
        <span class="reg-val">AUD2 — 同レイアウト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$0D0–$0DE</span>
        <span class="reg-val">AUD3 — 同レイアウト</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">ディスク</div>
    <div class="reg-row">
        <span class="reg-field">$020/$022 W</span>
        <span class="reg-val">DSKPT — ディスクDMAポインタ（ハイ/ロー）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$024 W</span>
        <span class="reg-val">DSKLEN — DMA長と制御</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$026 W</span>
        <span class="reg-val">DSKDAT — ディスクDMAデータ書き込み</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$008 R</span>
        <span class="reg-val">DSKDATR — ディスクDMAデータ読み取り（先読み）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$01A R</span>
        <span class="reg-val">DSKBYTR — ディスクバイトとステータス</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$07E W</span>
        <span class="reg-val">DSKSYNC — ディスク同期パターン</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">シリアル</div>
    <div class="reg-row">
        <span class="reg-field">$018 R</span>
        <span class="reg-val">SERDATR — シリアル受信データとステータス</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$030 W</span>
        <span class="reg-val">SERDAT — シリアル送信データ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$032 W</span>
        <span class="reg-val">SERPER — シリアル周期（ボーレート）</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">衝突検出</div>
    <div class="reg-row">
        <span class="reg-field">$00E R</span>
        <span class="reg-val">CLXDAT — 衝突データ（全スプライト間およびスプライト-プレイフィールド間衝突）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$098 W</span>
        <span class="reg-val">CLXCON — 衝突制御（イネーブルビットとマッチ値）</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">カラーパレット（$180〜$1BE）</div>
    <div class="reg-row">
        <span class="reg-field">$180–$1BE W</span>
        <span class="reg-val">COLOR00〜COLOR31 — 32色レジスタ、12ビットRGB（----RRRRGGGGBBBB）</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">マウス/ジョイスティック</div>
    <div class="reg-row">
        <span class="reg-field">$00A R</span>
        <span class="reg-val">JOY0DAT — ジョイスティック/マウスポート0データ（カウンタ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$00C R</span>
        <span class="reg-val">JOY1DAT — ジョイスティック/マウスポート1データ（カウンタ）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$036 W</span>
        <span class="reg-val">JOYTEST — 両ジョイスティックカウンタへの書き込み</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$012 R</span>
        <span class="reg-val">POT0DAT — Potカウンタペア0（アナログパドル/比例入力）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$014 R</span>
        <span class="reg-val">POT1DAT — Potカウンタペア1</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$034 W</span>
        <span class="reg-val">POTGO — Pot開始 / ピン方向</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$016 R</span>
        <span class="reg-val">POTGOR — Potポートデータ読み取り</span>
    </div>
</div>

<!-- section: $DFF1FE "詳細ページ" -->

各サブシステムには、完全なレジスタドキュメント、命令エンコーディング、プログラミング例を含む専用の詳細ページがある――*Bible de l'Amiga*から翻訳。

<div class="addr-map">
    <div class="addr-row"><div class="addr-label">$CPU000</div><div class="addr-body"><a class="addr-name" href="amiga/68000.html">Motorola 68000</a><div class="addr-size">ピン構成、制御信号、アドレッシング、割り込み</div></div></div>
    <div class="addr-row addr-highlight"><div class="addr-label">$BFE001</div><div class="addr-body"><a class="addr-name" href="amiga/cia.html">CIA 8520</a><div class="addr-size">CIA-A & CIA-Bポート、タイマー、TODカウンタ、割り込み制御</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF000</div><div class="addr-body"><a class="addr-name" href="amiga/chipset.html">カスタムチップ</a><div class="addr-size">Agnus、Denise、Paula — アーキテクチャとピン説明</div></div></div>
    <div class="addr-row"><div class="addr-label">$000000</div><div class="addr-body"><a class="addr-name" href="amiga/memory.html">メモリ構成</a><div class="addr-size">Chip RAM、Fast RAM、メモリマップ、ROM、レジスタ空間</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF096</div><div class="addr-body"><a class="addr-name" href="amiga/dma.html">DMAと割り込み</a><div class="addr-size">DMACON、バスサイクル、INTREQ/INTENA、優先度レベル</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF080</div><div class="addr-body"><a class="addr-name" href="amiga/copper.html">Copperコプロセッサ</a><div class="addr-size">MOVE/WAIT/SKIP命令、Copperリスト、DMA</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF100</div><div class="addr-body"><a class="addr-name" href="amiga/playfields.html">プレイフィールドとディスプレイ</a><div class="addr-size">ビットプレーン、解像度、HAM、デュアルプレイフィールド、スクロール</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF120</div><div class="addr-body"><a class="addr-name" href="amiga/sprites.html">スプライト</a><div class="addr-size">8チャネル、制御ワード、ポジショニング、アタッチドスプライト</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF040</div><div class="addr-body"><a class="addr-name" href="amiga/blitter.html">Blitter</a><div class="addr-size">ブロック転送、ミンターム、シフト、マスク、フィル、ライン描画</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF0A0</div><div class="addr-body"><a class="addr-name" href="amiga/audio.html">オーディオシステム</a><div class="addr-size">4チャネル、DMA再生、ボリューム、モジュレーション、フィルタ</div></div></div>
    <div class="addr-row"><div class="addr-label">$JOY00A</div><div class="addr-body"><a class="addr-name" href="amiga/input.html">マウスとジョイスティック</a><div class="addr-size">ゲームポート、クアドラチャデコーディング、パドル入力</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF030</div><div class="addr-body"><a class="addr-name" href="amiga/serial.html">シリアルポート</a><div class="addr-size">UART、ボーレート、SERDAT/SERDATR/SERPER</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF020</div><div class="addr-body"><a class="addr-name" href="amiga/disk.html">ディスクコントローラ</a><div class="addr-size">フロッピーDMA、MFMエンコーディング、DSKSYNC</div></div></div>
    <div class="addr-row"><div class="addr-label">$KBD001</div><div class="addr-body"><a class="addr-name" href="amiga/keyboard.html">キーボード</a><div class="addr-size">6500/1マイクロプロセッサ、シリアルプロトコル、キーコード</div></div></div>
    <div class="addr-row"><div class="addr-label">$CONN01</div><div class="addr-body"><a class="addr-name" href="amiga/connectors.html">コネクタ</a><div class="addr-size">RGB、シリアル、パラレル、フロッピー、ゲームポート、拡張</div></div></div>
</div>
