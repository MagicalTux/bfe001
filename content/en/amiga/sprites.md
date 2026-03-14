---
title: "Sprites"
addr: "$DFF120"
description: "The Amiga sprite system — 8 hardware sprite channels with 3 colors each, attached mode for 15-color sprites, and DMA-driven positioning."
order: 8
---

<!-- section: $DFF120 "Sprite Overview" -->

**Sprites** are independent graphical objects that can be positioned anywhere on screen, regardless of the playfield display. Each sprite is 16 pixels wide and can be as tall as the visible display area. Normally, sprites appear in front of the playfield, obscuring whatever is behind them — the mouse pointer is a classic example.

The Amiga provides **8 hardware sprite channels** (SPR0 through SPR7). Each sprite can display 3 colors plus transparency, for a total of 4 possible values per pixel. By combining two sprites into an **attached pair**, up to 15 colors plus transparency can be achieved.

### How Sprite Colors Work

Each sprite line consists of two 16-bit data words, functioning like two mini-bitplanes. The color of each pixel is determined by combining the corresponding bits from both words:

- **Bit 15** of both words determines the color of the leftmost pixel
- **Bit 0** of both words determines the color of the rightmost pixel
- The two-bit combination selects one of 4 values: `00` = transparent, `01`/`10`/`11` = three colors

Sprites do not have their own color registers. Instead, they share the **upper half of the color palette** (registers 16–31). Sprites are grouped in pairs, with each pair using 3 consecutive color registers:

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Sprite Pair</span>
<span class="bit-dir">Data Bits</span>
<span class="bit-name">Color Register</span>
<span class="bit-func">Notes</span>
</div>
<div class="bit-row"><span class="bit-num">0 & 1</span><span class="bit-dir">00</span><span class="bit-name">—</span><span class="bit-func">Transparent</span></div>
<div class="bit-row"><span class="bit-num">0 & 1</span><span class="bit-dir">01</span><span class="bit-name">COLOR17</span><span class="bit-func">$DFF1A2</span></div>
<div class="bit-row"><span class="bit-num">0 & 1</span><span class="bit-dir">10</span><span class="bit-name">COLOR18</span><span class="bit-func">$DFF1A4</span></div>
<div class="bit-row"><span class="bit-num">0 & 1</span><span class="bit-dir">11</span><span class="bit-name">COLOR19</span><span class="bit-func">$DFF1A6</span></div>
<div class="bit-row"><span class="bit-num">2 & 3</span><span class="bit-dir">00</span><span class="bit-name">—</span><span class="bit-func">Transparent</span></div>
<div class="bit-row"><span class="bit-num">2 & 3</span><span class="bit-dir">01</span><span class="bit-name">COLOR21</span><span class="bit-func">$DFF1AA</span></div>
<div class="bit-row"><span class="bit-num">2 & 3</span><span class="bit-dir">10</span><span class="bit-name">COLOR22</span><span class="bit-func">$DFF1AC</span></div>
<div class="bit-row"><span class="bit-num">2 & 3</span><span class="bit-dir">11</span><span class="bit-name">COLOR23</span><span class="bit-func">$DFF1AE</span></div>
<div class="bit-row"><span class="bit-num">4 & 5</span><span class="bit-dir">00</span><span class="bit-name">—</span><span class="bit-func">Transparent</span></div>
<div class="bit-row"><span class="bit-num">4 & 5</span><span class="bit-dir">01</span><span class="bit-name">COLOR25</span><span class="bit-func">$DFF1B2</span></div>
<div class="bit-row"><span class="bit-num">4 & 5</span><span class="bit-dir">10</span><span class="bit-name">COLOR26</span><span class="bit-func">$DFF1B4</span></div>
<div class="bit-row"><span class="bit-num">4 & 5</span><span class="bit-dir">11</span><span class="bit-name">COLOR27</span><span class="bit-func">$DFF1B6</span></div>
<div class="bit-row"><span class="bit-num">6 & 7</span><span class="bit-dir">00</span><span class="bit-name">—</span><span class="bit-func">Transparent</span></div>
<div class="bit-row"><span class="bit-num">6 & 7</span><span class="bit-dir">01</span><span class="bit-name">COLOR29</span><span class="bit-func">$DFF1BA</span></div>
<div class="bit-row"><span class="bit-num">6 & 7</span><span class="bit-dir">10</span><span class="bit-name">COLOR30</span><span class="bit-func">$DFF1BC</span></div>
<div class="bit-row"><span class="bit-num">6 & 7</span><span class="bit-dir">11</span><span class="bit-name">COLOR31</span><span class="bit-func">$DFF1BE</span></div>
</div>

