---
title: "Custom Chips"
addr: "$DFF000"
description: "The Amiga's custom chipset — Agnus, Denise, and Paula — three specialized processors that define the architecture."
order: 3
---

<!-- section: $DFF000 "The Custom Chipset" -->

The Amiga's unique capabilities come from three custom LSI chips designed by Jay Miner's team at Amiga Inc. Together they form a parallel processing architecture where graphics, sound, and I/O operate independently of the 68000 CPU via DMA. All three chips share a register space starting at **$DFF000**, and are collectively controlled through approximately 200 write-only registers.

The custom chips reside on the "Chip bus" and can only access **Chip RAM** (the first 512 KB on the A500, expandable to 1 MB on ECS). This is the fundamental constraint of the Amiga architecture — all graphics data, sound samples, Copper lists, and sprite data must reside in Chip RAM.

<!-- section: $DFF000 "Agnus (8375)" -->

**Agnus** (Address Generator Unit) is the master chip of the custom chipset. In the Amiga 500, this is the Fat Agnus (8375) — a large PLCC-84 package that sits near the center of the motherboard.

Agnus is responsible for:

- **DMA controller** — manages all 25 DMA channels, arbitrating access to Chip RAM between the 68000, Copper, Blitter, bitplane fetch, sprite fetch, audio, and disk
- **Address generation** — generates all DMA addresses, multiplexing access to Chip RAM through the bus cycle allocation scheme
- **Copper** — the display coprocessor is built into Agnus
- **Blitter** — the block image transfer engine is also part of Agnus
- **Beam counters** — tracks the video beam position (VHPOSR/VHPOSW at $DFF004/$DFF02C)

<div class="register-block">
<div class="reg-title">Key Agnus Registers</div>
<div class="reg-row"><span class="reg-field">$DFF004 R</span><span class="reg-val">VPOSR — Beam position (vertical high bits + chip ID)</span></div>
<div class="reg-row"><span class="reg-field">$DFF006 R</span><span class="reg-val">VHPOSR — Beam position (vertical low + horizontal)</span></div>
<div class="reg-row"><span class="reg-field">$DFF096 W</span><span class="reg-val"><span class="highlight">DMACON — DMA control (enable/disable all DMA channels)</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF07C R</span><span class="reg-val">DENISEID — Chip revision identification</span></div>
</div>

### Fat Agnus (8375)

The original Agnus in the A1000 could address only 512 KB of Chip RAM. The **Fat Agnus** (8375) in the A500 and A2000 extends this to 1 MB by adding an extra address line. The Fat Agnus also integrates the PAL/NTSC timing generation, making the chip region-specific — PAL and NTSC Fat Agnus chips are not interchangeable.

<!-- section: $DFF000 "Denise (8362)" -->

**Denise** (Display ENabler) handles all video output. It receives bitplane, sprite, and color data from Agnus and produces the final analog RGB signal sent to the monitor.

Denise is responsible for:

- **Bitplane-to-pixel conversion** — serializes up to 6 bitplane DMA streams into pixel color values
- **Color palette** — 32 color registers (COLOR00–COLOR31 at $DFF180–$DFF1BE) with 12-bit RGB (4096 colors)
- **Sprite rendering** — composites up to 8 sprites over the playfield
- **Playfield priority** — determines sprite-vs-playfield draw order (BPLCON2)
- **Collision detection** — hardware collision between sprites and playfields (CLXDAT/CLXCON)
- **Display modes** — HAM (Hold-And-Modify), Extra Half Brite (EHB), dual playfield

<div class="register-block">
<div class="reg-title">Key Denise Registers</div>
<div class="reg-row"><span class="reg-field">$DFF100 W</span><span class="reg-val">BPLCON0 — Bitplane control (number of planes, resolution, HAM/EHB enable)</span></div>
<div class="reg-row"><span class="reg-field">$DFF102 W</span><span class="reg-val">BPLCON1 — Horizontal scroll values for playfields</span></div>
<div class="reg-row"><span class="reg-field">$DFF104 W</span><span class="reg-val">BPLCON2 — Sprite-playfield priority and playfield transparency</span></div>
<div class="reg-row"><span class="reg-field">$DFF180 W</span><span class="reg-val"><span class="highlight">COLOR00 — Background color (and first of 32 color registers)</span></span></div>
</div>

