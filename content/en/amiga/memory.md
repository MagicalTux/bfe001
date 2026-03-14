---
title: "Memory Organization"
addr: "$000000"
description: "The Amiga memory map — Chip RAM, Fast RAM, CIA and custom chip address spaces, ROM, and the A1000 WOM."
order: 4
---

<!-- section: $000000 "Memory Map" -->

The Amiga's 68000 has a 24-bit address bus, giving a 16 MB address space ($000000–$FFFFFF). This space is divided between RAM, custom chip registers, CIA registers, expansion space, and ROM. Gary (the address decoder chip) determines which device responds to each bus cycle.

<div class="addr-map">
    <div class="addr-row"><div class="addr-label">$000000</div><div class="addr-body"><div class="addr-name">Chip RAM</div><div class="addr-size">512 KB ($000000–$07FFFF) — expandable to 1 MB with Fat Agnus</div></div></div>
    <div class="addr-row"><div class="addr-label">$080000</div><div class="addr-body"><div class="addr-name">Chip RAM (extended)</div><div class="addr-size">512 KB ($080000–$0FFFFF) — with 1 MB Agnus only</div></div></div>
    <div class="addr-row"><div class="addr-label">$100000</div><div class="addr-body"><div class="addr-name">Reserved</div><div class="addr-size">$100000–$1FFFFF — unused on stock A500</div></div></div>
    <div class="addr-row"><div class="addr-label">$200000</div><div class="addr-body"><div class="addr-name">Fast RAM (expansion)</div><div class="addr-size">$200000–$9FFFFF — up to 8 MB via trapdoor or Zorro</div></div></div>
    <div class="addr-row"><div class="addr-label">$A00000</div><div class="addr-body"><div class="addr-name">Reserved / Expansion</div><div class="addr-size">$A00000–$BEFFFF — I/O expansion space</div></div></div>
    <div class="addr-row addr-highlight"><div class="addr-label">$BFD000</div><div class="addr-body"><div class="addr-name">CIA-B (8520)</div><div class="addr-size">Even addresses: $BFD000, $BFD100, $BFD200, …</div></div></div>
    <div class="addr-row addr-highlight"><div class="addr-label">$BFE001</div><div class="addr-body"><div class="addr-name">CIA-A (8520)</div><div class="addr-size">Odd addresses: $BFE001, $BFE101, $BFE201, …</div></div></div>
    <div class="addr-row"><div class="addr-label">$C00000</div><div class="addr-body"><div class="addr-name">Slow RAM (A500 trapdoor)</div><div class="addr-size">$C00000–$D7FFFF — 512 KB "Slow" expansion RAM</div></div></div>
    <div class="addr-row"><div class="addr-label">$D80000</div><div class="addr-body"><div class="addr-name">Reserved</div><div class="addr-size">$D80000–$DFFFFF</div></div></div>
    <div class="addr-row"><div class="addr-label">$DFF000</div><div class="addr-body"><div class="addr-name">Custom Chip Registers</div><div class="addr-size">$DFF000–$DFF1FF — ~200 registers (Agnus, Denise, Paula)</div></div></div>
    <div class="addr-row"><div class="addr-label">$E00000</div><div class="addr-body"><div class="addr-name">Reserved / Expansion ROM</div><div class="addr-size">$E00000–$E7FFFF</div></div></div>
    <div class="addr-row"><div class="addr-label">$E80000</div><div class="addr-body"><div class="addr-name">Autoconfig Space</div><div class="addr-size">$E80000–$EFFFFF — Zorro II autoconfig registers</div></div></div>
    <div class="addr-row"><div class="addr-label">$F00000</div><div class="addr-body"><div class="addr-name">Reserved</div><div class="addr-size">$F00000–$F7FFFF</div></div></div>
    <div class="addr-row"><div class="addr-label">$F80000</div><div class="addr-body"><div class="addr-name">Reserved / Diagnostic ROM</div><div class="addr-size">$F80000–$FBFFFF</div></div></div>
    <div class="addr-row"><div class="addr-label">$FC0000</div><div class="addr-body"><div class="addr-name">Kickstart ROM</div><div class="addr-size">$FC0000–$FFFFFF — 256 KB (Kickstart 1.2/1.3)</div></div></div>
