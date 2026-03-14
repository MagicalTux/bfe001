---
title: "Copper Coprocessor"
addr: "$DFF080"
description: "The Amiga Copper coprocessor — a simple but powerful display-synchronized processor for register manipulation."
order: 6
---

<!-- section: $DFF080 "Copper Overview" -->

The **Copper** (short for *coprocessor*) is a simple but remarkably powerful processor built into Agnus. Its sole job is to watch the video beam position and write values into custom chip registers at precise moments during the display. By changing color registers, bitplane pointers, or sprite data mid-frame, the Copper enables effects that would be impossible with the CPU alone — split screens, rainbow gradients, per-line palette swaps, and multiplexed sprites.

The Copper has its own program counter and executes **Copper lists** stored in Chip RAM. Despite recognizing only three instructions, it can orchestrate the entire display.

### Three Instructions

**MOVE** — writes an immediate 16-bit value to any custom chip register (with some restrictions).

**WAIT** — halts execution until the video beam reaches a specified screen position.

**SKIP** — tests whether the beam has passed a given position; if true, skips the next instruction. This enables conditional branching within Copper lists.

Each instruction is exactly two words (4 bytes). A Copper list is simply a sequence of these instructions, terminated by a WAIT for an impossible beam position.

```asm
WAIT (x1,y1)  ; wait for beam position x1,y1
MOVE #0,$180   ; write 0 to background color register
MOVE #1,$181   ; write 1 to color register 1
WAIT (x2,y2)  ; wait for beam position x2,y2
```

<!-- section: $DFF080 "Copper Registers" -->

The Copper uses several registers at the base of the custom chip address space. All are **write-only**.

<div class="register-block">
<div class="reg-title">Copper Registers</div>
<div class="reg-row"><span class="reg-field">$DFF080</span><span class="reg-val">COP1LCH — Copper list 1 pointer (high word)</span></div>
<div class="reg-row"><span class="reg-field">$DFF082</span><span class="reg-val">COP1LCL — Copper list 1 pointer (low word)</span></div>
<div class="reg-row"><span class="reg-field">$DFF084</span><span class="reg-val">COP2LCH — Copper list 2 pointer (high word)</span></div>
<div class="reg-row"><span class="reg-field">$DFF086</span><span class="reg-val">COP2LCL — Copper list 2 pointer (low word)</span></div>
<div class="reg-row"><span class="reg-field">$DFF088</span><span class="reg-val">COPJMP1 — <span class="highlight">Strobe: load COP1LC into program counter</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF08A</span><span class="reg-val">COPJMP2 — <span class="highlight">Strobe: load COP2LC into program counter</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF02E</span><span class="reg-val">COPCON — Copper control (bit 0: allow access to blitter registers $040–$07E)</span></div>
</div>

The two `COPxLC` register pairs hold 18-bit addresses of Copper lists in Chip RAM. Since these are longword addresses, they are loaded with a `MOVE.L` instruction. The `COPJMPx` registers are **strobe** registers — any write triggers the action (the written value is irrelevant).

At the start of each vertical blank (line 0), the hardware automatically loads `COP1LC` into the program counter, so the Copper replays the same list every frame.

<!-- section: $DFF080 "Instruction Encoding" -->

All three instructions share the same 2-word (32-bit) format. The low bit of the first word distinguishes MOVE from WAIT/SKIP, and the low bit of the second word distinguishes WAIT from SKIP.

