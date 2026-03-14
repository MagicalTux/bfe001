---
title: "Amiga Technical Reference — BFE001"
description: "The Amiga's custom chipset, memory map, and CIA register details — the hardware behind $BFE001."
---

<!-- section: $DFF000 "Chipset Architecture" -->

The Amiga's power came from its custom chipset — three specialized chips
that handled graphics, sound, and DMA independently of the CPU. While the
Motorola 68000 ran your code, the custom chips ran the machine.

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
        <span class="pcb-info-hint" id="pcb-hint">Hover a component to inspect</span>
        <span class="pcb-info-name" id="pcb-name" style="display:none"></span>
        <span class="pcb-info-addr" id="pcb-addr" style="display:none"></span>
        <span class="pcb-info-desc" id="pcb-desc" style="display:none"></span>
    </div>
</div>

<script>
(function() {
    var chips = {
        'cpu':    { name:'Motorola 68000', addr:'CPU · 7.09 MHz', desc:'16/32-bit CPU with 24-bit address bus (16 MB). Runs your code while the custom chips handle everything else. DIP-64 package.' },
        'agnus':  { name:'Agnus (8375)', addr:'$DFF000', desc:'DMA controller, Copper coprocessor, and Blitter engine. Controls all chip RAM access and schedules DMA cycles. PLCC-84 package.' },
        'gary':   { name:'Gary (5719)', addr:'Address Decoder', desc:'Routes CPU access to the correct chip \u2014 chip RAM, fast RAM, ROM, CIAs, or custom registers. The silent traffic cop.' },
        'denise': { name:'Denise (8362)', addr:'$DFF000 (shared)', desc:'Video output: up to 6 bitplanes, 8 hardware sprites, collision detection, 32-color palette with HAM mode for 4096 colors.' },
        'paula':  { name:'Paula (8364)', addr:'$DFF000 (shared)', desc:'4-channel 8-bit audio with DMA, floppy disk controller, serial port, and interrupt management. The music plays itself.' },
        'cia-a':  { name:'CIA-A (8520)', addr:'$BFE001 \u2605', desc:'Keyboard serial input, game port fire buttons, power LED (bit 1), audio low-pass filter toggle, overlay bit, two 16-bit timers.' },
        'cia-b':  { name:'CIA-B (8520)', addr:'$BFD000', desc:'Parallel port data, floppy disk control (motor, direction, side, step), serial handshaking, two 16-bit timers.' },
        'rom':    { name:'Kickstart ROM', addr:'$F80000', desc:'Contains exec.library, intuition, graphics \u2014 the entire AmigaOS kernel. 256 KB (v1.3). Mapped to $000000 at power-on via overlay bit.' },
        'ram':    { name:'Chip RAM', addr:'$000000', desc:'512 KB stock (16 \u00d7 256Kbit DRAM). Shared between CPU and custom chips \u2014 graphics, audio, and disk data must live here.' },
        'kbd':    { name:'Keyboard', addr:'CIA-A serial', desc:'34-pin ribbon to integrated keyboard. CIA-A reads keycodes via serial protocol with handshake. Own 6500/1 microcontroller.' },
        'floppy': { name:'Floppy Drive', addr:'Paula + CIA-B', desc:'Internal 3.5" 880 KB DD drive. Paula handles disk DMA; CIA-B controls motor, direction, side select, and step signals.' },
        'power':  { name:'Power Input', addr:'5V / 12V DC', desc:'External power brick. The A500 draws about 10W idle. No internal PSU \u2014 just a simple DC-in connector.' },
        'joy0':   { name:'Joystick Port 0', addr:'CIA-A / $DFF00A', desc:'DE-9 connector. Active-low fire button via CIA-A PRA bit 6. Directional switches read from JOY0DAT ($DFF00A). Mouse-compatible.' },
        'joy1':   { name:'Joystick Port 1', addr:'CIA-A / $DFF00C', desc:'DE-9 connector. Active-low fire button via CIA-A PRA bit 7. JOY1DAT at $DFF00C. Primary game controller port.' },
        'serial': { name:'Serial Port', addr:'DB25 RS-232', desc:'Paula handles serial data via $DFF018/$DFF030. CIA-B manages CTS/RTS/DSR/DTR handshaking. Up to 292 Kbaud in raw mode.' },
        'parallel':{ name:'Parallel Port', addr:'CIA-A PRB', desc:'DB25 Centronics-compatible. 8-bit bidirectional data via CIA-A Port B ($BFE101). Active-low BUSY on CIA-B.' },
        'video':  { name:'Video Output', addr:'DB23 RGB', desc:'Analog RGB from Denise. 15 kHz horizontal / 50 Hz vertical (PAL). Directly drives Amiga monitors; needs a scan doubler for VGA.' },
        'audio':  { name:'Audio L/R', addr:'Paula DMA', desc:'Stereo RCA. Channels 0+3 = left, 1+2 = right. 8-bit DMA-driven from chip RAM. Hardware low-pass filter toggled via CIA-A bit 1.' },
        'exp':    { name:'Expansion Slot', addr:'CPU bus', desc:'86-pin side expansion + trapdoor slow RAM slot. Provides full CPU bus access for accelerators, RAM cards, and peripherals.' }
    };

    /* Connections: [from-id, to-id, bus-tag] */
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

    /* Build a map of element centers (as % of board) */
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

    /* Draw all trace lines */
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

    /* Which bus tags to highlight for each chip */
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
            var match = buses.indexOf(t.dataset.bus) !== -1;
            /* Also highlight if this specific wire connects to id */
            wires.forEach(function(w) {
                if (w[2] === t.dataset.bus && (w[0] === id || w[1] === id)) match = true;
            });
            t.classList.toggle('active', match);
        });
        /* Highlight connected chips too */
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

