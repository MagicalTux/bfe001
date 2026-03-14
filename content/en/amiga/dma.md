---
title: "DMA & Interrupts"
addr: "$DFF096"
description: "DMA channel control, bus cycle allocation, and the Amiga's 14-source interrupt system managed by PAULA."
order: 5
---

<!-- section: $DFF096 "DMA Overview" -->

All data flowing between the custom chips and Chip RAM passes through **DMA (Direct Memory Access)** channels controlled by Agnus. The 68000 CPU is just one of many bus masters competing for access to memory. Each custom chip subsystem — bitplanes, sprites, disk, audio, Copper, and Blitter — has its own DMA channel that can read or write Chip RAM independently of the processor.

DMA transfers work through three types of registers for each I/O unit:

- **Control registers** — programmed by the CPU to configure parameters (resolution, volume, disk track, etc.)
- **Data registers** — hold the actual data being transferred between the unit and Chip RAM
- **Address registers (pointers)** — tell the DMA controller where in Chip RAM to read from or write to

The central DMA control register, **DMACON**, enables or disables each channel individually. A master enable bit must also be set for any channel to operate.

### The Six DMA Channels

**Bitplane DMA** — reads display data from Chip RAM into the bitplane data registers, feeding the video sequencers in Denise for screen output.

**Sprite DMA** — transfers sprite image data from Chip RAM into sprite data registers.

**Disk DMA** — bidirectional transfer of floppy disk data between the disk controller and Chip RAM.

**Audio DMA** — reads digitized sound sample data from Chip RAM into the audio channel data registers.

**Copper DMA** — fetches Copper coprocessor instructions from Chip RAM.

**Blitter DMA** — transfers data to and from the Blitter for area fill, line draw, and block copy operations.

<!-- section: $DFF096 "Bus Cycle Allocation" -->

The Amiga's bus timing is synchronized to the video raster. One horizontal raster line lasts approximately 63.5 microseconds and comprises 227.5 bus cycles. Each bus cycle is about 280 nanoseconds — during which one memory access can occur.

### Odd and Even Cycles

The key insight to Amiga bus architecture is the division of cycles into **odd** and **even**:

- **Odd cycles** are reserved exclusively for DMA. The 68000 cannot use them.
- **Even cycles** are shared between the CPU, Copper, and Blitter. DMA always has priority.

The 68000 runs at a clock frequency that only allows one memory access every 560 ns — exactly two bus cycles. It can therefore only claim every other (even) cycle. This means the odd-cycle DMA channels (sprites, audio, disk, refresh) operate without slowing down the processor at all.

### DMA Priority and Cycle Assignment

Within each raster line, DMA slots are assigned in a fixed order:

<div class="register-block">
<div class="reg-title">DMA Cycle Assignment (per raster line)</div>
<div class="reg-row"><span class="reg-field">$00–$03</span><span class="reg-val">Refresh DMA — 4 cycles for DRAM refresh (odd cycles)</span></div>
<div class="reg-row"><span class="reg-field">$04–$06</span><span class="reg-val">Disk DMA — 3 cycles (odd cycles)</span></div>
<div class="reg-row"><span class="reg-field">$07–$0E</span><span class="reg-val">Audio DMA — channels 0–3 (odd cycles)</span></div>
<div class="reg-row"><span class="reg-field">$0F–$26</span><span class="reg-val">Sprite DMA — sprites 0–7, 2 cycles each (odd cycles)</span></div>
<div class="reg-row"><span class="reg-field">DDFSTRT–DDFSTOP</span><span class="reg-val">Bitplane DMA — variable position, uses odd cycles (up to 4 planes lores / 2 planes hires) and even cycles (5–6 planes lores / 3–4 planes hires)</span></div>
<div class="reg-row"><span class="reg-field">Even cycles</span><span class="reg-val">Copper DMA — uses free even cycles, priority over Blitter and CPU</span></div>
<div class="reg-row"><span class="reg-field">Even cycles</span><span class="reg-val">Blitter DMA — uses free even cycles, priority over CPU</span></div>
<div class="reg-row"><span class="reg-field">Even cycles</span><span class="reg-val">68000 CPU — gets whatever even cycles remain</span></div>
</div>

In low resolution with 4 or fewer bitplanes, all bitplane data fits in odd cycles, leaving even cycles free for the CPU. With 5 or 6 bitplanes in lores, two even cycles per 8-cycle group are consumed by bitplane DMA, reducing CPU bandwidth. In high resolution, 2 bitplanes fit in odd cycles, but 3 or 4 hires bitplanes steal even cycles — the CPU can lose over half its bus time.