<div class="bit-table">
<div class="bit-header">
<span class="bit-num"></span>
<span class="bit-dir">Bit</span>
<span class="bit-name">MOVE</span>
<span class="bit-func">WAIT / SKIP</span>
</div>
<div class="bit-row"><span class="bit-num">BW1</span><span class="bit-dir">15–9</span><span class="bit-name">x</span><span class="bit-func">VP7–VP1 (vertical beam position)</span></div>
<div class="bit-row"><span class="bit-num">BW1</span><span class="bit-dir">8–1</span><span class="bit-name">RA8–RA1</span><span class="bit-func">VP0, HP8–HP2 (horiz beam position)</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">BW1</span><span class="bit-dir">0</span><span class="bit-name">0</span><span class="bit-func">1 (distinguishes WAIT/SKIP from MOVE)</span></div>
<div class="bit-row"><span class="bit-num">BW2</span><span class="bit-dir">15</span><span class="bit-name">DW15</span><span class="bit-func">BFD (Blitter Finish Disable)</span></div>
<div class="bit-row"><span class="bit-num">BW2</span><span class="bit-dir">14–1</span><span class="bit-name">DW14–DW1</span><span class="bit-func">VM6–VM0, HM8–HM2 (mask bits)</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">BW2</span><span class="bit-dir">0</span><span class="bit-name">DW0</span><span class="bit-func">WAIT=0 / SKIP=1</span></div>
</div>

**Legend:** RA = register address, DW = data word, VP = vertical position, HP = horizontal position, VM = vertical mask, HM = horizontal mask, BFD = Blitter Finish Disable.

<!-- section: $DFF080 "MOVE Instruction" -->

The MOVE instruction is identified by **bit 0 of word 1 = 0**. The register address occupies bits 8–1 (9 bits, but bit 0 is always 0, giving word-aligned addresses). Word 2 contains the 16-bit value to write.

**Register restrictions:** Normally the Copper cannot access registers below `$040`. If bit 0 of `COPCON` ($DFF02E) is set, access to the range `$040`–`$07E` (blitter registers) is also permitted. Registers `$000`–`$03E` are always off-limits — this prevents the Copper from reprogramming itself or the DMA controller.

<!-- section: $DFF080 "WAIT Instruction" -->

The WAIT instruction is identified by **bit 0 of word 1 = 1** and **bit 0 of word 2 = 0**. It halts the Copper until the video beam reaches or passes the specified position. If the beam has already passed that position, execution continues immediately.

**Vertical resolution:** 8 bits (VP0–VP7) allow addressing lines 0–255. Since PAL has 313 lines, reaching lines 256+ requires two consecutive WAITs — first to line 255, then to the target line (the hardware ignores bit 8 of the vertical counter).

**Horizontal resolution:** 7 bits (HP2–HP8) give 128 positions per line. Since HP0 and HP1 are not available, horizontal positions are quantized to every 4 low-resolution pixels.

**Mask bits:** The mask (VM, HM) controls which position bits participate in the comparison. Only bits set in both the mask and the position are checked. This allows patterns like "trigger every 16 lines" by masking out upper vertical bits.

**BFD (Blitter Finish Disable):** When BFD=0, the Copper waits at every WAIT until the Blitter finishes its current operation before checking the beam position. Set BFD=1 to ignore Blitter status.

<!-- section: $DFF080 "SKIP Instruction" -->

SKIP has the same encoding as WAIT, except **bit 0 of word 2 = 1**. It tests whether the beam position is greater than or equal to the specified position. If true, the next instruction is skipped. This enables conditional branching — for example, the skipped instruction can be a `COPJMP` to jump to a different Copper list.

The mask and BFD bits work identically to WAIT.

<!-- section: $DFF080 "Copper List Structure" -->

A Copper list is a linear sequence of MOVE, WAIT, and SKIP instructions in Chip RAM. Its start address is stored in `COP1LC` (or `COP2LC` for a secondary list).

### Terminating a List

The list must end with a WAIT for an impossible beam position. The standard terminator is:

```asm
DC.W $FFFF,$FFFE    ; WAIT for position ($FF,$FE) — never reached
```

A horizontal position beyond `$E4` is unreachable, so this instruction keeps the Copper idle until the next vertical blank reloads `COP1LC`.

### Two Copper Lists

The Copper supports two list pointers. This is commonly used for double-buffering display changes or for splitting the screen — `COP1LC` handles the top portion, then a MOVE to `COPJMP2` switches to the list at `COP2LC` for the bottom.

<!-- section: $DFF080 "Copper Interrupts" -->

The Copper can trigger an interrupt by writing to the `INTREQ` register:

