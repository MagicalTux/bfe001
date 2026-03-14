---
title: "Playfields & Display"
addr: "$DFF100"
description: "Bitplane organization, resolution modes, color modes (HAM, EHB, Dual Playfield), display window, data fetch, and scrolling."
order: 7
---

<!-- section: $DFF100 "Bitplane Organization" -->

All Amiga graphics are built from **bitplanes** — contiguous blocks of memory where each bit represents one pixel. A single bitplane can only distinguish two states (on/off), so multiple bitplanes are combined to produce more colors. The value formed by combining the corresponding bit from each plane selects a color register.

### Memory Layout

A bitplane is stored as a sequence of 16-bit words in Chip RAM. The number of words per line determines the horizontal resolution:

- **Low resolution (320 pixels):** 320 / 16 = 20 words per line = 40 bytes
- **High resolution (640 pixels):** 640 / 16 = 40 words per line = 80 bytes

Within each word, bit 15 (MSB) corresponds to the leftmost pixel, and bit 0 (LSB) to the rightmost. The first word of the bitplane maps to the top-left corner of the display.

For a standard 320x200 lores display, one bitplane occupies 20 words x 200 lines = 4,000 words = 8,000 bytes. With 5 bitplanes (32 colors), the total is 40,000 bytes of Chip RAM.

### Resolution Modes

The Amiga supports two horizontal resolutions, selected by the HIRES bit in BPLCON0:

- **Low resolution (lores):** 320 pixels per line. Each pixel is displayed for 140 ns (2 color clocks). Up to **6 bitplanes** can be used.
- **High resolution (hires):** 640 pixels per line. Each pixel is displayed for 70 ns (1 color clock). Up to **4 bitplanes** can be used.

The vertical resolution is 200 lines in non-interlaced mode (256 lines for PAL overscan) or 400 lines (512 PAL overscan) in interlace mode.

### Bitplane Pointers

Each bitplane requires a 19-bit start address stored in a pair of registers. The DMA controller uses these pointers to read bitplane data from Chip RAM.

<div class="register-block">
<div class="reg-title">Bitplane Pointer Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF0E0</span><span class="reg-val">BPL1PTH / BPL1PTL — Bitplane 1 pointer</span></div>
<div class="reg-row"><span class="reg-field">$DFF0E4</span><span class="reg-val">BPL2PTH / BPL2PTL — Bitplane 2 pointer</span></div>
<div class="reg-row"><span class="reg-field">$DFF0E8</span><span class="reg-val">BPL3PTH / BPL3PTL — Bitplane 3 pointer</span></div>
<div class="reg-row"><span class="reg-field">$DFF0EC</span><span class="reg-val">BPL4PTH / BPL4PTL — Bitplane 4 pointer</span></div>
<div class="reg-row"><span class="reg-field">$DFF0F0</span><span class="reg-val">BPL5PTH / BPL5PTL — Bitplane 5 pointer</span></div>
<div class="reg-row"><span class="reg-field">$DFF0F4</span><span class="reg-val">BPL6PTH / BPL6PTL — Bitplane 6 pointer</span></div>
</div>

Each pointer pair is loaded with a single `MOVE.L` instruction. After each raster line, the DMA controller automatically increments the pointer by the number of words fetched. After the last visible line, the pointers must be **reset** to the start of each bitplane — this is typically done by the Copper at the beginning of each frame.

<!-- section: $DFF100 "Color Modes" -->

The Amiga generates colors by mixing three components — red, green, and blue — each with 4 bits of intensity (16 levels), giving a total palette of 4,096 possible colors ($000–$FFF). Each color register stores a 12-bit RGB value.

### The Color Palette