**Agnus** (later Fat Agnus, then Alice) was the brain of the custom chipset.
It controlled DMA — managing memory access for all the other chips so they
could read data without bothering the CPU. It contained the Copper
(a programmable co-processor that modified hardware registers in sync
with the video beam) and the Blitter (a hardware block-transfer engine for
fast memory copies, line drawing, and fill operations).

**Denise** (later Lisa in the AGA chipset) handled all video output:
bitplane graphics, hardware sprites, collision detection, and the color
palette. The Amiga used a planar graphics model — instead of storing pixels
as packed values, each bit of a pixel's color came from a separate bitplane.
This made scrolling and certain effects extremely efficient.

**Paula** handled four independent 8-bit audio channels with hardware
volume and period control, disk drive access, serial I/O, and interrupt
management. MOD music worked by having Paula's DMA channels continuously
read waveform samples from chip RAM while the CPU did other work — the
music literally played itself.

**CIA 8520** (×2) — Complex Interface Adapters. CIA-A at `$BFE001` handled
the keyboard, game port fire buttons, the power LED, and the audio low-pass
filter. CIA-B at `$BFD000` handled the parallel port, serial control
lines, and disk drive signals. Each CIA contained two 16-bit timers and
a 24-bit time-of-day counter.

<!-- section: $000000 "Memory Map" -->

The 68000's 24-bit address bus gave 16 MB of address space. Every piece of
hardware had its place. Writing to a memory address could store data, draw
graphics, play a sound, or toggle the power LED — depending on where
in the map you were writing.

<div class="addr-map">
    <div class="addr-row">
        <span class="addr-label">$000000</span>
        <div class="addr-body">
            <div class="addr-name">Chip RAM</div>
            <div class="addr-size">up to 2 MB</div>
            <div class="addr-comment">shared between CPU and custom chips; graphics, audio, disk data live here</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$200000</span>
        <div class="addr-body">
            <div class="addr-name">Fast RAM / Zorro II</div>
            <div class="addr-size">up to 8 MB</div>
            <div class="addr-comment">CPU-only, no DMA contention</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$A00000</span>
        <div class="addr-body">
            <div class="addr-name addr-size">Reserved</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$BFD000</span>
        <div class="addr-body">
            <div class="addr-name">CIA-B registers</div>
            <div class="addr-comment">parallel, disk, serial control</div>
        </div>
    </div>
    <div class="addr-row addr-highlight">
        <span class="addr-label">$BFE001</span>
        <div class="addr-body">
            <div class="addr-name">CIA-A registers ★</div>
            <div class="addr-comment">keyboard, LED, filter, game ports</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$C00000</span>
        <div class="addr-body">
            <div class="addr-name">Slow RAM</div>
            <div class="addr-size">512 KB (A500 trapdoor)</div>
            <div class="addr-comment">shares bus but no DMA access</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$D00000</span>
        <div class="addr-body">
            <div class="addr-name addr-size">Reserved / Expansion</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$DFF000</span>
        <div class="addr-body">
            <div class="addr-name">Custom Chip Registers</div>
            <div class="addr-size">~256 registers</div>
            <div class="addr-comment">Agnus, Denise, Paula — bitplanes, sprites, colors, DMA</div>
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
            <div class="addr-comment">exec.library, intuition, graphics — the entire Amiga OS kernel</div>
        </div>
    </div>
