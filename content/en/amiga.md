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
        'cpu':    { name:'Motorola 68000', addr:'CPU · 7.09 MHz', desc:'16/32-bit CPU with 24-bit address bus (16 MB). Runs your code while the custom chips handle everything else. DIP-64 package.', link:'amiga/68000.html' },
        'agnus':  { name:'Agnus (8375)', addr:'$DFF000', desc:'DMA controller, Copper coprocessor, and Blitter engine. Controls all chip RAM access and schedules DMA cycles. PLCC-84 package.', link:'amiga/chipset.html' },
        'gary':   { name:'Gary (5719)', addr:'Address Decoder', desc:'Routes CPU access to the correct chip \u2014 chip RAM, fast RAM, ROM, CIAs, or custom registers. The silent traffic cop.', link:'amiga/chipset.html' },
        'denise': { name:'Denise (8362)', addr:'$DFF000 (shared)', desc:'Video output: up to 6 bitplanes, 8 hardware sprites, collision detection, 32-color palette with HAM mode for 4096 colors.', link:'amiga/chipset.html' },
        'paula':  { name:'Paula (8364)', addr:'$DFF000 (shared)', desc:'4-channel 8-bit audio with DMA, floppy disk controller, serial port, and interrupt management. The music plays itself.', link:'amiga/chipset.html' },
        'cia-a':  { name:'CIA-A (8520)', addr:'$BFE001 \u2605', desc:'Keyboard serial input, game port fire buttons, power LED (bit 1), audio low-pass filter toggle, overlay bit, two 16-bit timers.', link:'amiga/cia.html' },
        'cia-b':  { name:'CIA-B (8520)', addr:'$BFD000', desc:'Parallel port data, floppy disk control (motor, direction, side, step), serial handshaking, two 16-bit timers.', link:'amiga/cia.html' },
        'rom':    { name:'Kickstart ROM', addr:'$F80000', desc:'Contains exec.library, intuition, graphics \u2014 the entire AmigaOS kernel. 256 KB (v1.3). Mapped to $000000 at power-on via overlay bit.', link:'amiga/memory.html' },
        'ram':    { name:'Chip RAM', addr:'$000000', desc:'512 KB stock (16 \u00d7 256Kbit DRAM). Shared between CPU and custom chips \u2014 graphics, audio, and disk data must live here.', link:'amiga/memory.html' },
        'kbd':    { name:'Keyboard', addr:'CIA-A serial', desc:'34-pin ribbon to integrated keyboard. CIA-A reads keycodes via serial protocol with handshake. Own 6500/1 microcontroller.', link:'amiga/keyboard.html' },
        'floppy': { name:'Floppy Drive', addr:'Paula + CIA-B', desc:'Internal 3.5" 880 KB DD drive. Paula handles disk DMA; CIA-B controls motor, direction, side select, and step signals.', link:'amiga/disk.html' },
        'power':  { name:'Power Input', addr:'5V / 12V DC', desc:'External power brick. The A500 draws about 10W idle. No internal PSU \u2014 just a simple DC-in connector.', link:'amiga/connectors.html' },
        'joy0':   { name:'Joystick Port 0', addr:'CIA-A / $DFF00A', desc:'DE-9 connector. Active-low fire button via CIA-A PRA bit 6. Directional switches read from JOY0DAT ($DFF00A). Mouse-compatible.', link:'amiga/input.html' },
        'joy1':   { name:'Joystick Port 1', addr:'CIA-A / $DFF00C', desc:'DE-9 connector. Active-low fire button via CIA-A PRA bit 7. JOY1DAT at $DFF00C. Primary game controller port.', link:'amiga/input.html' },
        'serial': { name:'Serial Port', addr:'DB25 RS-232', desc:'Paula handles serial data via $DFF018/$DFF030. CIA-B manages CTS/RTS/DSR/DTR handshaking. Up to 292 Kbaud in raw mode.', link:'amiga/serial.html' },
        'parallel':{ name:'Parallel Port', addr:'CIA-A PRB', desc:'DB25 Centronics-compatible. 8-bit bidirectional data via CIA-A Port B ($BFE101). Active-low BUSY on CIA-B.', link:'amiga/connectors.html' },
        'video':  { name:'Video Output', addr:'DB23 RGB', desc:'Analog RGB from Denise. 15 kHz horizontal / 50 Hz vertical (PAL). Directly drives Amiga monitors; needs a scan doubler for VGA.', link:'amiga/connectors.html' },
        'audio':  { name:'Audio L/R', addr:'Paula DMA', desc:'Stereo RCA. Channels 0+3 = left, 1+2 = right. 8-bit DMA-driven from chip RAM. Hardware low-pass filter toggled via CIA-A bit 1.', link:'amiga/audio.html' },
        'exp':    { name:'Expansion Slot', addr:'CPU bus', desc:'86-pin side expansion + trapdoor slow RAM slot. Provides full CPU bus access for accelerators, RAM cards, and peripherals.', link:'amiga/connectors.html' }
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
            show(el.dataset.id); el.classList.add('active');
        });
        el.addEventListener('mouseleave', function() {
            hide(); el.classList.remove('active');
        });
        el.addEventListener('click', function(e) {
            e.stopPropagation();
            var c = chips[el.dataset.id];
            if (c && c.link) {
                window.location.href = c.link;
            }
        });
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

<!-- section: $BFD000 "CIA-B Registers" -->