<div class="register-block">
<div class="reg-title">Color Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF180</span><span class="reg-val">COLOR00 — Background / border color</span></div>
<div class="reg-row"><span class="reg-field">$DFF182</span><span class="reg-val">COLOR01</span></div>
<div class="reg-row"><span class="reg-field">$DFF184–$DFF1BC</span><span class="reg-val">COLOR02–COLOR30</span></div>
<div class="reg-row"><span class="reg-field">$DFF1BE</span><span class="reg-val">COLOR31</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bits</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Range</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">15–12</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">Unused (always 0)</span></div>
<div class="bit-row"><span class="bit-num">11–8</span><span class="bit-dir">R3–R0</span><span class="bit-name">0–15</span><span class="bit-func">Red component</span></div>
<div class="bit-row"><span class="bit-num">7–4</span><span class="bit-dir">G3–G0</span><span class="bit-name">0–15</span><span class="bit-func">Green component</span></div>
<div class="bit-row"><span class="bit-num">3–0</span><span class="bit-dir">B3–B0</span><span class="bit-name">0–15</span><span class="bit-func">Blue component</span></div>
</div>

The combined bitplane value acts as an index into the color palette. The number of simultaneously displayable colors depends on the number of active bitplanes:

| Bitplanes | Colors | Registers Used |
|-----------|--------|----------------|
| 1 | 2 | COLOR00–01 |
| 2 | 4 | COLOR00–03 |
| 3 | 8 | COLOR00–07 |
| 4 | 16 | COLOR00–15 |
| 5 | 32 | COLOR00–31 |

In high resolution, only 4 bitplanes are available, limiting the display to 16 simultaneous colors.

### Extra Half-Brite (EHB) Mode

In low resolution with all 6 bitplanes active, the sixth bitplane serves a special purpose: it controls brightness rather than acting as an additional color index bit.

Bitplanes 1–5 (bits 0–4) select a color register as usual (0–31). If bit 5 (from bitplane 6) is **0**, the color is displayed at full brightness. If bit 5 is **1**, each RGB component is shifted right by one bit (divided by 2), producing a half-brightness version of the same color.

This gives 64 effective colors — 32 at full brightness and 32 at half brightness — without requiring additional color registers.

### Hold-And-Modify (HAM) Mode

HAM mode allows all **4,096 colors** to appear simultaneously on screen. It requires 6 bitplanes in low resolution and exploits the spatial coherence of natural images — adjacent pixels tend to have similar colors.

The two high-order bits (from bitplanes 5 and 6) determine how the four low-order bits (from bitplanes 1–4) are interpreted:

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit 5</span>
<span class="bit-dir">Bit 4</span>
<span class="bit-name">Low bits</span>
<span class="bit-func">Action</span>
</div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">0</span><span class="bit-name">C3–C0</span><span class="bit-func">Select color register C0–C3 directly (16 palette colors)</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">1</span><span class="bit-name">B3–B0</span><span class="bit-func">Hold red and green from previous pixel, modify blue</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">0</span><span class="bit-name">R3–R0</span><span class="bit-func">Hold blue and green from previous pixel, modify red</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">1</span><span class="bit-name">G3–G0</span><span class="bit-func">Hold red and blue from previous pixel, modify green</span></div>
</div>

For the first pixel of each line, the "previous" color is COLOR00 (the background color). Only one color component can change per pixel, so smooth gradients across all three channels require at least three pixels. Despite this constraint, HAM produces remarkably natural-looking images, especially for photographic content.

### Dual Playfield Mode

Dual playfield mode displays two independent playfields overlaid on each other. The odd-numbered bitplanes (1, 3, 5) form **playfield 1**, and the even-numbered bitplanes (2, 4, 6) form **playfield 2**.

Each playfield has up to 3 bitplanes, giving 8 colors each. Playfield 1 uses COLOR00–07, playfield 2 uses COLOR08–15. A pixel value of 0 in either playfield is **transparent**, allowing the other playfield (or the background) to show through.

| Bitplanes Active | Playfield 1 | Playfield 2 |
|------------------|-------------|-------------|
| 2 | Plane 1 | Plane 2 |
| 4 | Planes 1, 3 | Planes 2, 4 |
| 6 | Planes 1, 3, 5 | Planes 2, 4, 6 |

In hires dual playfield mode, each playfield has only 2 bitplanes (4 colors). The PF2PRI bit in BPLCON2 controls which playfield appears in front when both have non-transparent pixels at the same position.

This mode is commonly used in games for parallax scrolling, where background and foreground layers scroll at different speeds.