</div>

<!-- section: $000000 "Chip RAM vs. Fast RAM" -->

The most important distinction in the Amiga memory map is between **Chip RAM** and **Fast RAM**.

**Chip RAM** ($000000–$07FFFF, or $0FFFFF with Fat Agnus) is accessible by both the 68000 and the custom chips. All data that the custom chips need — bitplane graphics, sprites, audio samples, Copper lists, Blitter sources — must reside in Chip RAM. This is because Agnus generates the DMA addresses and can only address the lower portion of memory.

**Fast RAM** ($200000 and up) is accessible only by the 68000. The custom chips cannot see it. The advantage is that the 68000 gets full-speed access without contention from DMA — hence "fast." Programs and data structures that don't need DMA access should be placed in Fast RAM when available.

**Slow RAM** ($C00000–$D7FFFF) is the A500's trapdoor expansion RAM. Despite being in the "fast" address range, it sits on the Chip bus and is subject to DMA contention — so it's slower than true Fast RAM but inaccessible to the custom chips. It's the worst of both worlds, but better than no expansion at all.

<!-- section: $000000 "CIA Address Space" -->

The two CIA 8520 chips occupy a peculiar position in the memory map. CIA-A is addressed at **odd** byte addresses starting at **$BFE001**, and CIA-B at **even** addresses starting at **$BFD000**. This unusual arrangement is a side effect of how Gary decodes addresses and connects the CIAs to different halves of the data bus.

Each CIA has 16 registers, spaced 256 bytes apart (only A0–A3 and A8–A11 are decoded):

<div class="register-block">
<div class="reg-title">CIA-A Register Addresses</div>
<div class="reg-row"><span class="reg-field">$BFE001</span><span class="reg-val">PRA — Port A data (accent on accent on fire buttons, overlay, LED)</span></div>
<div class="reg-row"><span class="reg-field">$BFE101</span><span class="reg-val">PRB — Port B data (parallel port accent on accent data)</span></div>
<div class="reg-row"><span class="reg-field">$BFE201</span><span class="reg-val">DDRA — Data direction register A</span></div>
<div class="reg-row"><span class="reg-field">$BFE301</span><span class="reg-val">DDRB — Data direction register B</span></div>
<div class="reg-row"><span class="reg-field">$BFE401</span><span class="reg-val">TALO — Timer A low byte</span></div>
<div class="reg-row"><span class="reg-field">$BFE501</span><span class="reg-val">TAHI — Timer A high byte</span></div>
<div class="reg-row"><span class="reg-field">$BFE601</span><span class="reg-val">TBLO — Timer B low byte</span></div>
<div class="reg-row"><span class="reg-field">$BFE701</span><span class="reg-val">TBHI — Timer B high byte</span></div>
<div class="reg-row"><span class="reg-field">$BFE801</span><span class="reg-val">TODLO — TOD counter low (1/10 seconds)</span></div>
<div class="reg-row"><span class="reg-field">$BFE901</span><span class="reg-val">TODMID — TOD counter mid (seconds)</span></div>
<div class="reg-row"><span class="reg-field">$BFEA01</span><span class="reg-val">TODHI — TOD counter high (minutes)</span></div>
<div class="reg-row"><span class="reg-field">$BFEC01</span><span class="reg-val">SDR — Serial data register</span></div>
<div class="reg-row"><span class="reg-field">$BFED01</span><span class="reg-val">ICR — Interrupt control register</span></div>
<div class="reg-row"><span class="reg-field">$BFEE01</span><span class="reg-val">CRA — Control register A</span></div>
<div class="reg-row"><span class="reg-field">$BFEF01</span><span class="reg-val">CRB — Control register B</span></div>
</div>