</div>

At power-on, the overlay bit in CIA-A maps the ROM at address `$000000` so
the CPU can read its reset vectors. One of the first things Kickstart does
is clear that bit, swapping chip RAM into `$000000` and moving the ROM back
to `$F80000`. The jump-table architecture of `exec.library` meant you could
call OS functions at fixed negative offsets from the library base — a
mechanism I spent many evenings exploring.

<!-- section: $BFE001 "CIA-A Registers" -->

The register this site is named after. CIA-A sits at odd addresses starting
from `$BFE001` (the two CIAs use odd/even address decoding to share the
same region without conflicts).

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">Dir</span>
        <span class="bit-name">Name</span>
        <span class="bit-func">$BFE001 — PRA — Port Register A</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/FIR1</span>
        <span class="bit-func">Game port 1 fire button (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/FIR0</span>
        <span class="bit-func">Game port 0 fire button (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/RDY</span>
        <span class="bit-func">Disk ready (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/TK0</span>
        <span class="bit-func">Disk track zero (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/WPRO</span>
        <span class="bit-func">Disk write protect (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/CHNG</span>
        <span class="bit-func">Disk change (active low)</span>
    </div>
    <div class="bit-row bit-highlight">
        <span class="bit-num">1</span>
        <span class="bit-dir">OUT</span>
        <span class="bit-name">LED</span>
        <span class="bit-func">Power LED / audio low-pass filter</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">OUT</span>
        <span class="bit-name">OVL</span>
        <span class="bit-func">Overlay — 1: ROM at $000000, 0: RAM</span>
    </div>
</div>

Bit 1 is the star. Setting it low turns on the power LED and engages the
audio low-pass filter. On the Amiga 500, it was a hard on/off. On the
Amiga 2000 and later, the LED dimmed instead — and the filter smoothly
attenuated frequencies above ~3.3 kHz. Games toggled it to simulate
effects like driving through a tunnel: the muffled audio came from the
hardware filter, not from software processing.

The instruction `MOVE.b #$02,$BFE001` sets bit 1 and clears everything
else — turning the LED off and disabling the filter. To toggle just the
LED without affecting other bits, you'd read the register, XOR bit 1,
and write it back: `BCHG #1,$BFE001`.

<div class="register-block">
    <div class="reg-title">Other CIA-A Registers</div>
    <div class="reg-row">
        <span class="reg-field">$BFE101</span>
        <span class="reg-val">PRB — Port B: parallel port data (active but directly bits)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE201</span>
        <span class="reg-val">DDRA — Data Direction Register A (1 = output, 0 = input)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE301</span>
        <span class="reg-val">DDRB — Data Direction Register B</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE401</span>
        <span class="reg-val">TALO — Timer A low byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE501</span>
        <span class="reg-val">TAHI — Timer A high byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE601</span>
        <span class="reg-val">TBLO — Timer B low byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE701</span>
        <span class="reg-val">TBHI — Timer B high byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE801</span>
        <span class="reg-val">Event counter (TOD) — low byte (1/10 seconds)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE901</span>
        <span class="reg-val">Event counter (TOD) — mid byte (seconds)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEA01</span>
        <span class="reg-val">Event counter (TOD) — high byte (minutes)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFED01</span>
        <span class="reg-val">ICR — Interrupt Control Register</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEE01</span>
        <span class="reg-val">CRA — Control Register A (timer A mode, serial port)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEF01</span>
        <span class="reg-val">CRB — Control Register B (timer B mode, TOD alarm)</span>
    </div>
</div>

The keyboard was read through CIA-A's serial port mechanism. Each keypress
generated an 8-bit scan code sent serially via the KDAT line, clocked by
KCLK. The CIA shifted in the bits and triggered an interrupt when a full
byte was received. The Amiga keyboard had its own microcontroller running
a handshake protocol — the host had to acknowledge each keycode before the
next one could be sent.