<!-- section: $DFF100 "Display Window & Data Fetch" -->

The playfield's position and size on screen are controlled by two pairs of registers: the **display window** registers define the visible area, and the **data fetch** registers define when bitplane DMA occurs.

### Display Window (DIWSTRT / DIWSTOP)

<div class="register-block">
<div class="reg-title">Display Window Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF08E</span><span class="reg-val">DIWSTRT — Display window start position</span></div>
<div class="reg-row"><span class="reg-field">$DFF090</span><span class="reg-val">DIWSTOP — Display window stop position</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Reg</span>
<span class="bit-dir">Bits</span>
<span class="bit-name">Name</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">DIWSTRT</span><span class="bit-dir">15–8</span><span class="bit-name">V7–V0</span><span class="bit-func">Vertical start position (line number)</span></div>
<div class="bit-row"><span class="bit-num">DIWSTRT</span><span class="bit-dir">7–0</span><span class="bit-name">H7–H0</span><span class="bit-func">Horizontal start position (pixel)</span></div>
<div class="bit-row"><span class="bit-num">DIWSTOP</span><span class="bit-dir">15–8</span><span class="bit-name">V7–V0</span><span class="bit-func">Vertical stop position (V8 generated from V7 inversion)</span></div>
<div class="bit-row"><span class="bit-num">DIWSTOP</span><span class="bit-dir">7–0</span><span class="bit-name">H7–H0</span><span class="bit-func">Horizontal stop position (H8 hardwired to 1)</span></div>
</div>

DIWSTRT defines the top-left corner where the playfield begins. DIWSTOP defines the bottom-right corner + 1 (the first position *after* the playfield). Outside the display window, the border color (COLOR00) is shown.

The missing high bits are generated by hardware: for DIWSTRT, V8 and H8 are assumed 0 (start position limited to lines 0–255 and columns 0–255). For DIWSTOP, H8 is hardwired to 1, and V8 is the inversion of V7, allowing stop positions in the range 128–312 vertically and 256–511 horizontally.

**Standard values** for a centered 320x256 display:

```
DIWSTRT = $2C81  (line 44, column 129)
DIWSTOP = $2CC1  (line 300, column 449)
```

The visible area of a PAL display spans lines 26–312 vertically and columns beyond 107 horizontally. Pixels outside these ranges fall within the blanking intervals and cannot be seen.

### Data Fetch (DDFSTRT / DDFSTOP)

<div class="register-block">
<div class="reg-title">Data Fetch Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF092</span><span class="reg-val">DDFSTRT — Data fetch start (first bitplane DMA cycle)</span></div>
<div class="reg-row"><span class="reg-field">$DFF094</span><span class="reg-val">DDFSTOP — Data fetch stop (last bitplane DMA cycle)</span></div>
</div>

These registers specify the horizontal bus cycle positions where bitplane DMA begins and ends. The hardware needs data to be fetched ahead of when it is displayed — 8.5 bus cycles (17 lores pixels) before display start in lores, or 4.5 cycles (9 hires pixels) in hires.

**Calculating DDFSTRT and DDFSTOP (lores):**

```
DDFSTRT = (Hstart/2 - 8.5) AND $FFF8
DDFSTOP = DDFSTRT + (pixels_per_line/2 - 8)
```

For Hstart = $81, 320 pixels per line:
```
DDFSTRT = ($81/2 - 8.5) AND $FFF8 = $38
DDFSTOP = $38 + (320/2 - 8) = $D0
```

**Calculating DDFSTRT and DDFSTOP (hires):**

```
DDFSTRT = (Hstart/2 - 4.5) AND $FFFC
DDFSTOP = DDFSTRT + (pixels_per_line/4 - 8)
```

For Hstart = $81, 640 pixels per line:
```
DDFSTRT = ($81/2 - 4.5) AND $FFFC = $3C
DDFSTOP = $3C + (640/4 - 8) = $D4
```