CIA-B sits at even addresses starting from `$BFD000`. Where CIA-A faced
the user (keyboard, LED, joystick buttons), CIA-B faced the peripherals —
parallel port, floppy drive control, and serial handshaking.

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">Dir</span>
        <span class="bit-name">Name</span>
        <span class="bit-func">$BFD000 — PRA — Port Register A</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/DTR</span>
        <span class="bit-func">Serial DTR (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/RTS</span>
        <span class="bit-func">Serial RTS (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/CD</span>
        <span class="bit-func">Serial carrier detect (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/CTS</span>
        <span class="bit-func">Serial clear to send (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">IN</span>
        <span class="bit-name">/DSR</span>
        <span class="bit-func">Serial data set ready (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">OUT</span>
        <span class="bit-name">/SEL</span>
        <span class="bit-func">External floppy drive select (active low)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">1</span>
        <span class="bit-dir">OUT</span>
        <span class="bit-name">/SIDE</span>
        <span class="bit-func">Disk side select (0 = upper, 1 = lower)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">OUT</span>
        <span class="bit-name">/STEP</span>
        <span class="bit-func">Disk step pulse (active low)</span>
    </div>
</div>

CIA-B's Port B (`$BFD100`) carried the parallel port's 8 data lines —
directly mapped, bit for bit. The data direction register at `$BFD300`
controlled whether each line was input or output, making the port fully
bidirectional.

<div class="register-block">
    <div class="reg-title">Other CIA-B Registers</div>
    <div class="reg-row">
        <span class="reg-field">$BFD100</span>
        <span class="reg-val">PRB — Port B: parallel port data (8 bidirectional data lines, directly mapped)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD200</span>
        <span class="reg-val">DDRA — Data Direction Register A</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD300</span>
        <span class="reg-val">DDRB — Data Direction Register B</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD400</span>
        <span class="reg-val">TALO — Timer A low byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD500</span>
        <span class="reg-val">TAHI — Timer A high byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD600</span>
        <span class="reg-val">TBLO — Timer B low byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD700</span>
        <span class="reg-val">TBHI — Timer B high byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD800</span>
        <span class="reg-val">Event counter (TOD) — low byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFD900</span>
        <span class="reg-val">Event counter (TOD) — mid byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFDA00</span>
        <span class="reg-val">Event counter (TOD) — high byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFDD00</span>
        <span class="reg-val">ICR — Interrupt Control Register</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFDE00</span>
        <span class="reg-val">CRA — Control Register A</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFDF00</span>
        <span class="reg-val">CRB — Control Register B</span>
    </div>
</div>

The two CIAs shared the address range `$A00000`–`$BFFFFF` through an
elegant trick: CIA-A responded only to odd addresses (byte-aligned on the 68000's lower data byte), CIA-B only to even ones.
Gary, the address decoder, used address line A12 to select between them
and directly drove their chip selects. This meant you could never accidentally talk
to both at once.

Each CIA's two 16-bit timers could run in one-shot or continuous mode,
count system clock pulses or external events, and trigger interrupts on
underflow. The 24-bit time-of-day counter was clocked by the mains
frequency (50 Hz PAL, 60 Hz NTSC) — giving real wall-clock timing without
burning a timer.

<!-- section: $DFF096 "DMA System" -->

The Amiga's DMA system was the key to its performance. While the 68000
could only do one thing at a time, the custom chips could independently
read and write chip RAM through 25 DMA channels — all orchestrated by
Agnus without CPU intervention.

Every scan line was divided into 227.5 color clocks (PAL). Each color
clock contained two bus cycles — an even cycle and an odd cycle. Agnus
allocated these cycles to different DMA channels in a fixed priority order.
The CPU got whatever cycles were left over.

<div class="register-block">
    <div class="reg-title">DMA Channels</div>
    <div class="reg-row">
        <span class="reg-field">Disk</span>
        <span class="reg-val">3 cycles per scan line — reads/writes floppy data</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Audio</span>
        <span class="reg-val">4 channels, 1 cycle each when active — fetches waveform samples</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Bitplane</span>
        <span class="reg-val">Up to 6 channels (1 per bitplane) — fetches display data</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Sprite</span>
        <span class="reg-val">8 channels, 2 cycles each during sprite lines — fetches sprite image data</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Copper</span>
        <span class="reg-val">1 cycle per instruction word — fetches Copper program instructions</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Blitter</span>
        <span class="reg-val">Up to 4 channels (A, B, C, D) — bulk memory operations</span>
    </div>
</div>

The priority order (highest first) was: disk, audio, sprites, bitplanes,
Copper, Blitter, CPU. In practice, the CPU typically had about 40–60% of
bus cycles available in a normal low-res display. A 6-bitplane hi-res
display consumed so many cycles that the CPU was effectively starved.

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">R/W</span>
        <span class="bit-name">Name</span>
        <span class="bit-func">DMACON $096 (W) / DMACONR $002 (R) — DMA Control</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">15</span>
        <span class="bit-dir">W</span>
        <span class="bit-name">SET/CLR</span>
        <span class="bit-func">1 = set listed bits, 0 = clear listed bits</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">14</span>
        <span class="bit-dir">R</span>
        <span class="bit-name">BBUSY</span>
        <span class="bit-func">Blitter busy (read-only)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">13</span>
        <span class="bit-dir">R</span>
        <span class="bit-name">BZERO</span>
        <span class="bit-func">Blitter zero — all output bits were 0 (read-only)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">10</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">BLTPRI</span>
        <span class="bit-func">Blitter priority over CPU (blitter nasty)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">9</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">DMAEN</span>
        <span class="bit-func">Master DMA enable — all DMA off if clear</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">8</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">BPLEN</span>
        <span class="bit-func">Bitplane DMA enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">COPEN</span>
        <span class="bit-func">Copper DMA enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">BLTEN</span>
        <span class="bit-func">Blitter DMA enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">SPREN</span>
        <span class="bit-func">Sprite DMA enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">DSKEN</span>
        <span class="bit-func">Disk DMA enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">AUD3EN</span>
        <span class="bit-func">Audio channel 3 DMA enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">AUD2EN</span>
        <span class="bit-func">Audio channel 2 DMA enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">1</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">AUD1EN</span>
        <span class="bit-func">Audio channel 1 DMA enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">RW</span>
        <span class="bit-name">AUD0EN</span>
        <span class="bit-func">Audio channel 0 DMA enable</span>
    </div>
</div>

DMACON used the SET/CLR convention found throughout the custom chip
registers: writing with bit 15 set turned on the specified bits; writing
with bit 15 clear turned them off. This avoided the read-modify-write
race conditions that would occur if you had to read the register first.
For example, `MOVE.w #$8380,$DFF096` enabled master DMA, bitplane DMA,
and Copper DMA without affecting anything else.

<!-- section: $DFF09A "Interrupts" -->

The Amiga had 14 interrupt sources mapped across 7 priority levels of the
68000. Paula managed the interrupt request and enable registers, combining
all sources into the CPU's IPL lines.

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">Level</span>
        <span class="bit-name">Name</span>
        <span class="bit-func">INTREQ $09C (W) / INTREQR $01E (R) — Interrupt Request</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">15</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">SET/CLR</span>
        <span class="bit-func">1 = set listed bits, 0 = clear listed bits</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">14</span>
        <span class="bit-dir">6</span>
        <span class="bit-name">INTEN</span>
        <span class="bit-func">Master interrupt enable (must be set for any interrupt)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">13</span>
        <span class="bit-dir">6</span>
        <span class="bit-name">EXTER</span>
        <span class="bit-func">CIA-B interrupt (active low from /INT on CIA-B)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">12</span>
        <span class="bit-dir">5</span>
        <span class="bit-name">DSKSYN</span>
        <span class="bit-func">Disk sync word matched (DSKSYNC register)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">11</span>
        <span class="bit-dir">5</span>
        <span class="bit-name">RBF</span>
        <span class="bit-func">Serial port receive buffer full</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">10</span>
        <span class="bit-dir">4</span>
        <span class="bit-name">AUD3</span>
        <span class="bit-func">Audio channel 3 block finished</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">9</span>
        <span class="bit-dir">4</span>
        <span class="bit-name">AUD2</span>
        <span class="bit-func">Audio channel 2 block finished</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">8</span>
        <span class="bit-dir">4</span>
        <span class="bit-name">AUD1</span>
        <span class="bit-func">Audio channel 1 block finished</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">4</span>
        <span class="bit-name">AUD0</span>
        <span class="bit-func">Audio channel 0 block finished</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">3</span>
        <span class="bit-name">BLIT</span>
        <span class="bit-func">Blitter finished</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">3</span>
        <span class="bit-name">VERTB</span>
        <span class="bit-func">Vertical blank (start of frame)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">3</span>
        <span class="bit-name">COPER</span>
        <span class="bit-func">Copper (triggered by Copper program)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">2</span>
        <span class="bit-name">PORTS</span>
        <span class="bit-func">CIA-A interrupt (keyboard, TOD, timers)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">1</span>
        <span class="bit-name">SOFT</span>
        <span class="bit-func">Software interrupt (set by CPU)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">1</span>
        <span class="bit-dir">1</span>
        <span class="bit-name">DSKBLK</span>
        <span class="bit-func">Disk block finished (DMA transfer complete)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">1</span>
        <span class="bit-name">TBE</span>
        <span class="bit-func">Serial port transmit buffer empty</span>
    </div>
</div>

INTENA (`$09A` write, `$01C` read) had the same layout. Both the request
*and* the enable bit had to be set, plus the master enable (bit 14), for
an interrupt to actually reach the CPU. Interrupt handlers had to clear
the request bit in INTREQ before returning, or the interrupt would
re-trigger immediately.

Level 3 (INT3) was the workhorse — vertical blank ran every frame at 50 Hz
(PAL) or 60 Hz (NTSC), making it the standard heartbeat for game logic,
music playback, and OS scheduling. The Copper interrupt let programs
trigger code at specific screen positions, which was essential for split-screen
effects.

<!-- section: $DFF080 "Copper" -->

The Copper was a simple but extraordinarily powerful coprocessor built into
Agnus. It had only three instructions — MOVE, WAIT, and SKIP — and could
only write to custom chip registers. But because it executed in sync with
the video beam, it could change hardware state at any point on screen.

<div class="register-block">
    <div class="reg-title">Copper Instructions (32 bits each)</div>
    <div class="reg-row">
        <span class="reg-field">MOVE</span>
        <span class="reg-val">Write a value to a custom chip register. First word: register address (low 9 bits, bit 0 always 0). Second word: data value.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">WAIT</span>
        <span class="reg-val">Halt until the beam reaches a given position. First word: VP[7:0] HP[7:1] 1. Second word: VPM[7:0] HPM[7:1] 0. Mask bits define comparison precision.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">SKIP</span>
        <span class="reg-val">Skip the next instruction if the beam has passed a position. Same format as WAIT but second word bit 0 = 1.</span>
    </div>
</div>

The Copper list was a program stored in chip RAM. On every frame, the
Copper restarted from the address in COP1LC (`$080`/`$082`). Writing to
COPJMP1 (`$088`) forced an immediate restart. COP2LC/COPJMP2 provided a
second entry point — typically used by the vertical blank interrupt to
switch lists between frames.

A typical Copper list set up the display: load bitplane pointers, set
colors, configure scroll registers. But the real magic was mid-screen
changes. By waiting for specific beam positions and then writing new
values, the Copper could create effects impossible with static register
settings — gradient skies, split-screen resolutions, per-scanline color
cycling, and even raster bars that cost zero CPU time.

The `WAIT $FFDFFFFE` instruction was the conventional end-of-list marker.
It waited for beam position V=255 H=223, which in PAL exceeded the
visible area. The Copper would stall there until the next vertical blank
restarted it.

For Copper to write to registers above `$07F` (the color and sprite
registers), bit 1 of COPCON (`$02E`) — the "Copper danger" bit — had to
be set. This prevented the Copper from accidentally modifying sensitive
registers like DMACON or disk pointers.

<!-- section: $DFF100 "Playfields" -->

The Amiga used a planar graphics model. Instead of storing each pixel as
a packed color value, the image was split across up to 6 **bitplanes** —
each bitplane held one bit of every pixel's color index. This made
scrolling and certain blitter operations extremely efficient at the cost
of more complex pixel manipulation.

<div class="register-block">
    <div class="reg-title">Display Modes (BPLCON0 $100)</div>
    <div class="reg-row">
        <span class="reg-field">Low-res</span>
        <span class="reg-val">320×256 (PAL) / 320×200 (NTSC), up to 6 bitplanes = 64 colors (HAM) or 32 colors</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">High-res</span>
        <span class="reg-val">640×256 / 640×200, up to 4 bitplanes = 16 colors (HIRES bit 15 = 1)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">HAM</span>
        <span class="reg-val">Hold-and-Modify — 6 bitplanes, top 2 bits select hold/modify-R/modify-G/modify-B, bottom 4 bits = value. 4096 colors on screen.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">EHB</span>
        <span class="reg-val">Extra Half-Brite — 6 bitplanes, plane 6 selects half-brightness version of the 32-color palette. 64 colors.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Dual PF</span>
        <span class="reg-val">Dual Playfield — two independent 3-bitplane layers (odd planes = PF1, even planes = PF2). 8+8 colors with transparency.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Interlace</span>
        <span class="reg-val">Doubles vertical resolution (512 lines PAL) by alternating fields. LACE bit 2 in BPLCON0.</span>
    </div>
</div>

The display window defined the visible area. DIWSTRT (`$08E`) set the
start position and DIWSTOP (`$090`) set the stop position, both in beam
coordinates. Standard PAL values were `$2C81` (start) and `$2CC1` (stop)
for a 320×256 display.

Data fetch timing was separate from the display window. DDFSTRT (`$092`)
and DDFSTOP (`$094`) controlled when Agnus started and stopped reading
bitplane data from RAM. For standard low-res: DDFSTRT = `$0038`,
DDFSTOP = `$00D0`. For high-res: DDFSTRT = `$003C`, DDFSTOP = `$00D4`.

Each bitplane had a pointer pair (BPL1PTH/L at `$0E0`/`$0E2` through
BPL6PTH/L at `$0EC`/`$0EE`) pointing to its data in chip RAM. After
each scan line, a modulo value was added to the pointer — BPL1MOD
(`$108`) for odd bitplanes, BPL2MOD (`$10A`) for even. Setting the
modulo to the display width allowed the bitplanes to wrap naturally; larger
values enabled smooth vertical scrolling by skipping lines.

Horizontal scrolling was achieved through BPLCON1 (`$102`), which held
independent 4-bit delay values for playfield 1 and playfield 2. Combined
with adjusting the data fetch start and bitplane pointers, this gave
pixel-precise smooth scrolling at no CPU cost — the hardware did it all.

<div class="register-block">
    <div class="reg-title">Color Palette — COLOR00–COLOR31 ($180–$1BE)</div>
    <div class="reg-row">
        <span class="reg-field">Format</span>
        <span class="reg-val">12-bit RGB: ---- RRRR GGGG BBBB. Each component 0–15. Example: $0F00 = bright red, $0FFF = white.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">COLOR00</span>
        <span class="reg-val">Background color — displayed in border and behind all playfields/sprites</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Sprite colors</span>
        <span class="reg-val">Sprites 0–1 use COLOR17–19, Sprites 2–3 use COLOR21–23, Sprites 4–5 use COLOR25–27, Sprites 6–7 use COLOR29–31</span>
    </div>
</div>

<!-- section: $DFF120 "Sprites" -->

The Amiga had 8 hardware sprites, each 16 pixels wide and any height,
with 3 colors plus transparency. They were completely independent of the
playfield — rendered by Denise on top of (or behind) the bitplane
display, with zero CPU overhead once the DMA was set up.

Each sprite had a pointer pair (SPR0PTH/L at `$120`/`$122` through
SPR7PTH/L at `$13E`/`$13F`) pointing to sprite data in chip RAM. The
data format was simple:

<div class="register-block">
    <div class="reg-title">Sprite Data Format (in chip RAM)</div>
    <div class="reg-row">
        <span class="reg-field">Word 0</span>
        <span class="reg-val">SPRxPOS — vertical start (V7–V0 in high byte), horizontal start (H8–H1 in low byte)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Word 1</span>
        <span class="reg-val">SPRxCTL — vertical stop (V7–V0 in high byte), control bits (attach, V8, H0) in low byte</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Words 2+3</span>
        <span class="reg-val">First image line: DATA word (plane 0) + DATB word (plane 1). 16 pixels, 2 bits per pixel.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">...</span>
        <span class="reg-val">Repeat DATA/DATB pairs for each additional line</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Last pair</span>
        <span class="reg-val">Two zero words ($0000 $0000) mark the end of sprite data</span>
    </div>
</div>

Sprites could be **attached** in pairs (0+1, 2+3, 4+5, 6+7). When
attached, the two sprites combined their bitplanes into a single 4-bitplane
sprite with 15 colors plus transparent — at the cost of reducing 8
independent sprites to 4.

Sprite priority relative to the playfield was controlled by BPLCON2
(`$104`). Each sprite pair could be placed in front of, between, or behind
the two playfields. The mouse pointer was traditionally sprite 0.

<!-- section: $DFF040 "Blitter" -->

The Blitter was a hardware block-transfer engine inside Agnus, designed
for three operations: bulk memory copies (with logic), area filling, and
line drawing. It operated on rectangular regions of chip RAM at speeds
the 68000 could not match.

The Blitter had four DMA channels: **A**, **B**, and **C** as data sources,
and **D** as the output destination. For each word processed, the three
source values were combined through a programmable logic function
(the *minterm*) to produce the output. This single mechanism handled
everything from simple copies to cookie-cut sprite rendering.

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">Name</span>
        <span class="bit-func">BLTCON0 $040 — Blitter Control Register 0</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">15–12</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">ASH</span>
        <span class="bit-func">Shift value for source A (0–15 bits right)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">11</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">USEA</span>
        <span class="bit-func">Enable DMA channel A</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">10</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">USEB</span>
        <span class="bit-func">Enable DMA channel B</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">9</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">USEC</span>
        <span class="bit-func">Enable DMA channel C</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">8</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">USED</span>
        <span class="bit-func">Enable DMA channel D (output)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7–0</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">LF7–LF0</span>
        <span class="bit-func">Minterm logic function — 256 possible Boolean combinations of A, B, C</span>
    </div>
</div>

The minterm byte encoded the Boolean function as a truth table: each bit
position corresponded to a combination of A, B, C inputs. For example,
`$F0` = copy A (D=A), `$CA` = cookie-cut (D=AC+BC̄, i.e., use A where C
is set, B where C is clear), `$00` = clear to zero.

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">Name</span>
        <span class="bit-func">BLTCON1 $042 — Blitter Control Register 1</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">15–12</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">BSH</span>
        <span class="bit-func">Shift value for source B (0–15 bits right)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">EFE</span>
        <span class="bit-func">Exclusive fill enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">IFE</span>
        <span class="bit-func">Inclusive fill enable</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">FCI</span>
        <span class="bit-func">Fill carry input (initial fill state)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">1</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">DESC</span>
        <span class="bit-func">Descending mode (process from bottom-right to top-left)</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">—</span>
        <span class="bit-name">LINE</span>
        <span class="bit-func">Line draw mode enable</span>
    </div>
</div>

The Blitter's **area fill** mode worked line by line, right to left. It
tracked a carry bit that toggled every time it encountered a set bit in
the input — filling the space between pairs of boundary pixels. Inclusive
fill (IFE) kept the boundary pixels in the output; exclusive fill (EFE)
removed them. This made it trivial to draw filled polygons: render the
outline with the line-draw mode, then fill it in a single blitter pass.

**Line drawing** used a hardware Bresenham algorithm. The screen was
divided into 8 octants (numbered 0–7); the programmer calculated which
octant the line fell in and set the SUD/SUL/AUL bits in BLTCON1
accordingly. The Blitter could draw lines up to 1024 pixels long,
with optional 16-bit repeating patterns for dashed or textured lines.

BLTSIZE (`$058`) started the operation: bits 15–6 held the height
(number of rows), bits 5–0 held the width (in words). Writing to
BLTSIZE triggered the Blitter — it was always the last register written
in a blitter setup sequence.

<!-- section: $DFF0A0 "Audio" -->

Paula provided four independent 8-bit PCM audio channels with hardware
DMA. Channels 0 and 3 were routed to the left speaker, channels 1 and 2
to the right. Each channel continuously read waveform samples from chip
RAM and fed them through a DAC — the music played itself without CPU
intervention.

<div class="register-block">
    <div class="reg-title">Audio Channel Registers (×4 — channel 0 shown)</div>
    <div class="reg-row">
        <span class="reg-field">AUD0LC</span>
        <span class="reg-val">$0A0/$0A2 — Pointer to waveform data in chip RAM (high/low words)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">AUD0LEN</span>
        <span class="reg-val">$0A4 — Length in words (the DMA will transfer this many words, then restart from AUD0LC)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">AUD0PER</span>
        <span class="reg-val">$0A6 — Period (sample rate). Lower = higher pitch. Period = clock / (2 × frequency).</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">AUD0VOL</span>
        <span class="reg-val">$0A8 — Volume: 0 (silent) to 64 (maximum). 6-bit linear scale.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">AUD0DAT</span>
        <span class="reg-val">$0AA — Audio data register (2 samples, high byte played first). Used for non-DMA playback.</span>
    </div>
</div>

Channels 1–3 had identical registers at offsets `$0B0`, `$0C0`, `$0D0`.

The period register set the playback rate. The PAL system clock was
3.546895 MHz. For a sample rate of 8000 Hz: period = 3546895 / (2 × 8000)
≈ 222. The minimum useful period was about 124 (28.6 kHz) — below that,
audio DMA consumed too many bus cycles and starved the display.

Paula also supported **channel modulation**: one channel could modulate
the period or volume of the next channel in the pair (0→1, 2→3). ADKCON
(`$09E`) bits 4–7 controlled this. Period modulation enabled FM-like
synthesis effects; volume modulation enabled amplitude modulation (tremolo).
These modes were rarely used by tracker music but appeared in some demo
effects.

When a channel finished playing its buffer (AUDxLEN words exhausted), it
triggered an audio interrupt (AUD0–AUD3 in INTREQ). The interrupt handler
could then set up the next buffer — enabling double-buffered streaming
audio. MOD players used this mechanism: each channel's interrupt pointed
to the next set of samples, while the CPU spent its time mixing and
applying effects.

<!-- section: $DFF030 "Serial Port" -->

Paula contained a UART (Universal Asynchronous Receiver/Transmitter) for
serial communication. The DB25 RS-232 connector carried the data lines;
CIA-B handled the handshaking signals (DTR, RTS, CTS, DSR, CD).

<div class="register-block">
    <div class="reg-title">UART Registers</div>
    <div class="reg-row">
        <span class="reg-field">SERDAT $030</span>
        <span class="reg-val">Transmit data (write). Lower 8 or 9 bits = data, upper bits = stop bits (set to 1).</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">SERDATR $018</span>
        <span class="reg-val">Receive data (read). Bit 14: RBF (buffer full), Bit 13: TBE (transmit empty), Bit 12: TSRE (shift register empty), Bit 11: RXD level, Bits 0–7: received data.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">SERPER $032</span>
        <span class="reg-val">Baud rate and mode. Bit 15: LONG (1 = 9-bit data). Bits 14–0: period value.</span>
    </div>
</div>

The baud rate was set through SERPER using the formula:

SERPER = (clock / baud_rate) − 1, where clock = 3,579,545 Hz.

For 9600 baud: SERPER = (3579545 / 9600) − 1 ≈ 372 = `$0174`.
For 4800 baud: SERPER ≈ 745 = `$02E9`.

The UART generated two interrupts: TBE (transmit buffer empty, INTREQ
bit 0, level 1) when ready for the next byte, and RBF (receive buffer
full, INTREQ bit 11, level 5) when a byte arrived. In raw mode (ignoring
handshaking), the Amiga could reach about 292 Kbaud — far beyond the
standard RS-232 rates.

<!-- section: $DFF020 "Disk Controller" -->

Paula's disk controller used DMA to transfer raw MFM-encoded data
between chip RAM and the floppy drive. CIA-B controlled the mechanical
signals (motor, step, direction, side select), while Paula handled the
data stream.

<div class="register-block">
    <div class="reg-title">Disk Registers</div>
    <div class="reg-row">
        <span class="reg-field">DSKPTH/L</span>
        <span class="reg-val">$020/$022 — Pointer to disk DMA buffer in chip RAM</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKLEN $024</span>
        <span class="reg-val">Bit 15: DMAEN (start DMA), Bit 14: WRITE (1 = write mode), Bits 13–0: transfer length in words</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKSYNC $07E</span>
        <span class="reg-val">Sync word — DMA waits for this pattern before transferring (typically $4489 for AmigaDOS)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKBYTR $01A</span>
        <span class="reg-val">Disk byte and status (read). Bit 15: BYTEREADY, Bit 14: DMAON, Bit 13: DSKWRITE, Bit 12: WORDEQUAL, Bits 7–0: raw data byte.</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKDAT $026</span>
        <span class="reg-val">Disk DMA write data register</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">DSKDAT $008</span>
        <span class="reg-val">Disk DMA read data register (early-read, not CPU-accessible)</span>
    </div>
</div>

To read a track, the programming sequence was:

1. Ensure no write operation is in progress (check DSKLEN)
2. Set DSKPTH/L to the buffer address
3. Set DSKLEN with the transfer length and DMAEN set
4. Write DSKLEN again (safety: requires two writes to start DMA)
5. Wait for DSKBLK interrupt (INTREQ bit 1)
6. Clear DSKLEN to stop DMA

The write sequence added an extra safety measure: DSKLEN had to be
written twice with the WRITE bit set before a write would begin. This
prevented accidental disk corruption from a single stray write.

ADKCON (`$09E`) controlled MFM/GCR encoding selection, precompensation
timing, and the WORDSYNC mode that made the controller wait for the sync
word in DSKSYNC before starting the transfer. The standard Amiga 3.5"
DD drive stored 880 KB per disk: 80 tracks × 2 sides × 11 sectors × 512
bytes.

<!-- section: $KBD001 "Keyboard" -->

The Amiga 500's keyboard contained its own 6500/1 microcontroller. It
scanned the key matrix independently and transmitted 8-bit scan codes
serially to CIA-A through a two-wire protocol: KDAT (data) and KCLK
(clock).

The protocol was active-low, MSB first. After transmitting a complete
byte, the keyboard waited for the host to acknowledge by pulling KDAT
low for at least 85 microseconds. Without this handshake, the keyboard
would not send the next keycode — providing built-in flow control.

The scan code format was: bit 7 = 0 for key press, 1 for key release.
Bits 6–0 encoded the key identity. The raw codes were position-based,
not ASCII — the OS keymap translated them into characters.

A special three-key combination (Ctrl + Amiga + Amiga) triggered a hard
reset by asserting the /KBRESET line, which was directly wired to the
system reset circuit. This was the infamous "three-finger salute" that
every Amiga user knew by instinct.

The keyboard also transmitted a power-up key stream: a sequence of codes
at startup that identified the keyboard type and language layout. The
system could detect whether the keyboard was present by checking for this
stream within a timeout period.

<!-- section: $CONN01 "Connectors" -->

The Amiga 500's rear panel carried a distinctive row of connectors, each
with a specific purpose:

<div class="register-block">
    <div class="reg-title">Video — DB23 Female (analog RGB)</div>
    <div class="reg-row">
        <span class="reg-field">Pins 3,4,5</span>
        <span class="reg-val">Red, Green, Blue — analog 0–0.7V from Denise</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 10</span>
        <span class="reg-val">/CSYNC — composite sync</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 11</span>
        <span class="reg-val">/HSYNC — horizontal sync</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 12</span>
        <span class="reg-val">/VSYNC — vertical sync</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Timing</span>
        <span class="reg-val">15.625 kHz horizontal (PAL), 50 Hz vertical. Directly drives Amiga monitors; needs a scan doubler for VGA.</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Joystick / Mouse — DE-9 Male (×2)</div>
    <div class="reg-row">
        <span class="reg-field">Pins 1–4</span>
        <span class="reg-val">Up, Down, Left, Right (directly active digital switches, read via JOY0DAT $00A / JOY1DAT $00C)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 6</span>
        <span class="reg-val">Fire button — active low, read via CIA-A PRA bit 6 (port 0) or bit 7 (port 1)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 5</span>
        <span class="reg-val">Middle button / pot X — active low digital or proportional analog input (read via POT0DAT / POT1DAT)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 9</span>
        <span class="reg-val">Right button / pot Y — same as pin 5</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 7</span>
        <span class="reg-val">+5V power supply (max 50 mA)</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Serial — DB25 Male (RS-232)</div>
    <div class="reg-row">
        <span class="reg-field">Pin 2</span>
        <span class="reg-val">TXD — transmit data (from Paula)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 3</span>
        <span class="reg-val">RXD — receive data (to Paula)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pins 4–6,8,20</span>
        <span class="reg-val">Handshake signals (RTS, CTS, DSR, CD, DTR) — managed by CIA-B Port A</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 7</span>
        <span class="reg-val">Signal ground</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Parallel — DB25 Female (Centronics-compatible)</div>
    <div class="reg-row">
        <span class="reg-field">Pins 2–9</span>
        <span class="reg-val">Data bits 0–7 — bidirectional via CIA-A Port B ($BFE101)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 1</span>
        <span class="reg-val">/STROBE — active low output pulse</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 11</span>
        <span class="reg-val">BUSY — active low, directly read via CIA-B Flag input</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 12</span>
        <span class="reg-val">POUT — paper out</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 13</span>
        <span class="reg-val">/SEL — printer selected</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Floppy — DB23 Male (active low control, custom Amiga pinout)</div>
    <div class="reg-row">
        <span class="reg-field">Pin 2</span>
        <span class="reg-val">/CHNG — disk changed (to CIA-A PRA bit 2)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 3</span>
        <span class="reg-val">/INDEX — once per revolution</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 5</span>
        <span class="reg-val">/TK0 — track zero (to CIA-A PRA bit 4)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 6</span>
        <span class="reg-val">/WPRO — write protect (to CIA-A PRA bit 3)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 7</span>
        <span class="reg-val">/RDY — drive ready (to CIA-A PRA bit 5)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pins 15,18,19</span>
        <span class="reg-val">/MTR, /DIR, /STEP — motor, direction, step (from CIA-B)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 13</span>
        <span class="reg-val">/SEL0 — drive 0 select (active low, from CIA-B PRA bit 3)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Pin 14</span>
        <span class="reg-val">/SIDE — side select (from CIA-B PRA bit 2)</span>
    </div>
</div>

The 86-pin expansion connector on the bottom provided full access to the
68000's address and data bus, plus control signals. This was the gateway
for accelerator boards, RAM expansions, and any add-on that needed to
talk directly to the CPU bus. It was the Amiga's greatest strength and
limitation — completely open, completely unprotected, completely trusting.

<!-- section: $DFF1FE "Custom Chip Register Map" -->

All custom chip registers lived in a 512-byte block starting at `$DFF000`.
The offset (e.g., `$096` for DMACON) was added to this base address.
Registers were either read-only (R), write-only (W), or strobe (S —
writing any value triggers the action). Some register pairs shared
addresses for reading and writing different values.

<div class="register-block">
    <div class="reg-title">DMA &amp; Control</div>
    <div class="reg-row">
        <span class="reg-field">$002 R</span>
        <span class="reg-val">DMACONR — DMA control read</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$096 W</span>
        <span class="reg-val">DMACON — DMA control write (SET/CLR)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$01C R</span>
        <span class="reg-val">INTENAR — Interrupt enable read</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$09A W</span>
        <span class="reg-val">INTENA — Interrupt enable write (SET/CLR)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$01E R</span>
        <span class="reg-val">INTREQR — Interrupt request read</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$09C W</span>
        <span class="reg-val">INTREQ — Interrupt request write (SET/CLR)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$010 R</span>
        <span class="reg-val">ADKCONR — Audio/disk control read</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$09E W</span>
        <span class="reg-val">ADKCON — Audio/disk control write (SET/CLR)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$004 R</span>
        <span class="reg-val">VPOSR — Beam position (V8 and frame ID)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$006 R</span>
        <span class="reg-val">VHPOSR — Beam position (V7–V0, H8–H1)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$02A W</span>
        <span class="reg-val">VPOSW — Beam position write (for genlock sync)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$02C W</span>
        <span class="reg-val">VHPOSW — Beam position write</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$02E W</span>
        <span class="reg-val">COPCON — Copper control (bit 1 = Copper danger)</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Copper</div>
    <div class="reg-row">
        <span class="reg-field">$080 W</span>
        <span class="reg-val">COP1LCH — Copper list 1 pointer (high)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$082 W</span>
        <span class="reg-val">COP1LCL — Copper list 1 pointer (low)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$084 W</span>
        <span class="reg-val">COP2LCH — Copper list 2 pointer (high)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$086 W</span>
        <span class="reg-val">COP2LCL — Copper list 2 pointer (low)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$088 S</span>
        <span class="reg-val">COPJMP1 — Restart Copper at list 1</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$08A S</span>
        <span class="reg-val">COPJMP2 — Restart Copper at list 2</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$08C W</span>
        <span class="reg-val">COPINS — Copper instruction fetch (debug)</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Bitplane</div>
    <div class="reg-row">
        <span class="reg-field">$100 W</span>
        <span class="reg-val">BPLCON0 — Bitplane control (# planes, HIRES, HAM, DPF)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$102 W</span>
        <span class="reg-val">BPLCON1 — Scroll values (PF1 and PF2 horizontal delay)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$104 W</span>
        <span class="reg-val">BPLCON2 — Sprite/playfield priority</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$108 W</span>
        <span class="reg-val">BPL1MOD — Odd bitplane modulo</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$10A W</span>
        <span class="reg-val">BPL2MOD — Even bitplane modulo</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$0E0–$0EE W</span>
        <span class="reg-val">BPL1PT–BPL6PT — Bitplane 1–6 pointers (high/low word pairs)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$110–$11E R</span>
        <span class="reg-val">BPL1DAT–BPL6DAT — Bitplane data (latched from DMA)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$08E W</span>
        <span class="reg-val">DIWSTRT — Display window start (V7–V0, H7–H0)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$090 W</span>
        <span class="reg-val">DIWSTOP — Display window stop (V7–V0, H7–H0)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$092 W</span>
        <span class="reg-val">DDFSTRT — Data fetch start</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$094 W</span>
        <span class="reg-val">DDFSTOP — Data fetch stop</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Sprites ($120–$17E)</div>
    <div class="reg-row">
        <span class="reg-field">$120–$13E W</span>
        <span class="reg-val">SPR0PT–SPR7PT — Sprite 0–7 DMA pointers (high/low pairs)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$140–$17E W</span>
        <span class="reg-val">SPR0POS/CTL/DATA/DATB through SPR7POS/CTL/DATA/DATB — position, control, image data</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Blitter</div>
    <div class="reg-row">
        <span class="reg-field">$040 W</span>
        <span class="reg-val">BLTCON0 — Blitter control 0 (shifts, enables, minterms)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$042 W</span>
        <span class="reg-val">BLTCON1 — Blitter control 1 (B-shift, fill, line mode)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$044 W</span>
        <span class="reg-val">BLTAFWM — First word mask for source A</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$046 W</span>
        <span class="reg-val">BLTALWM — Last word mask for source A</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$048–$056 W</span>
        <span class="reg-val">BLTCPT, BLTBPT, BLTAPT, BLTDPT — Source C/B/A and dest D pointers</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$058 W</span>
        <span class="reg-val">BLTSIZE — Start blitter (height × 64 + width in words)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$060–$066 W</span>
        <span class="reg-val">BLTCMOD, BLTBMOD, BLTAMOD, BLTDMOD — Modulo values for C/B/A/D</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$070–$076 W</span>
        <span class="reg-val">BLTCDAT, BLTBDAT, BLTADAT — Data registers for C/B/A</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$000 R</span>
        <span class="reg-val">BLTDDAT — Blitter destination data (early read)</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Audio</div>
    <div class="reg-row">
        <span class="reg-field">$0A0–$0AE</span>
        <span class="reg-val">AUD0 — LC (pointer), LEN (length), PER (period), VOL (volume), DAT (data)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$0B0–$0BE</span>
        <span class="reg-val">AUD1 — same layout</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$0C0–$0CE</span>
        <span class="reg-val">AUD2 — same layout</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$0D0–$0DE</span>
        <span class="reg-val">AUD3 — same layout</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Disk</div>
    <div class="reg-row">
        <span class="reg-field">$020/$022 W</span>
        <span class="reg-val">DSKPT — Disk DMA pointer (high/low)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$024 W</span>
        <span class="reg-val">DSKLEN — DMA length and control</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$026 W</span>
        <span class="reg-val">DSKDAT — Disk DMA data write</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$008 R</span>
        <span class="reg-val">DSKDATR — Disk DMA data read (early read)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$01A R</span>
        <span class="reg-val">DSKBYTR — Disk byte and status</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$07E W</span>
        <span class="reg-val">DSKSYNC — Disk sync pattern</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Serial</div>
    <div class="reg-row">
        <span class="reg-field">$018 R</span>
        <span class="reg-val">SERDATR — Serial receive data and status</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$030 W</span>
        <span class="reg-val">SERDAT — Serial transmit data</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$032 W</span>
        <span class="reg-val">SERPER — Serial period (baud rate)</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Collision Detection</div>
    <div class="reg-row">
        <span class="reg-field">$00E R</span>
        <span class="reg-val">CLXDAT — Collision data (all sprite-sprite and sprite-playfield collisions)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$098 W</span>
        <span class="reg-val">CLXCON — Collision control (enable bits and match values)</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Color Palette ($180–$1BE)</div>
    <div class="reg-row">
        <span class="reg-field">$180–$1BE W</span>
        <span class="reg-val">COLOR00–COLOR31 — 32 color registers, 12-bit RGB (----RRRRGGGGBBBB)</span>
    </div>
</div>

<div class="register-block">
    <div class="reg-title">Mouse/Joystick</div>
    <div class="reg-row">
        <span class="reg-field">$00A R</span>
        <span class="reg-val">JOY0DAT — Joystick/mouse port 0 data (counters)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$00C R</span>
        <span class="reg-val">JOY1DAT — Joystick/mouse port 1 data (counters)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$036 W</span>
        <span class="reg-val">JOYTEST — Write to both joystick counters</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$012 R</span>
        <span class="reg-val">POT0DAT — Pot counter pair 0 (analog paddle/proportional inputs)</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$014 R</span>
        <span class="reg-val">POT1DAT — Pot counter pair 1</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$034 W</span>
        <span class="reg-val">POTGO — Pot start / pin direction</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$016 R</span>
        <span class="reg-val">POTGOR — Pot port data read</span>
    </div>
</div>

<!-- section: $DFF1FE "Deep Dive Pages" -->

Each subsystem has a dedicated deep-dive page with full register documentation, instruction encoding, and programming examples — translated from the *Bible de l'Amiga*.

<div class="addr-map">
    <div class="addr-row addr-highlight"><div class="addr-label">$A01000</div><div class="addr-body"><a class="addr-name" href="amiga/models.html">Amiga Models</a><div class="addr-size">A1000 through A4000 — chipset generations, CPUs, release history</div></div></div>
    <div class="addr-row"><div class="addr-label">$CPU000</div><div class="addr-body"><a class="addr-name" href="amiga/68000.html">Motorola 68000</a><div class="addr-size">Pin configuration, control signals, addressing, interrupts</div></div></div>
    <div class="addr-row addr-highlight"><div class="addr-label">$BFE001</div><div class="addr-body"><a class="addr-name" href="amiga/cia.html">CIA 8520</a><div class="addr-size">CIA-A & CIA-B ports, timers, TOD counter, interrupt control</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF000</div><div class="addr-body"><a class="addr-name" href="amiga/chipset.html">Custom Chips</a><div class="addr-size">Agnus, Denise, Paula — architecture and pin descriptions</div></div></div>
    <div class="addr-row"><div class="addr-label">$000000</div><div class="addr-body"><a class="addr-name" href="amiga/memory.html">Memory Organization</a><div class="addr-size">Chip RAM, Fast RAM, memory map, ROM, register space</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF096</div><div class="addr-body"><a class="addr-name" href="amiga/dma.html">DMA & Interrupts</a><div class="addr-size">DMACON, bus cycles, INTREQ/INTENA, priority levels</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF080</div><div class="addr-body"><a class="addr-name" href="amiga/copper.html">Copper Coprocessor</a><div class="addr-size">MOVE/WAIT/SKIP instructions, Copper lists, DMA</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF100</div><div class="addr-body"><a class="addr-name" href="amiga/playfields.html">Playfields & Display</a><div class="addr-size">Bitplanes, resolutions, HAM, dual playfields, scrolling</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF120</div><div class="addr-body"><a class="addr-name" href="amiga/sprites.html">Sprites</a><div class="addr-size">8 channels, control words, positioning, attached sprites</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF040</div><div class="addr-body"><a class="addr-name" href="amiga/blitter.html">Blitter</a><div class="addr-size">Block transfers, minterms, shifting, masks, fill, line draw</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF0A0</div><div class="addr-body"><a class="addr-name" href="amiga/audio.html">Audio System</a><div class="addr-size">4 channels, DMA playback, volume, modulation, filter</div></div></div>
    <div class="addr-row"><div class="addr-label">$JOY00A</div><div class="addr-body"><a class="addr-name" href="amiga/input.html">Mouse & Joystick</a><div class="addr-size">Game ports, quadrature decoding, paddle inputs</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF030</div><div class="addr-body"><a class="addr-name" href="amiga/serial.html">Serial Port</a><div class="addr-size">UART, baud rate, SERDAT/SERDATR/SERPER</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF020</div><div class="addr-body"><a class="addr-name" href="amiga/disk.html">Disk Controller</a><div class="addr-size">Floppy DMA, MFM encoding, DSKSYNC</div></div></div>
    <div class="addr-row"><div class="addr-label">$KBD001</div><div class="addr-body"><a class="addr-name" href="amiga/keyboard.html">Keyboard</a><div class="addr-size">6500/1 microprocessor, serial protocol, key codes</div></div></div>
    <div class="addr-row"><div class="addr-label">$CONN01</div><div class="addr-body"><a class="addr-name" href="amiga/connectors.html">Connectors</a><div class="addr-size">RGB, serial, parallel, floppy, game ports, expansion</div></div></div>
</div>
