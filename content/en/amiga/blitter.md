---
title: "Blitter"
addr: "$DFF040"
description: "The Amiga Blitter — a high-speed Block Image Transferrer capable of data copying, logical combination, area filling, and line drawing."
order: 9
---

<!-- section: $DFF040 "Blitter Overview" -->

The **Blitter** (Block Image Transferrer) is a dedicated hardware unit in the Agnus chip that copies and combines blocks of memory at very high speed. It can process over **16 million pixels per second** — far faster than the 68000 CPU could manage with software alone.

The Blitter has three main operating modes:

- **Data copy and logical combination** — transfer memory blocks while combining up to three source channels through any of 256 Boolean functions
- **Area fill** — fill enclosed regions bounded by single-pixel edges
- **Line drawing** — draw lines at up to 1 million pixels per second using a hardware implementation of the Bresenham algorithm

AmigaOS uses the Blitter for virtually all graphics operations: rendering text, drawing gadgets, scrolling windows, and clipping graphical data.

### Basic Operation

The Blitter operates on rectangular memory regions (the "Blitter window"). It reads data from up to three source channels — **A**, **B**, and **C** — combines them according to a chosen logic function, and writes the result to destination **D**. All four addresses can be anywhere in Chip RAM ($000000–$07FFFF).

The combination is performed **bit by bit**: for each bit position, the corresponding bits from A, B, and C are fed into the logic function to produce the output bit for D. This allows operations ranging from a simple copy (D=A) to complex multi-source compositing.

<!-- section: $DFF040 "Blitter Window and Modulo" -->

The Blitter window defines the rectangular region of memory processed in one operation. It is specified in the **BLTSIZE** register as a height (in lines) and width (in words).

| Register | Bits | Name | Function |
|----------|------|------|----------|
| BLTSIZE | 15–6 | H9–H0 | Height — 1 to 1024 lines (0 = 1024) |
| BLTSIZE | 5–0 | W5–W0 | Width — 1 to 64 words (0 = 64, max 1024 pixels) |

The register value is computed as: `BLTSIZE = Height * 64 + Width`

**Writing to BLTSIZE starts the Blitter immediately.** It must always be the last register programmed in any Blitter operation.

The Blitter processes its window line by line, starting with the first word of the first line and finishing with the last word of the last line. Within each line, words are processed sequentially.

### Source and Destination Pointers

Each channel has a pointer register pair holding the start address:

<div class="register-block">
<div class="reg-title">Blitter Pointer Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF050</span><span class="reg-val">BLTAPTH/L — Source A pointer (bits 16–18 / 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF04C</span><span class="reg-val">BLTBPTH/L — Source B pointer (bits 16–18 / 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF048</span><span class="reg-val">BLTCPTH/L — Source C pointer (bits 16–18 / 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF054</span><span class="reg-val">BLTDPTH/L — Destination D pointer (bits 16–18 / 0–15)</span></div>
</div>

### Modulo Values

When the Blitter window is smaller than the actual bitmap, **modulo** values tell the Blitter how many bytes to skip at the end of each line to reach the start of the next line. This allows blitting a sub-region of a larger bitmap without copying the entire thing.

<div class="register-block">
<div class="reg-title">Blitter Modulo Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF064</span><span class="reg-val">BLTAMOD — Source A modulo (bytes, signed)</span></div>
<div class="reg-row"><span class="reg-field">$DFF062</span><span class="reg-val">BLTBMOD — Source B modulo (bytes, signed)</span></div>
<div class="reg-row"><span class="reg-field">$DFF060</span><span class="reg-val">BLTCMOD — Source C modulo (bytes, signed)</span></div>
<div class="reg-row"><span class="reg-field">$DFF066</span><span class="reg-val">BLTDMOD — Destination D modulo (bytes, signed)</span></div>
</div>

For example, to copy a 10-word-wide graphic into a 20-word-wide bitplane: the source modulo is 0 (the graphic is exactly as wide as the Blitter window), and the destination modulo is `(20 - 10) * 2 = 20` bytes.

After each line, the Blitter adds the modulo value to the current pointer. After the entire operation completes, each pointer holds the address of the last word processed, plus 2, plus the modulo.

<!-- section: $DFF040 "Minterms (Logic Functions)" -->

The Blitter's logical combination of sources A, B, and C is controlled by 8 bits called **minterms** (LF0–LF7). These bits select which of the 8 possible input combinations produce a 1 in the output bit D. The results of all selected minterms are OR'd together.

| Bit | Minterm | Input ABC | Equation (1 when true) |
|-----|---------|-----------|------------------------|
| LF7 | ABC | 111 | A AND B AND C |
| LF6 | ABc | 110 | A AND B AND (NOT C) |
| LF5 | AbC | 101 | A AND (NOT B) AND C |
| LF4 | Abc | 100 | A AND (NOT B) AND (NOT C) |
| LF3 | aBC | 011 | (NOT A) AND B AND C |
| LF2 | aBc | 010 | (NOT A) AND B AND (NOT C) |
| LF1 | abC | 001 | (NOT A) AND (NOT B) AND C |
| LF0 | abc | 000 | (NOT A) AND (NOT B) AND (NOT C) |

### Common Minterm Values

- **D = A** (straight copy): LF7–LF0 = `$F0` (11110000). D=1 whenever A=1, regardless of B and C.
- **D = NOT A** (inversion): LF7–LF0 = `$0F` (00001111).
- **D = A OR B** (overlay graphic onto bitplane): LF7–LF0 = `$FC` (11111100).
- **D = A AND B** (mask): LF7–LF0 = `$C0` (11000000).
- **D = A XOR B**: LF7–LF0 = `$3C` or `$6C` depending on C usage.
- **Cookie cut** (D = AB + aC, masked blit): LF7–LF0 = `$CA`.

To determine the correct minterm: identify all input combinations where D should be 1, then set the corresponding LF bits.

When a source channel is not used, deactivate its DMA (USEx=0 in BLTCON0) and ensure the minterm excludes it from influencing D.

<!-- section: $DFF040 "Shifting and Masking" -->

### Barrel Shifter

The Blitter can shift the data from sources **A** and **B** by 0–15 bits to the right, using a barrel shifter. This allows pixel-accurate placement of graphics at any horizontal position, not just word boundaries.

The shift is applied across the entire line: bits shifted out of one word roll into the next. This operation costs no additional time — the Blitter runs at full speed regardless of the shift amount.

The shift values are stored in the upper 4 bits of the control registers:
- **ASH3–ASH0** (BLTCON0 bits 15–12): shift amount for source A
- **BSH3–BSH0** (BLTCON1 bits 15–12): shift amount for source B

### First/Last Word Masks

When the edges of a graphic do not align with word boundaries, the **first word mask** and **last word mask** on source A allow selective masking of unwanted bits at the beginning and end of each line.

<div class="register-block">
<div class="reg-title">Blitter Mask Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF044</span><span class="reg-val">BLTAFWM — Source A first word mask (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF046</span><span class="reg-val">BLTALWM — Source A last word mask (bits 0–15)</span></div>
</div>

Only bits that are **set** in the mask are passed through; masked bits are forced to 0. If the Blitter window is only 1 word wide, both masks are applied together (AND'd). For most copy operations, set both masks to `$FFFF`.

<!-- section: $DFF040 "Control Registers" -->

### BLTCON0 ($DFF040, write only)

| Bit | Name | Function | Description |
|-----|------|----------|-------------|
| 15–12 | ASH3–ASH0 | Source A shift | Right-shift amount for source A data (0–15) |
| 11 | USEA | Enable A DMA | Activate DMA channel for source A |
| 10 | USEB | Enable B DMA | Activate DMA channel for source B |
| 9 | USEC | Enable C DMA | Activate DMA channel for source C |
| 8 | USED | Enable D DMA | Activate DMA channel for destination D |
| 7–0 | LF7–LF0 | Minterms | Logic function selection (see above) |

When a USEx bit is 0, the corresponding DMA channel is disabled. The Blitter will reuse the last value written to that channel's data register (BLTxDAT). This can be exploited to fill memory with a pattern by writing a value to BLTxDAT via the CPU and then running the Blitter with that channel's DMA disabled.

### BLTCON1 ($DFF042, write only)

| Bit | Name | Function | Description |
|-----|------|----------|-------------|
| 15–12 | BSH3–BSH0 | Source B shift | Right-shift amount for source B data (0–15) |
| 11–5 | — | Unused | Must be 0 in copy mode |
| 4 | EFE | Exclusive fill | Enable exclusive fill mode |
| 3 | IFE | Inclusive fill | Enable inclusive fill mode |
| 2 | FCI | Fill carry in | Initial value of fill carry bit |
| 1 | DESC | Descending mode | Process addresses in descending order |
| 0 | LINE | Line draw mode | Enable line drawing mode (must be 0 for copy/fill) |

### Ascending vs. Descending Mode

In **ascending mode** (DESC=0, default), the Blitter starts at BLTxPT and increments the address after each word. In **descending mode** (DESC=1), it starts at BLTxPT and decrements. Descending mode is essential when the source and destination regions overlap with the destination at a higher address — ascending mode would overwrite source data before reading it.

Rules for choosing the mode:
- No overlap: either mode works
- Destination before source: ascending mode only
- Destination after source: descending mode only

In descending mode, BLTxPT must point to the **last word** (bottom-right corner) of the region rather than the first word (top-left).

<!-- section: $DFF040 "Area Fill" -->

The Blitter can fill enclosed areas by scanning each line from right to left and toggling a **fill carry (FC)** bit at each set pixel. Between two set pixels, all intervening pixels are filled.

The fill algorithm is simple: starting from the rightmost bit of each line, the FC bit is initially set to 0 (or 1 if FCI=1 in BLTCON1). For each input bit:
- If the input bit is 0, the output takes the current FC value
- If the input bit is 1, the FC bit is **toggled** (inverted)

This means areas between pairs of set bits are filled. An odd number of boundary pixels on a line will cause fill errors — the fill "leaks" to the edge of the line.

### Inclusive vs. Exclusive Fill

- **Inclusive fill (IFE=1)**: boundary pixels are preserved in the output. The filled area includes both edges.
- **Exclusive fill (EFE=1)**: the left boundary pixel (where FC transitions from 1 to 0) is cleared. This allows single-pixel-wide filled areas, which is impossible with inclusive fill.

### Fill Requirements

- **Descending mode must be used** (DESC=1) — the Blitter fills from right to left, which requires words to be processed from high to low addresses
- LINE must be 0
- The source data must contain proper single-pixel-wide boundary lines (no thick borders)
- BLTSIZE is set to the dimensions of the area to fill

Fill mode can be combined with a copy operation — the Blitter first computes D from the minterms, then applies the fill algorithm to the result before writing it to memory. Fill speed is the same as copy speed: up to 16 million pixels per second.

<!-- section: $DFF040 "Line Drawing" -->

The Blitter can draw lines at up to **1 million pixels per second** using a hardware Bresenham-style algorithm. To draw a line, you do not simply provide two endpoints — the Blitter requires pre-computed values that define the line's slope and direction.

### Octants

The direction from start point to end point is classified into one of **8 octants**. Three comparisons determine the octant:

1. Is X2 >= X1? (determines left/right)
2. Is Y2 >= Y1? (determines up/down)
3. Is DeltaX >= DeltaY? (determines major axis)

Each octant maps to a 3-bit code composed of three control bits:

| Octant | Conditions | SUD/SUL/AUL | Code |
|--------|-----------|-------------|------|
| 0 | X1<=X2, Y1<=Y2, dX>=dY | 1/1/0 | 6 |
| 1 | X1<=X2, Y1<=Y2, dX<=dY | 0/0/1 | 1 |
| 2 | X1>=X2, Y1<=Y2, dX<=dY | 0/1/1 | 3 |
| 3 | X1>=X2, Y1<=Y2, dX>=dY | 1/1/1 | 7 |
| 4 | X1>=X2, Y1>=Y2, dX>=dY | 1/0/1 | 5 |
| 5 | X1>=X2, Y1>=Y2, dX<=dY | 0/1/0 | 2 |
| 6 | X1<=X2, Y1>=Y2, dX<=dY | 0/0/0 | 0 |
| 7 | X1<=X2, Y1>=Y2, dX>=dY | 1/0/0 | 4 |

### Slope Computation

Let **Pdelta** = min(DeltaX, DeltaY) and **Gdelta** = max(DeltaX, DeltaY). The Blitter needs:

1. `2 * Pdelta` (written to BLTBMOD)
2. `2 * Pdelta - Gdelta` (written to BLTAPTL)
3. `2 * Pdelta - 2 * Gdelta` (written to BLTAMOD)

The **SIGN** flag (bit 6 in BLTCON1) is set to 1 when `2 * Pdelta < Gdelta`.

### Register Setup for Line Mode

In line drawing mode, registers take on different meanings:

<div class="register-block">
<div class="reg-title">Blitter Registers in Line Mode</div>
<div class="reg-row"><span class="reg-field">BLTAPTL</span><span class="reg-val">2 * Pdelta - Gdelta (initial error term)</span></div>
<div class="reg-row"><span class="reg-field">BLTAMOD</span><span class="reg-val">2 * Pdelta - 2 * Gdelta</span></div>
<div class="reg-row"><span class="reg-field">BLTBMOD</span><span class="reg-val">2 * Pdelta</span></div>
<div class="reg-row"><span class="reg-field">BLTCPT / BLTDPT</span><span class="reg-val">Address of the starting pixel in the bitplane</span></div>
<div class="reg-row"><span class="reg-field">BLTCMOD / BLTDMOD</span><span class="reg-val">Bitplane width in bytes (e.g. 40 for 320-pixel display)</span></div>
<div class="reg-row"><span class="reg-field">BLTADAT</span><span class="reg-val">$8000 (single pixel in bit 15)</span></div>
<div class="reg-row"><span class="reg-field">BLTBDAT</span><span class="reg-val">Line pattern mask (e.g. $FFFF for solid line)</span></div>
<div class="reg-row"><span class="reg-field">BLTAFWM</span><span class="reg-val">$FFFF</span></div>
<div class="reg-row"><span class="reg-field">BLTSIZE</span><span class="reg-val">Width=2, Height=line length (Gdelta + 1)</span></div>
</div>

**BLTCON0** in line mode: bits 15–12 = START position (X1 AND $F), bits 11–8 = `$B` (USEA=1, USEB=0, USEC=1, USED=1), bits 7–0 = `$CA` (minterm for D = aC + AB).

**BLTCON1** in line mode: bits 15–12 = same as START (for texture alignment), bit 6 = SIGN, bits 4–2 = SUD/SUL/AUL, bit 1 = SING (single pixel per horizontal line, for fill boundaries), bit 0 = LINE = 1.

The line pattern in BLTBDAT allows dashed and dotted lines. A mask of `$FFFF` draws a solid line; patterns like `$FF00` produce dashes.

<!-- section: $DFF040 "DMA and Status" -->

The Blitter uses 4 DMA channels (one per source/destination) controlled by the **BLTEN** bit (bit 6) in DMACON. All four channels are enabled together.

<div class="register-block">
<div class="reg-title">Blitter Data Registers</div>
<div class="reg-row"><span class="reg-field">$DFF000</span><span class="reg-val">BLTDDAT — Destination data output (read only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF070</span><span class="reg-val">BLTCDAT — Source C data register</span></div>
<div class="reg-row"><span class="reg-field">$DFF072</span><span class="reg-val">BLTBDAT — Source B data register</span></div>
<div class="reg-row"><span class="reg-field">$DFF074</span><span class="reg-val">BLTADAT — Source A data register</span></div>
</div>

### DMACON Status Bits

Three read-only bits in DMACON ($DFF002) report Blitter state:

- **Bit 14 — BBUSY**: Set to 1 while the Blitter is active. After writing BLTSIZE, the Blitter sets BBUSY and clears it when the operation completes. The Blitter-Finished interrupt (INTREQ bit 6) is triggered at the same time.

- **Bit 13 — BZERO**: Set to 1 if **all** output bits were zero during the last operation. This is useful for collision detection: configure the minterms so D=1 only when two sources overlap, disable USED (to avoid writing), and check BZERO afterward. If BZERO=0, at least one pixel overlapped.

- **Bit 10 — BLTPRI**: When set, the Blitter has absolute bus priority over the 68000 CPU (Blitter nasty mode). This maximizes Blitter throughput but stalls the processor completely until the Blitter finishes.

### Waiting for the Blitter

Before starting a new Blitter operation, always check that the previous one has finished by polling BBUSY:

```asm
.wait:
    btst    #6,$DFF002     ; test BBUSY in DMACONR
    bne.s   .wait          ; loop until Blitter is idle
```

Alternatively, enable the Blitter-Finished interrupt (INTENA bit 6) and handle completion asynchronously.