**Constraints:** DDFSTRT must not be below $18. The maximum DDFSTOP is $D8. Values of DDFSTRT below $28 are in the horizontal blanking area. Below $34, bitplane DMA overlaps sprite DMA slots, causing sprite loss — moving DDFSTRT to $30 eliminates sprites 1–7 (sprite 0, the mouse pointer, is preserved).

<!-- section: $DFF100 "Scrolling & Modulo" -->

The Amiga supports smooth scrolling of playfields larger than the visible display, using the modulo registers and the scroll value in BPLCON1.

### Modulo Registers

<div class="register-block">
<div class="reg-title">Modulo Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF108</span><span class="reg-val">BPL1MOD — Modulo for odd bitplanes (1, 3, 5)</span></div>
<div class="reg-row"><span class="reg-field">$DFF10A</span><span class="reg-val">BPL2MOD — Modulo for even bitplanes (2, 4, 6)</span></div>
</div>

The modulo value is added to BPLxPT after each displayed line. This allows a bitplane in memory to be **wider** than the display window. For example, if the bitplane is 640 pixels wide (40 words = 80 bytes per line) but only 320 pixels (20 words = 40 bytes) are displayed, the modulo should be set to 40 bytes — the difference between the full line width and the displayed width.

After DMA fetches the visible portion of a line, BPLxPT has been incremented by 40 bytes. Adding the modulo (40 bytes) advances it to the start of the next line in the wider bitplane, skipping the non-displayed portion.

For vertical scrolling, adjust BPLxPT to point at a different line within the bitplane. For example, to scroll down 100 lines in a 320-pixel-wide display, add 100 x 40 = 4,000 bytes to the base address. The Copper updates BPLxPT each frame.

Odd and even modulos are separate, allowing independent scrolling of the two playfields in dual playfield mode. In single playfield mode, both registers should contain the same value.

### Horizontal Fine Scrolling (BPLCON1)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bits</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Range</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">15–8</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">Unused</span></div>
<div class="bit-row"><span class="bit-num">7–4</span><span class="bit-dir">PF2H3–PF2H0</span><span class="bit-name">0–15</span><span class="bit-func">Scroll delay for even bitplanes (playfield 2)</span></div>
<div class="bit-row"><span class="bit-num">3–0</span><span class="bit-dir">PF1H3–PF1H0</span><span class="bit-name">0–15</span><span class="bit-func">Scroll delay for odd bitplanes (playfield 1)</span></div>
</div>

The scroll value delays the output of bitplane data by 0–15 low-resolution pixels, shifting the display to the **right**. Combined with BPLxPT adjustments in 16-pixel (one word) increments, this provides pixel-perfect horizontal scrolling.

**Scrolling right:** increment the scroll value from 0 to 15, then reset to 0 and decrement BPLxPT by one word.

**Scrolling left:** decrement the scroll value from 15 to 0, then reset to 15 and increment BPLxPT by one word.

To prevent visual artifacts at the left edge during scrolling, DDFSTRT should be set 8 bus cycles earlier than normal ($30 instead of $38 in lores). This fetches one extra word of data per line, which the scroll offset then positions correctly. The modulo value must be adjusted accordingly to account for the extra word.

BPLCON1 must be modified during vertical blanking — either via the Copper list or in a VERTB interrupt handler.

<!-- section: $DFF100 "BPLCON Registers" -->

Three control registers configure the display mode, scrolling, and sprite/playfield priorities.

### BPLCON0 ($DFF100)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Value</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">HIRES</span><span class="bit-name">0/1</span><span class="bit-func">High resolution mode (640 pixels per line)</span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">BPU2</span><span class="bit-name" rowspan="3">0–6</span><span class="bit-func">Number of active bitplanes (3-bit counter)</span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">BPU1</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">BPU0</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11</span><span class="bit-dir">HOMOD</span><span class="bit-name">0/1</span><span class="bit-func">Hold-And-Modify mode enable</span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">DBPLF</span><span class="bit-name">0/1</span><span class="bit-func">Dual Playfield mode enable</span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">COLOR</span><span class="bit-name">0/1</span><span class="bit-func">Composite color output enable (does not affect RGB)</span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">GAUD</span><span class="bit-name">0/1</span><span class="bit-func">Genlock audio enable</span></div>
<div class="bit-row"><span class="bit-num">7–4</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">Unused</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">LPEN</span><span class="bit-name">0/1</span><span class="bit-func">Light pen enable</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">2</span><span class="bit-dir">LACE</span><span class="bit-name">0/1</span><span class="bit-func">Interlace mode (doubles vertical resolution)</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">ERSY</span><span class="bit-name">0/1</span><span class="bit-func">External sync (for Genlock)</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">Unused</span></div>
</div>