Since sprites share palette entries 16–31 with the playfield, color conflicts arise when the playfield uses more than 16 colors. The `00` combination always means transparent — whatever is behind the sprite shows through.

<!-- section: $DFF120 "Sprite DMA and Data Structure" -->

Programming sprites on the Amiga is straightforward because the DMA controller handles most of the work. To display a sprite, you prepare a **sprite data list** in Chip RAM and point the corresponding DMA channel to it.

Each of the 8 sprites has its own DMA channel. However, each channel can only read **two words per raster line** — this is the fundamental reason sprites are limited to 16 pixels wide and 4 colors. Because two data words are read every line, the sprite height is limited only by the display window.

### Sprite Data List Format

A sprite data list is a sequence of word pairs. Each pair contains either two **control words** (defining position) or two **data words** (defining pixels for one line).

<div class="addr-map">
<div class="addr-row">
<span class="addr-label">Start</span>
<span class="addr-body"><span class="addr-name">1st and 2nd control words</span><span class="addr-size">Position and height</span></span>
</div>
<div class="addr-row">
<span class="addr-label">Start+4</span>
<span class="addr-body"><span class="addr-name">1st and 2nd data words — line 1</span><span class="addr-size">Pixel data</span></span>
</div>
<div class="addr-row">
<span class="addr-label">Start+8</span>
<span class="addr-body"><span class="addr-name">1st and 2nd data words — line 2</span><span class="addr-size">Pixel data</span></span>
</div>
<div class="addr-row">
<span class="addr-label">...</span>
<span class="addr-body"><span class="addr-name">1st and 2nd data words — line N</span><span class="addr-size">Pixel data</span></span>
</div>
<div class="addr-row addr-highlight">
<span class="addr-label">Start+4*(N+1)</span>
<span class="addr-body"><span class="addr-name">$0000, $0000 — End of sprite data</span><span class="addr-size">Terminator</span></span>
</div>
</div>

The DMA controller reads the control words first, places them in the SPRxPOS and SPRxCTL registers, and waits until the beam reaches the starting line (VSTART). Then, at each subsequent line, it reads two data words and DENISE displays 16 pixels at the correct horizontal position. This continues until the ending line (VSTOP) is reached. The next two words are interpreted as new control words — if both are zero, the DMA channel goes idle until the next frame.

### Control Word Layout

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Word</span>
<span class="bit-dir">Bits</span>
<span class="bit-name">Name</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">1st (SPRxPOS)</span><span class="bit-dir">15–8</span><span class="bit-name">E7–E0</span><span class="bit-func">VSTART — vertical start position (bits 7–0)</span></div>
<div class="bit-row"><span class="bit-num">1st (SPRxPOS)</span><span class="bit-dir">7–0</span><span class="bit-name">H8–H1</span><span class="bit-func">HSTART — horizontal start position (bits 8–1)</span></div>
<div class="bit-row"><span class="bit-num">2nd (SPRxCTL)</span><span class="bit-dir">15–8</span><span class="bit-name">L7–L0</span><span class="bit-func">VSTOP — vertical stop position (bits 7–0)</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">2nd (SPRxCTL)</span><span class="bit-dir">7</span><span class="bit-name">AT</span><span class="bit-func">Attach control bit (for 15-color mode)</span></div>
<div class="bit-row"><span class="bit-num">2nd (SPRxCTL)</span><span class="bit-dir">6–4</span><span class="bit-name">—</span><span class="bit-func">Unused (0)</span></div>
<div class="bit-row"><span class="bit-num">2nd (SPRxCTL)</span><span class="bit-dir">3</span><span class="bit-name">E8</span><span class="bit-func">VSTART bit 8</span></div>
<div class="bit-row"><span class="bit-num">2nd (SPRxCTL)</span><span class="bit-dir">2</span><span class="bit-name">L8</span><span class="bit-func">VSTOP bit 8</span></div>
<div class="bit-row"><span class="bit-num">2nd (SPRxCTL)</span><span class="bit-dir">1</span><span class="bit-name">H0</span><span class="bit-func">HSTART bit 0</span></div>
</div>

There are 9 bits each for horizontal and vertical positioning. Vertical resolution is one raster line, and horizontal resolution is one low-resolution pixel. These are independent of the playfield resolution mode.