```asm
MOVE #$8010,INTREQ    ; SET/CLR + COPPER interrupt bit
```

Bit 4 of `INTREQ` is dedicated to the Copper. This generates a level 3 interrupt that the 68000 can service. The primary use is **raster interrupts** — notifying the CPU when a specific screen position is reached, enabling time-critical per-frame updates that are synchronized to the display.

<!-- section: $DFF080 "Copper DMA" -->

The Copper fetches its instructions via a DMA channel. It uses **even bus cycles** and has priority over both the Blitter and the 68000. Each instruction requires two DMA cycles (one per word). WAIT instructions need one additional cycle to check the beam position; during the wait phase the Copper releases the bus.

The `COPEN` bit (bit 7) in `DMACON` ($DFF096) enables Copper DMA. When cleared, the Copper stops executing and releases the bus. When set, execution resumes from the current program counter — so the `COPxLC` registers must be properly initialized first.

### Initialization Sequence

```asm
    LEA $DFF000,A5                    ; custom chip base
    MOVE.W #$0080,DMACON(A5)          ; disable Copper DMA
    MOVE.L #COPPERLIST,COP1LCH(A5)   ; set Copper list address
    CLR.W COPJMP1(A5)                ; load address into program counter
    MOVE.W #$8080,DMACON(A5)          ; enable Copper DMA
```

<!-- section: $DFF080 "Example: Color Bars" -->

This example program installs a Copper list that produces colored horizontal bands by changing the background color at specific beam positions.

```asm
;*** Copper List Color Bars Example ***

; Custom chip register offsets
INTENA  = $9A    ; interrupt enable (write)
DMACON  = $96    ; DMA control (write)
COLOR00 = $180   ; background color register
COP1LC  = $80    ; Copper list 1 pointer
COPJMP1 = $88    ; strobe: jump to list 1
CIAAPRA = $BFE001  ; CIA-A port A (left mouse button)

Start:
    move.l 4,a6                       ; ExecBase
    moveq  #CLsize,d0
    moveq  #2,d1                      ; MEMF_CHIP
    jsr    -198(a6)                    ; AllocMem
    move.l d0,CLadr
    beq.s  .done

    ; Copy Copper list to Chip RAM
    lea    CLstart,a0
    move.l CLadr,a1
    moveq  #CLsize-1,d0
.copy:
    move.b (a0)+,(a1)+
    dbf    d0,.copy

    ; Install and start Copper
    jsr    -132(a6)                    ; Forbid
    lea    $DFF000,a5
    move.w #$03A0,DMACON(a5)          ; disable DMA
    move.l CLadr,COP1LC(a5)
    clr.w  COPJMP1(a5)
    move.w #$8280,DMACON(a5)          ; enable Copper + bitplane DMA

    ; Wait for left mouse button
.wait:
    btst   #6,CIAAPRA
    bne.s  .wait

    ; Restore system Copper list
    move.l #0,a1
    clr.l  d0
    jsr    -552(a6)                    ; OpenLibrary "graphics.library"
    move.l d0,a4
    move.l 38(a4),COP1LC(a5)          ; restore StartList
    clr.w  COPJMP1(a5)
    move.w #$83E0,DMACON(a5)
    jsr    -138(a6)                    ; Permit
    move.l CLadr,a1
    moveq  #CLsize,d0
    jsr    -210(a6)                    ; FreeMem

.done:
    clr.l  d0
    rts

CLadr:  DC.L 0

; Copper list data
CLstart:
    DC.W COLOR00,$000F                ; blue
    DC.W $780F,$FFFE                  ; WAIT line $78
    DC.W COLOR00,$00F0                ; green
    DC.W $D70F,$FFFE                  ; WAIT line $D7
    DC.W COLOR00,$0F00                ; red
    DC.W $FFFF,$FFFE                  ; end of list
CLend:
CLsize = CLend-CLstart
```

The WAIT instructions use `$0F` as the horizontal position (start of horizontal blank), so color changes happen outside the visible area for a clean transition. If you change the horizontal position to `$00`, the color switch becomes visible at the right edge of the screen.