**Important notes:**

- HOMOD and DBPLF must not be enabled simultaneously
- Extra-Half-Brite mode is activated automatically when 6 bitplanes are enabled (neither HOMOD nor DBPLF set)
- Setting LACE causes the LOF bit in VPOS to toggle each frame, alternating between long and short frames
- ERSY switches the sync signal pins from output to input, allowing an external video source to synchronize the Amiga's display (used with Genlock devices)

### BPLCON1 ($DFF102)

Horizontal scroll values for both playfields (see Scrolling section above).

### BPLCON2 ($DFF104)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Value</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">15–7</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">Unused</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">6</span><span class="bit-dir">PF2PRI</span><span class="bit-name">0/1</span><span class="bit-func">Playfield 2 priority over playfield 1 (dual playfield mode)</span></div>
<div class="bit-row"><span class="bit-num">5–3</span><span class="bit-dir">PF2P2–PF2P0</span><span class="bit-name">0–7</span><span class="bit-func">Playfield 2 vs sprite priority</span></div>
<div class="bit-row"><span class="bit-num">2–0</span><span class="bit-dir">PF1P2–PF1P0</span><span class="bit-name">0–7</span><span class="bit-func">Playfield 1 vs sprite priority</span></div>
</div>

The PFxP values determine which sprites appear in front of or behind each playfield. Combined with the sprite's own priority (set by its position in the sprite list), this creates the layered display compositing the Amiga is known for.

<!-- section: $DFF100 "Interlace Mode" -->

Interlace mode doubles the vertical resolution by displaying alternating sets of lines on each frame. Despite the doubled resolution, the programming changes required are minimal:

### Setup

1. Set the **LACE** bit (bit 2) in BPLCON0
2. Set the **modulo** value to the width of one line (in bytes) — this causes each frame to skip every other line
3. Create **two Copper lists**: one for odd lines (long frame), one for even lines (short frame)
4. Each list initializes BPLxPT to the appropriate starting line and loads the other list's address into COP1LC at the end

```asm
Copper1:                              ; Long Frame (odd lines)
    MOVE #line1_hi,BPL1PTH
    MOVE #line1_lo,BPL1PTL
    ... display instructions ...
    MOVE #Copper2_hi,COP1LCH         ; switch to Copper2 for next frame
    MOVE #Copper2_lo,COP1LCL
    WAIT ($FF,$FE)

Copper2:                              ; Short Frame (even lines)
    MOVE #line2_hi,BPL1PTH
    MOVE #line2_lo,BPL1PTL
    ... display instructions ...
    MOVE #Copper1_hi,COP1LCH         ; switch back to Copper1
    MOVE #Copper1_lo,COP1LCL
    WAIT ($FF,$FE)
```

### Initialization Sequence

1. Set COP1LC to point to Copper1
2. Clear the **LOF** bit (bit 15) in VPOS ($DFF02A) to 0, ensuring the first displayed frame is a Long Frame
3. Enable the LACE bit in BPLCON0
4. Wait for line 0 (start of next frame)
5. Enable Copper DMA

### Flicker Reduction

Interlaced displays suffer from visible flicker because each line is only refreshed 25 times per second (every other frame). Flicker is most noticeable on high-contrast horizontal edges. To minimize it, use low-contrast color pairs and avoid single-pixel-height horizontal lines.

### Enabling the Display

After configuring all registers, enable the necessary DMA channels:

```asm
    MOVE.W #$8380,$DFF096    ; SET DMAEN + BPLEN + COPEN
```

This activates the master DMA enable, bitplane DMA, and Copper DMA in a single write.