The start position defines the **upper-left corner** of the sprite. VSTOP is the first line *after* the sprite (last line + 1). The sprite height is therefore `VSTOP - VSTART` lines.

Sprite positioning is constrained by the display window defined by DIWSTRT and DIWSTOP. If the control word coordinates fall outside the window, the sprite will be partially or fully clipped.

### Example: 3-Color Sprite

A simple 8-line target pattern, using color values 0–3:

```
0000000222000000
0000220000220000
0002200330022000
0022003113002200
0022003113002200
0002200330022000
0000220000220000
0000000222000000
```

In the sprite data list, the two data words are separated (data word 1 = lower bitplane, data word 2 = upper bitplane):

```asm
Start:
dc.w $A05A,$A800   ; HSTART=$B4, VSTART=$A0, VSTOP=$A8
dc.w %0000000000000000,%0000001111000000  ; line 1
dc.w %0000000000000000,%0000110000110000  ; line 2
dc.w %0000000110000000,%0001100110011000  ; line 3
dc.w %0000001111000000,%0011001001001100  ; line 4
dc.w %0000001111000000,%0011001001001100  ; line 5
dc.w %0000000110000000,%0001100110011000  ; line 6
dc.w %0000000000000000,%0000110000110000  ; line 7
dc.w %0000000000000000,%0000001111000000  ; line 8
dc.w 0,0             ; end of sprite data
```

<!-- section: $DFF120 "Attached Sprites (15-Color Mode)" -->

When 3 colors are not enough, two sprites can be **attached** to form a single 15-color sprite. The two data words from each sprite combine into a 4-bit value per pixel, pointing into 15 color registers (value `0000` remains transparent).

To enable attachment, set the **AT bit** (bit 7 of SPRxCTL) on the **odd-numbered sprite** of a pair. For example, setting AT on sprite 1 causes its data bits to be combined with sprite 0's data bits.

The bit ordering for the 4-bit color value is:

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit Position</span>
<span class="bit-dir">Weight</span>
<span class="bit-name">Source</span>
<span class="bit-func">Description</span>
</div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">MSB</span><span class="bit-name">Odd sprite, data word 2</span><span class="bit-func">Highest color bit</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir"></span><span class="bit-name">Odd sprite, data word 1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir"></span><span class="bit-name">Even sprite, data word 2</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">LSB</span><span class="bit-name">Even sprite, data word 1</span><span class="bit-func">Lowest color bit</span></div>
</div>

All four possible sprite pairs (0&1, 2&3, 4&5, 6&7) can form attached sprites, and in this mode they use COLOR16 through COLOR31. The two sprites must have **identical position control words** for attachment to work properly. If positions do not match, the system falls back to normal 3-color mode.

### Multiple Sprites on One DMA Channel

After a sprite finishes displaying, its DMA channel becomes free. Instead of terminating with two zero words, the data list can contain **new control words** for a second sprite on the same channel.

There is one restriction: the DMA channel needs **one blank line** between sprites to read the new control words. If the first sprite's last line is at raster line 163, the control words are read at line 164, and the second sprite begins at line 165.

```asm
Start:
; First sprite at line $A0, horizontal $B4
dc.w $A05A,$A800
dc.w %0000000000000000,%0000001111000000
; ... (sprite data) ...
dc.w %0000000000000000,%0000001111000000
; Second sprite follows immediately — new control words
dc.w $B096,$B800  ; different position
dc.w %0000000000000000,%0000001111000000
; ... (sprite data) ...
dc.w %0000000000000000,%0000001111000000
dc.w 0,0          ; end of sprite data list
```

This technique allows displaying many more than 8 sprites per frame by **multiplexing** DMA channels, as long as sprites sharing a channel do not overlap vertically (plus the one-line gap).

<!-- section: $DFF120 "Sprite Registers" -->

Each sprite channel has a pointer register pair and four control/data registers. The pointer registers are used to initialize DMA; the control and data registers are written automatically by the DMA controller during display.