Programs running from **Fast RAM** or **Kickstart ROM** are unaffected by DMA contention, since those buses are independent of the Chip RAM bus.

<!-- section: $DFF096 "Screen Structure & Raster" -->

The Amiga's video output follows the **PAL** standard (or NTSC in North American models). Understanding the raster is essential because all DMA timing is derived from it.

### PAL Timing

A PAL frame consists of 625 lines at 50 fields per second. To avoid flicker while keeping line frequency manageable (15,625 Hz), the image is split into two **interlaced** fields:

- **Long Frame** (odd lines: 1, 3, 5, ... 625) — 313 lines
- **Short Frame** (even lines: 2, 4, 6, ... 624) — 312 lines

In the Amiga's default non-interlaced mode, both fields display the same 313 lines at 50 Hz, producing a stable image with visible line spacing. Interlace mode can be enabled to double vertical resolution to 625 lines, at the cost of visible flicker on high-contrast horizontal edges.

### Beam Position Registers

Agnus maintains an internal counter tracking the electron beam's position. The CPU can read this counter to determine exactly where in the frame it is.

<div class="register-block">
<div class="reg-title">Beam Position Registers</div>
<div class="reg-row"><span class="reg-field">$DFF006</span><span class="reg-val">VHPOSR — vertical (V7–V0) and horizontal (H8–H1) position (read)</span></div>
<div class="reg-row"><span class="reg-field">$DFF004</span><span class="reg-val">VPOSR — LOF flag (bit 15) and V8 (bit 0) (read)</span></div>
<div class="reg-row"><span class="reg-field">$DFF02C</span><span class="reg-val">VHPOSW — beam position (write)</span></div>
<div class="reg-row"><span class="reg-field">$DFF02A</span><span class="reg-val">VPOSW — LOF and V8 (write)</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Reg</span>
<span class="bit-dir">Bits</span>
<span class="bit-name">Name</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">VHPOS</span><span class="bit-dir">15–8</span><span class="bit-name">V7–V0</span><span class="bit-func">Vertical position (line number, lower 8 bits)</span></div>
<div class="bit-row"><span class="bit-num">VHPOS</span><span class="bit-dir">7–0</span><span class="bit-name">H8–H1</span><span class="bit-func">Horizontal position (bus cycle number, 0–$E3)</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">VPOS</span><span class="bit-dir">15</span><span class="bit-name">LOF</span><span class="bit-func">Long Frame flag (1 = long frame / odd lines)</span></div>
<div class="bit-row"><span class="bit-num">VPOS</span><span class="bit-dir">0</span><span class="bit-name">V8</span><span class="bit-func">Vertical position bit 8 (for lines 256–312)</span></div>
</div>

Horizontal positions range from $00 to $E3 (0–227), with $0F–$35 corresponding to the horizontal blanking interval. Vertical lines 0–25 are the vertical blanking interval, with the visible area starting at line 26 and extending to line 312.

The **LOF** bit indicates which field type is currently being displayed — set for Long Frame (odd lines), clear for Short Frame (even lines). In non-interlaced mode, LOF is always 1.

<!-- section: $DFF096 "DMACON Register" -->

The **DMACON** register is the central switch for all DMA channels. It uses a SET/CLR mechanism: you cannot write it directly, only set or clear individual bits.