Because CIAs use the 6800-compatible synchronous bus protocol (via VPA̅/VMA̅/E clock), each CIA access takes approximately 1.4 µs — about 5× slower than a custom chip register access.

<!-- section: $000000 "Custom Chip Register Space" -->

The custom chip registers occupy **$DFF000–$DFF1FF** — 512 bytes containing approximately 200 individual registers. Many are write-only (the 68000 can write values but not read them back), while read registers have different functions at the same address.

The registers are organized by subsystem within this space:

<div class="register-block">
<div class="reg-title">Register Space Layout</div>
<div class="reg-row"><span class="reg-field">$000–$03E</span><span class="reg-val">System: BLTDDAT, DMACONR, VPOSR, VHPOSR, DSKDAT, JOYxDAT, CLXDAT, POTINP, SERDATR, DSKBYTR, INTENAR, INTREQR</span></div>
<div class="reg-row"><span class="reg-field">$040–$07E</span><span class="reg-val">Blitter: BLTCONx, BLTxPT, BLTxMOD, BLTxDAT, BLTSIZE, BLTxFWM/LWM</span></div>
<div class="reg-row"><span class="reg-field">$080–$08E</span><span class="reg-val">Copper: COPxLC, COPJMPx, COPCON, DIWSTRT, DIWSTOP</span></div>
<div class="reg-row"><span class="reg-field">$090–$09E</span><span class="reg-val">Control: DDFSTRx, DMACON, CLXCON, INTENA, INTREQ</span></div>
<div class="reg-row"><span class="reg-field">$0A0–$0DF</span><span class="reg-val">Audio: AUDxLCH/L, AUDxLEN, AUDxPER, AUDxVOL, AUDxDAT (×4 channels)</span></div>
<div class="reg-row"><span class="reg-field">$0E0–$0FE</span><span class="reg-val">Bitplane pointers: BPLxPTH/L (×6 planes)</span></div>
<div class="reg-row"><span class="reg-field">$100–$10E</span><span class="reg-val">Bitplane control: BPLCONx, BPLxMOD</span></div>
<div class="reg-row"><span class="reg-field">$110–$11E</span><span class="reg-val">Bitplane data: BPLxDAT</span></div>
<div class="reg-row"><span class="reg-field">$120–$17E</span><span class="reg-val">Sprite: SPRxPT, SPRxPOS, SPRxCTL, SPRxDATA, SPRxDATB (×8)</span></div>
<div class="reg-row"><span class="reg-field">$180–$1BE</span><span class="reg-val">Color: COLOR00–COLOR31</span></div>
</div>

The Copper is restricted from accessing registers below $040 (to prevent it from reprogramming the DMA controller or itself). With COPCON bit 0 set, it can additionally access $040–$07E (Blitter registers).

<!-- section: $000000 "ROM and Overlay" -->

The Amiga's operating system, **Kickstart**, resides in ROM at **$FC0000–$FFFFFF** (256 KB on the A500). At power-on, the 68000 reads its reset vectors from address $000000. Since RAM contains garbage at this point, Gary activates the **ROM overlay**, temporarily mirroring the Kickstart ROM at $000000. The overlay is disabled when the first write to CIA-A bit 0 (OVL) occurs — at that point, $000000 reverts to Chip RAM and the ROM is only visible at $FC0000.

### A1000 WOM (Write-Once Memory)

The A1000 had no ROM — instead it had a **WOM** (Write-Once Memory), a 256 KB area of write-once RAM at $FC0000. At power-on, a small bootstrap ROM (8 KB) at $F80000 loaded Kickstart from floppy disk into the WOM. Once written, the WOM could not be modified until the next reset. This allowed the A1000 to use different Kickstart versions simply by booting from a different disk — a flexibility that was abandoned in later models in favor of the simpler and faster mask ROM approach.