Denise outputs analog RGB through the 23-pin video connector. Each color channel has 4 bits of precision, giving 16 intensity levels per channel and a total palette of 4096 colors. The actual number of simultaneous colors depends on the display mode — from 2 colors (1 bitplane) up to 4096 in HAM mode.

<!-- section: $DFF000 "Paula (8364)" -->

**Paula** handles audio output, disk I/O, UART serial communication, and interrupt management. She is the "I/O hub" of the custom chipset.

Paula is responsible for:

- **Audio** — 4 independent 8-bit DMA audio channels with per-channel volume control, stereo output (channels 0+3 left, 1+2 right), and inter-channel modulation
- **Disk controller** — MFM floppy disk DMA, sync word detection, read/write control
- **Serial port** — UART for RS232 communication
- **Interrupt controller** — collects interrupt requests from all sources, prioritizes them, and drives the 68000's IPL lines
- **Analog input** — potentiometer/paddle input measurement (POTGO/POTINP)

<div class="register-block">
<div class="reg-title">Key Paula Registers</div>
<div class="reg-row"><span class="reg-field">$DFF09A W</span><span class="reg-val"><span class="highlight">INTENA — Interrupt enable (master switch + per-source enables)</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF09C W</span><span class="reg-val">INTREQ — Interrupt request (set/clear individual interrupt flags)</span></div>
<div class="reg-row"><span class="reg-field">$DFF01E R</span><span class="reg-val">INTREQR — Interrupt request read</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A0 W</span><span class="reg-val">AUD0LCH — Audio channel 0 location pointer (first of 16 audio registers)</span></div>
<div class="reg-row"><span class="reg-field">$DFF020 W</span><span class="reg-val">DSKPTH — Disk DMA pointer (high word)</span></div>
<div class="reg-row"><span class="reg-field">$DFF030 W</span><span class="reg-val">SERDAT — Serial port data write</span></div>
</div>

<!-- section: $DFF000 "Gary (5719)" -->

**Gary** is the address decoder and bus controller — the "glue logic" chip. It is not part of the custom chipset proper but is essential for system operation.

Gary is responsible for:

- **Address decoding** — determines which chip responds to each 68000 bus cycle (Chip RAM, Kickstart ROM, CIA-A, CIA-B, custom chips, or expansion bus)
- **DTACK generation** — signals the 68000 that a bus cycle has completed
- **ROM overlay** — controls the ROM overlay at reset (mapping Kickstart ROM to address $000000 until the first write to the OVL bit)
- **Bus timeout** — generates bus error on access to unmapped addresses

Gary also handles the address line mapping that places CIA-A at odd addresses ($BFE001, $BFE101, etc.) and CIA-B at even addresses ($BFD000, $BFD100, etc.) — the origin of this site's name.

<!-- section: $DFF000 "Bus Cycle Allocation" -->

The Amiga's bus runs at approximately 3.58 MHz (one memory cycle per 280 ns). Each horizontal line of the display consists of 227.5 color clocks (PAL), and each color clock is one bus cycle. These cycles are divided into two categories:

**Odd cycles** — reserved for bitplane DMA, sprite DMA, audio DMA, disk DMA, and refresh. The 68000 has no access during odd cycles.

**Even cycles** — shared between the Copper, Blitter, and 68000. The Copper has highest priority, then the Blitter (if BLTPRI is set), then the 68000.

This interleaved scheme allows the custom chips to maintain continuous DMA streams (e.g., fetching bitplane data every line) without visibly interrupting the 68000 — as long as DMA activity stays within its allotted bandwidth. When too many DMA channels are active (e.g., 6 bitplanes in hires + all sprites), the 68000 may be starved of bus cycles entirely.