<div class="register-block">
<div class="reg-title">DMACON Register Addresses</div>
<div class="reg-row"><span class="reg-field">$DFF096</span><span class="reg-val">DMACON — DMA control (write)</span></div>
<div class="reg-row"><span class="reg-field">$DFF002</span><span class="reg-val">DMACONR — DMA control (read)</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func">Details</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">SET/CLR</span><span class="bit-name">Set/Clear control</span><span class="bit-func">1 = set listed bits; 0 = clear listed bits</span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">BBUSY</span><span class="bit-name">Blitter busy</span><span class="bit-func">Read-only: 1 when Blitter is operating</span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">BZERO</span><span class="bit-name">Blitter zero</span><span class="bit-func">Read-only: 1 when all Blitter output was zero</span></div>
<div class="bit-row"><span class="bit-num">12–11</span><span class="bit-dir">—</span><span class="bit-name">Unused</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">BLTPRI</span><span class="bit-name">Blitter priority</span><span class="bit-func">1 = Blitter has absolute priority over CPU</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">9</span><span class="bit-dir">DMAEN</span><span class="bit-name">Master DMA enable</span><span class="bit-func">Must be set for any DMA channel (bits 0–8) to operate</span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">BPLEN</span><span class="bit-name">Bitplane DMA</span><span class="bit-func">Enable bitplane display DMA</span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">COPEN</span><span class="bit-name">Copper DMA</span><span class="bit-func">Enable Copper coprocessor DMA</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">BLTEN</span><span class="bit-name">Blitter DMA</span><span class="bit-func">Enable Blitter DMA</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">SPREN</span><span class="bit-name">Sprite DMA</span><span class="bit-func">Enable sprite DMA</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">DSKEN</span><span class="bit-name">Disk DMA</span><span class="bit-func">Enable disk DMA</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">AUD3EN</span><span class="bit-name">Audio ch. 3</span><span class="bit-func">Enable audio channel 3 DMA</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">AUD2EN</span><span class="bit-name">Audio ch. 2</span><span class="bit-func">Enable audio channel 2 DMA</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">AUD1EN</span><span class="bit-name">Audio ch. 1</span><span class="bit-func">Enable audio channel 1 DMA</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">AUD0EN</span><span class="bit-name">Audio ch. 0</span><span class="bit-func">Enable audio channel 0 DMA</span></div>
</div>

### SET/CLR Mechanism

DMACON cannot be written like an ordinary register. Instead, bit 15 determines the operation:

- **Bit 15 = 1:** All bits set to 1 in the written value will be **set** in DMACON. Other bits are unchanged.
- **Bit 15 = 0:** All bits set to 1 in the written value will be **cleared** in DMACON. Other bits are unchanged.

A DMA channel is active only when **both** its individual enable bit **and** the master DMAEN bit (bit 9) are set.

```asm
    ; Enable bitplane and Copper DMA:
    MOVE.W #$8380,$DFF096    ; SET/CLR=1, DMAEN=1, BPLEN=1, COPEN=1

    ; Disable Blitter DMA only:
    MOVE.W #$0040,$DFF096    ; SET/CLR=0, BLTEN=1 → clears BLTEN
```

### Blitter Priority (BLTPRI)

When BLTPRI is set, the Blitter takes absolute priority over the 68000 — the CPU gets no even-cycle bus access during Blitter operations. When clear, the CPU is granted one cycle out of every four even bus cycles, preventing it from being locked out entirely. This is critical when the CPU must service time-sensitive interrupts or access OS data structures in Chip RAM.

<!-- section: $DFF096 "Interrupt System" -->

The Amiga's interrupt system is managed by **PAULA**, which collects interrupt requests from all sources and generates the appropriate 68000 interrupt level signals. There are 14 interrupt sources organized into 6 priority levels (level 7 / NMI is not used).

Two registers control the system, both using the same SET/CLR mechanism as DMACON:

<div class="register-block">
<div class="reg-title">Interrupt Registers</div>
<div class="reg-row"><span class="reg-field">$DFF09C</span><span class="reg-val">INTREQ — Interrupt request (write)</span></div>
<div class="reg-row"><span class="reg-field">$DFF01E</span><span class="reg-val">INTREQR — Interrupt request (read)</span></div>
<div class="reg-row"><span class="reg-field">$DFF09A</span><span class="reg-val">INTENA — Interrupt enable (write)</span></div>
<div class="reg-row"><span class="reg-field">$DFF01C</span><span class="reg-val">INTENAR — Interrupt enable (read)</span></div>
</div>

Both INTREQ and INTENA share the same bit layout:

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Level</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">SET/CLR</span><span class="bit-name">—</span><span class="bit-func">Set/Clear control bit</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">14</span><span class="bit-dir">INTEN</span><span class="bit-name">(6)</span><span class="bit-func">Master interrupt enable (must be set for any interrupt to fire)</span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">EXTER</span><span class="bit-name">6</span><span class="bit-func">CIA-B interrupt or expansion port</span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">DSKSYN</span><span class="bit-name">5</span><span class="bit-func">Disk sync word match detected</span></div>
<div class="bit-row"><span class="bit-num">11</span><span class="bit-dir">RBF</span><span class="bit-name">5</span><span class="bit-func">Serial port receive buffer full</span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">AUD3</span><span class="bit-name">4</span><span class="bit-func">Audio channel 3 data sent</span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">AUD2</span><span class="bit-name">4</span><span class="bit-func">Audio channel 2 data sent</span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">AUD1</span><span class="bit-name">4</span><span class="bit-func">Audio channel 1 data sent</span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">AUD0</span><span class="bit-name">4</span><span class="bit-func">Audio channel 0 data sent</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">BLIT</span><span class="bit-name">3</span><span class="bit-func">Blitter operation finished</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">VERTB</span><span class="bit-name">3</span><span class="bit-func">Vertical blank — start of each frame (line 0, 50 Hz)</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">COPER</span><span class="bit-name">3</span><span class="bit-func">Copper-generated interrupt</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">PORTS</span><span class="bit-name">2</span><span class="bit-func">CIA-A interrupt or expansion port</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">SOFT</span><span class="bit-name">1</span><span class="bit-func">Software interrupt (triggered by writing to INTREQ)</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">DSKBLK</span><span class="bit-name">1</span><span class="bit-func">Disk DMA transfer complete</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">TBE</span><span class="bit-name">1</span><span class="bit-func">Serial port transmit buffer empty</span></div>
</div>