<div class="register-block">
<div class="reg-title">Sprite Pointer Registers (write only)</div>
<div class="reg-row"><span class="reg-field">$DFF120</span><span class="reg-val">SPR0PTH — Sprite 0 data pointer (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF122</span><span class="reg-val">SPR0PTL — Sprite 0 data pointer (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF124</span><span class="reg-val">SPR1PTH — Sprite 1 data pointer (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF126</span><span class="reg-val">SPR1PTL — Sprite 1 data pointer (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF128</span><span class="reg-val">SPR2PTH — Sprite 2 data pointer (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF12A</span><span class="reg-val">SPR2PTL — Sprite 2 data pointer (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF12C</span><span class="reg-val">SPR3PTH — Sprite 3 data pointer (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF12E</span><span class="reg-val">SPR3PTL — Sprite 3 data pointer (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF130</span><span class="reg-val">SPR4PTH — Sprite 4 data pointer (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF132</span><span class="reg-val">SPR4PTL — Sprite 4 data pointer (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF134</span><span class="reg-val">SPR5PTH — Sprite 5 data pointer (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF136</span><span class="reg-val">SPR5PTL — Sprite 5 data pointer (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF138</span><span class="reg-val">SPR6PTH — Sprite 6 data pointer (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF13A</span><span class="reg-val">SPR6PTL — Sprite 6 data pointer (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF13C</span><span class="reg-val">SPR7PTH — Sprite 7 data pointer (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF13E</span><span class="reg-val">SPR7PTL — Sprite 7 data pointer (bits 0–15)</span></div>
</div>

The DMA controller uses these as running pointers into the sprite data list. After processing a complete list, the pointer has advanced past the end. To display the same sprite again next frame, the pointer must be **reloaded** during vertical blank — typically by the Copper:

```asm
; Copper list: reload sprite pointers each frame
MOVE #StartSprite0H,SPR0PTH
MOVE #StartSprite0L,SPR0PTL
MOVE #StartSprite1H,SPR1PTH
MOVE #StartSprite1L,SPR1PTL
; ... repeat for all 8 channels ...
WAIT $FFFE    ; end of Copper list
```

<div class="register-block">
<div class="reg-title">Per-Sprite Control and Data Registers</div>
<div class="reg-row"><span class="reg-field">SPRxPOS</span><span class="reg-val">First control word — VSTART (bits 15–8), HSTART (bits 7–0)</span></div>
<div class="reg-row"><span class="reg-field">SPRxCTL</span><span class="reg-val">Second control word — VSTOP (bits 15–8), AT/E8/L8/H0 (bits 7–0)</span></div>
<div class="reg-row"><span class="reg-field">SPRxDATA</span><span class="reg-val">First data word for current line (low bitplane)</span></div>
<div class="reg-row"><span class="reg-field">SPRxDATB</span><span class="reg-val">Second data word for current line (high bitplane)</span></div>
</div>

When a value is written to SPRxCTL (by DMA or the 68000), DENISE disables that sprite's output until the beam reaches VSTART. Writing to SPRxDATA reactivates sprite output. The DMA controller uses SPRxPOS and SPRxCTL to compare against the current beam position for correct horizontal placement.

There is **no individual enable/disable** for each sprite DMA channel. The SPREN bit (bit 5) in DMACON enables all 8 sprite DMA channels simultaneously:

```asm
MOVE.W #$8220,$DFF096   ; set SPREN + DMAEN in DMACON
```

Unused sprite channels must still be initialized — point their SPRxPT to a memory location containing two zero words (the terminator of any active sprite list will do).

<!-- section: $DFF120 "Sprite-Playfield Priority" -->

When sprites and playfields overlap, a priority system determines which element appears in front. Among sprites, priority is determined by sprite number: **sprite 0 has the highest priority** and cannot be obscured by any other sprite.

For sprite-vs-playfield priority, sprites are grouped in pairs: 0&1, 2&3, 4&5, 6&7. The playfield can be inserted at any position among these four pairs, giving **5 possible priority positions** for each playfield:

<div class="addr-map">
<div class="addr-row addr-highlight">
<span class="addr-label">Pos 0</span>
<span class="addr-body"><span class="addr-name">PLF &gt; SPR0&1 &gt; SPR2&3 &gt; SPR4&5 &gt; SPR6&7</span><span class="addr-size">Playfield in front</span></span>
</div>
<div class="addr-row">
<span class="addr-label">Pos 1</span>
<span class="addr-body"><span class="addr-name">SPR0&1 &gt; PLF &gt; SPR2&3 &gt; SPR4&5 &gt; SPR6&7</span><span class="addr-size"></span></span>
</div>
<div class="addr-row">
<span class="addr-label">Pos 2</span>
<span class="addr-body"><span class="addr-name">SPR0&1 &gt; SPR2&3 &gt; PLF &gt; SPR4&5 &gt; SPR6&7</span><span class="addr-size"></span></span>
</div>
<div class="addr-row">
<span class="addr-label">Pos 3</span>
<span class="addr-body"><span class="addr-name">SPR0&1 &gt; SPR2&3 &gt; SPR4&5 &gt; PLF &gt; SPR6&7</span><span class="addr-size"></span></span>
</div>
<div class="addr-row addr-highlight">
<span class="addr-label">Pos 4</span>
<span class="addr-body"><span class="addr-name">SPR0&1 &gt; SPR2&3 &gt; SPR4&5 &gt; SPR6&7 &gt; PLF</span><span class="addr-size">Playfield behind</span></span>
</div>
</div>

This is controlled by the **BPLCON2** register ($DFF104, write only):

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func">Description</span>
</div>
<div class="bit-row"><span class="bit-num">15–7</span><span class="bit-dir">—</span><span class="bit-name">Unused</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">6</span><span class="bit-dir">PF2PRI</span><span class="bit-name">PF2 priority</span><span class="bit-func">If set, playfield 2 appears in front of playfield 1</span></div>
<div class="bit-row"><span class="bit-num">5–3</span><span class="bit-dir">PF2P2–PF2P0</span><span class="bit-name">PF2 sprite priority</span><span class="bit-func">Position of playfield 2 among sprite pairs (0–4)</span></div>
<div class="bit-row"><span class="bit-num">2–0</span><span class="bit-dir">PF1P2–PF1P0</span><span class="bit-name">PF1 sprite priority</span><span class="bit-func">Position of playfield 1 among sprite pairs (0–4)</span></div>
</div>

In Dual-Playfield mode, each playfield can be independently positioned among the sprite pairs. The PF2PRI bit controls which playfield appears in front of the other, and this inter-playfield priority takes precedence over sprite-playfield priority. In single-playfield mode, only PF1Px is relevant; PF2PRI and PF2Px have no effect.

<!-- section: $DFF120 "Collision Detection" -->

The Amiga hardware can automatically detect collisions between sprites and between sprites and playfields. A collision occurs when non-transparent pixels from two elements overlap at the same screen position.

### CLXDAT — Collision Data Register ($DFF00E, read only)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Collision Between</span>
<span class="bit-name"></span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">SPR4(5) and SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">SPR2(3) and SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">SPR2(3) and SPR4(5)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11</span><span class="bit-dir">SPR0(1) and SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">SPR0(1) and SPR4(5)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">SPR0(1) and SPR2(3)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">Playfield 2 and SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">Playfield 2 and SPR4(5)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">Playfield 2 and SPR2(3)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">Playfield 2 and SPR0(1)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">Playfield 1 and SPR6(7)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">Playfield 1 and SPR4(5)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">Playfield 1 and SPR2(3)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">Playfield 1 and SPR0(1)</span><span class="bit-name"></span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">Playfield 1 and Playfield 2</span><span class="bit-name"></span><span class="bit-func"></span></div>
</div>

### CLXCON — Collision Control Register ($DFF098, write only)

The CLXCON register controls which elements participate in collision detection. The ENSPx bits enable collision testing for odd-numbered sprites (even-numbered sprites always participate). The ENBPx/MVBPx bits select which bitplane color combinations can trigger playfield collisions — allowing fine-grained control such as "only red pixels trigger collisions."

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">15</span><span class="bit-dir">ENSP7</span><span class="bit-name">Enable collision for sprite 7</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">ENSP5</span><span class="bit-name">Enable collision for sprite 5</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">ENSP3</span><span class="bit-name">Enable collision for sprite 3</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">ENSP1</span><span class="bit-name">Enable collision for sprite 1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11–6</span><span class="bit-dir">ENBP6–ENBP1</span><span class="bit-name">Enable bitplane comparison</span><span class="bit-func">Select which bitplanes participate</span></div>
<div class="bit-row"><span class="bit-num">5–0</span><span class="bit-dir">MVBP6–MVBP1</span><span class="bit-name">Match value for bitplanes</span><span class="bit-func">Required bit value for collision</span></div>
</div>

When an ENBPx bit is set, the corresponding bitplane's pixel value must match the MVBPx bit for a collision to register. If ENBPx is clear, that bitplane is ignored in the comparison (any value matches). Setting all ENBPx to 0 means every playfield pixel can trigger a collision. For attached sprite pairs, the corresponding ENSPx bit must be set for correct collision detection.
