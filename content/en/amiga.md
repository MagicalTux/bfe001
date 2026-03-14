---
title: "Amiga Technical Reference — BFE001"
description: "The Amiga's custom chipset, memory map, and CIA register details — the hardware behind $BFE001."
---

<!-- section: $DFF000 "Chipset Architecture" -->

The Amiga's power came from its custom chipset — three specialized chips
that handled graphics, sound, and DMA independently of the CPU. While the
Motorola 68000 ran your code, the custom chips ran the machine.

<div class="chip-diagram">
    <div class="chip-box chip-cpu">
        <div class="chip-name">Motorola 68000</div>
        <div class="chip-detail">16-bit data bus</div>
        <div class="chip-detail">24-bit address bus</div>
    </div>
    <div class="chip-bus no-stub"></div>
    <div class="chip-row chip-connected">
        <div class="chip-box">
            <div class="chip-name">AGNUS</div>
            <div class="chip-detail">DMA controller</div>
            <div class="chip-detail">Copper</div>
            <div class="chip-detail">Blitter</div>
            <div class="chip-detail">Beam counter</div>
        </div>
        <div class="chip-box">
            <div class="chip-name">DENISE</div>
            <div class="chip-detail">Video output</div>
            <div class="chip-detail">Bitplanes</div>
            <div class="chip-detail">Sprites</div>
            <div class="chip-detail">Collision</div>
        </div>
        <div class="chip-box">
            <div class="chip-name">PAULA</div>
            <div class="chip-detail">4-ch audio</div>
            <div class="chip-detail">Disk I/O</div>
            <div class="chip-detail">Serial I/O</div>
            <div class="chip-detail">Interrupts</div>
        </div>
    </div>
    <div class="chip-bus no-stub"></div>
    <div class="chip-row chip-connected">
        <div class="chip-box chip-highlight">
            <div class="chip-addr">$BFE001</div>
            <div class="chip-name">CIA-A</div>
            <div class="chip-detail">Keyboard</div>
            <div class="chip-detail">Game ports</div>
            <div class="chip-detail">LED / Filter</div>
            <div class="chip-detail">Timers A/B</div>
        </div>
        <div class="chip-box">
            <div class="chip-addr">$BFD000</div>
            <div class="chip-name">CIA-B</div>
            <div class="chip-detail">Parallel port</div>
            <div class="chip-detail">Disk control</div>
            <div class="chip-detail">Serial ctrl</div>
            <div class="chip-detail">Timers C/D</div>
        </div>
    </div>
</div>

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