### How Interrupts Fire

For an interrupt to reach the 68000, three conditions must be met:

1. The corresponding bit in **INTREQ** is set (the hardware source is requesting service)
2. The corresponding bit in **INTENA** is set (the source is enabled)
3. The **INTEN** bit (bit 14) in INTENA is set (master enable is on)

When all three conditions are true, PAULA asserts the appropriate interrupt level on the 68000's IPL pins.

### Acknowledging Interrupts

After servicing an interrupt, the handler **must** clear the corresponding bit in INTREQ by writing to it. The hardware does not auto-clear request bits. For CIA interrupts, note that reading the CIA's ICR register clears the CIA-side flags, but the corresponding INTREQ bit (3 for CIA-A, 13 for CIA-B) must still be explicitly cleared.

<!-- section: $DFF096 "Interrupt Vectors" -->

The 68000 uses autovectored interrupts on the Amiga. Each priority level maps to a fixed vector address:

<div class="register-block">
<div class="reg-title">68000 Interrupt Vectors</div>
<div class="reg-row"><span class="reg-field">$064 (Vec 25)</span><span class="reg-val">Level 1 — TBE, DSKBLK, SOFT</span></div>
<div class="reg-row"><span class="reg-field">$068 (Vec 26)</span><span class="reg-val">Level 2 — PORTS (CIA-A, expansion)</span></div>
<div class="reg-row"><span class="reg-field">$06C (Vec 27)</span><span class="reg-val">Level 3 — COPER, VERTB, BLIT</span></div>
<div class="reg-row"><span class="reg-field">$070 (Vec 28)</span><span class="reg-val">Level 4 — AUD0, AUD1, AUD2, AUD3</span></div>
<div class="reg-row"><span class="reg-field">$074 (Vec 29)</span><span class="reg-val">Level 5 — RBF, DSKSYN</span></div>
<div class="reg-row"><span class="reg-field">$078 (Vec 30)</span><span class="reg-val">Level 6 — EXTER (CIA-B, expansion), INTEN</span></div>
<div class="reg-row"><span class="reg-field">$07C (Vec 31)</span><span class="reg-val">Level 7 — NMI (not used by Amiga hardware)</span></div>
</div>

Higher level numbers indicate higher priority. A level 6 interrupt can preempt a level 3 handler, but not vice versa. Since multiple sources share a level, the interrupt handler must read INTREQR to determine which source triggered the interrupt.

### CIA Interrupt Routing

The two CIAs route their interrupts through PAULA differently:

- **CIA-A** triggers the **PORTS** interrupt (bit 3, level 2). The specific CIA-A source is identified by reading CIA-A's ICR register.
- **CIA-B** triggers the **EXTER** interrupt (bit 13, level 6). The specific CIA-B source is identified by reading CIA-B's ICR register.

Both PORTS and EXTER can also be triggered by expansion cards on the Zorro bus.

### Software Interrupts and Copper Interrupts

The SOFT bit (bit 2) allows software to generate a level 1 interrupt by writing to INTREQ. The Copper can also generate interrupts — since it can only perform MOVE operations, it triggers the COPER interrupt (bit 4, level 3) by writing the appropriate value to INTREQ:

```asm
    ; Copper instruction to trigger an interrupt:
    MOVE #$8010,INTREQ    ; SET bit 4 (COPER) in INTREQ
```

The **VERTB** interrupt (bit 5, level 3) fires at the start of every vertical blank period (line 0), 50 times per second in PAL. This is the primary timing reference for frame-synchronized operations — updating animation, reading input, and reinitializing bitplane pointers via the Copper.
