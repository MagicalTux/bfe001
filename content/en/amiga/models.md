---
title: "Amiga Models"
addr: "$A01000"
description: "The complete Amiga hardware lineup — from the A1000 to the A4000, covering chipset generations, CPUs, and what changed between models."
order: 0
---

<!-- section: $A01000 "The Amiga Family" -->

Between 1985 and 1994, Commodore produced over a dozen Amiga models spanning home computers, professional workstations, a multimedia player, and a game console. Despite the variety, they all share the same fundamental architecture: a Motorola 680x0 CPU paired with a custom chipset handling graphics, sound, and DMA.

The hardware evolved through three chipset generations:

**OCS** (Original Chip Set, 1985) — Agnus, Denise, Paula. The foundation: 4096-color palette, 32 on screen (or 4096 via HAM), 4-channel 8-bit audio, hardware sprites, Copper, Blitter.

**ECS** (Enhanced Chip Set, 1990) — Fat Agnus / Super Agnus, Super Denise, Paula. Incremental: 1 MB Chip RAM addressing, productivity display modes (e.g. 1280×200), improved sprites.

**AGA** (Advanced Graphics Architecture, 1992) — Alice, Lisa, Paula. Major upgrade: 24-bit palette (16.7M colors), up to 256 colors on screen (or 262,144 via HAM8), 2 MB Chip RAM, wider data paths.

<!-- section: $A01000 "A1000 (1985)" -->

The original. Announced at Lincoln Center in July 1985 with a Debbie Harry demo, the A1000 was Jay Miner's vision realized.

<div class="register-block">
<div class="reg-title">Amiga 1000</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">July 1985 (US) — $1,295</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.16 MHz (NTSC)</span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">OCS — Agnus (8361), Denise (8362), Paula (8364)</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">256 KB (expandable to 512 KB)</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val"><span class="highlight">WOM (Write-Once Memory) — loaded from floppy at boot</span></span></div>
<div class="reg-row"><span class="reg-field">Expansion</span><span class="reg-val">86-pin sidecar bus</span></div>
<div class="reg-row"><span class="reg-field">Storage</span><span class="reg-val">3.5" DD floppy (880 KB)</span></div>
<div class="reg-row"><span class="reg-field">Form</span><span class="reg-val">Desktop with separate keyboard</span></div>
</div>

The A1000 was unique in having no ROM — instead, a small 8 KB bootstrap ROM at $F80000 loaded Kickstart from floppy into 256 KB of WOM (Write-Once Memory). Once written, the WOM was read-only until reset. This meant different Kickstart versions could be loaded, but it also meant a longer boot time.

The original Agnus could only address 512 KB of Chip RAM. The A1000 shipped with 256 KB and could be expanded internally to 512 KB. A "daughterboard" connector on the front panel (originally signed by the Amiga engineering team) provided the internal expansion path.

The A1000 was discontinued in 1987 when the A500 and A2000 took over.

<!-- section: $A01000 "A500 (1987)" -->

The most successful Amiga ever made. The A500 put the Amiga into millions of homes, particularly in Europe where it dominated the home computer market alongside the Atari ST.

<div class="register-block">
<div class="reg-title">Amiga 500</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">January 1987 — $699 (US), £499 (UK)</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz (PAL) / 7.16 MHz (NTSC)</span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">OCS — Fat Agnus (8375), Denise (8362), Paula (8364)</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val"><span class="highlight">512 KB (1 MB with Fat Agnus upgrade)</span></span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">1.2 or 1.3 in ROM (256 KB)</span></div>
<div class="reg-row"><span class="reg-field">Expansion</span><span class="reg-val">86-pin side expansion + trapdoor slot (512 KB slow RAM)</span></div>
<div class="reg-row"><span class="reg-field">Storage</span><span class="reg-val">3.5" DD floppy (880 KB)</span></div>
<div class="reg-row"><span class="reg-field">Form</span><span class="reg-val">All-in-one with integrated keyboard</span></div>
</div>

The A500 introduced the **Fat Agnus** (8375), which could address up to 1 MB of Chip RAM (though only 512 KB was fitted as standard). It also moved Kickstart into mask ROM, eliminating the A1000's floppy boot requirement.

The trapdoor expansion slot on the underside accepted a 512 KB RAM card, but this "slow RAM" sat on the Chip bus at $C00000 — accessible only by the CPU but subject to DMA contention, making it slower than true Fast RAM and inaccessible to the custom chips.

The A500 was the platform that defined the Amiga's golden age of gaming and demoscene creativity. Approximately 6 million units were sold.

<!-- section: $A01000 "A2000 (1987)" -->

The professional counterpart to the A500. Same core hardware in a big-box desktop case with internal expansion.

<div class="register-block">
<div class="reg-title">Amiga 2000</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">March 1987 — $1,495</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz</span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">OCS (later revisions: ECS)</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">512 KB (expandable to 1 MB or 2 MB with ECS)</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">1.2 or 1.3 (later 2.04)</span></div>
<div class="reg-row"><span class="reg-field">Expansion</span><span class="reg-val"><span class="highlight">5× Zorro II slots, 2× ISA (PC bridgeboard), 1× CPU slot, 1× video slot</span></span></div>
<div class="reg-row"><span class="reg-field">Storage</span><span class="reg-val">3.5" DD floppy + internal hard drive bay</span></div>
<div class="reg-row"><span class="reg-field">Form</span><span class="reg-val">Desktop with separate keyboard</span></div>
</div>

The A2000's defining feature was its **expansion architecture**. The Zorro II slots allowed true autoconfig peripherals — hard drive controllers, RAM boards, Ethernet cards, video capture devices. The dedicated CPU slot accepted accelerator boards with 68020, 68030, or even 68040 processors, transforming the machine entirely.

The **video slot** enabled genlocks and video production hardware, making the A2000 the standard platform for broadcast video work — famously used for weather graphics, TV titling, and the Video Toaster.

A variant, the **A2500**, shipped with a 68020 or 68030 accelerator pre-installed.

<!-- section: $A01000 "A3000 (1990)" -->

Commodore's most technically ambitious Amiga. A full 32-bit design that pushed the architecture forward.

<div class="register-block">
<div class="reg-title">Amiga 3000</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">June 1990 — $3,295 (25 MHz model)</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val"><span class="highlight">Motorola 68030 @ 16 or 25 MHz + 68882 FPU</span></span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">ECS — Super Agnus, Super Denise, Paula</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">1 MB (expandable to 2 MB)</span></div>
<div class="reg-row"><span class="reg-field">Fast RAM</span><span class="reg-val">1 MB on-board (expandable to 16 MB)</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">2.04 (ROM)</span></div>
<div class="reg-row"><span class="reg-field">Expansion</span><span class="reg-val">4× Zorro III (32-bit) slots, 1× CPU slot</span></div>
<div class="reg-row"><span class="reg-field">Storage</span><span class="reg-val">3.5" DD floppy + built-in SCSI controller</span></div>
<div class="reg-row"><span class="reg-field">Form</span><span class="reg-val">Desktop / tower (A3000T)</span></div>
</div>

The A3000 introduced **Zorro III** — a 32-bit expansion bus that could actually exploit the 68030's full bandwidth. It also included a built-in **SCSI** controller (a first for Amiga), a hardware **flicker fixer** (scan-doubling the 15 kHz output to 31 kHz for VGA monitors), and shipped with **AmigaOS 2.0**, a major OS revision.

The ECS chipset brought **Super Denise**, adding productivity modes like 640×480 interlaced and superhires (1280×200), and **Super Agnus** with full 2 MB Chip RAM addressing.

The **A3000T** (tower variant, 1991) offered more drive bays and expansion room. A rare **A3000UX** variant ran Amiga Unix (Amix), making the Amiga a certified Unix workstation.

<!-- section: $A01000 "A500+ & A600 (1991–1992)" -->

Two transitional models that bridged OCS and AGA.

<div class="register-block">
<div class="reg-title">Amiga 500+</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">November 1991</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz</span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">ECS — Fat Agnus (8375), Super Denise (8373), Paula</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">1 MB</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">2.04</span></div>
</div>

The A500+ was a minor revision — essentially an A500 with ECS and Kickstart 2.04. It was short-lived, replaced within months by the A600.

<div class="register-block">
<div class="reg-title">Amiga 600</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">March 1992 — £399</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz</span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">ECS</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">1 MB (expandable to 2 MB)</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">2.05</span></div>
<div class="reg-row"><span class="reg-field">New</span><span class="reg-val"><span class="highlight">PCMCIA slot, IDE interface, smaller form factor</span></span></div>
</div>

The A600 was controversial. It was smaller and cheaper than the A500 but removed the numeric keypad and the side expansion bus. Its main additions were a **PCMCIA Type II slot** (for memory cards and networking) and an internal **IDE interface** for a 2.5" hard drive. However, the lack of a CPU slot or Zorro expansion made it a dead end for upgrades.

Many in the community felt the A600 was a misstep — the resources should have gone toward getting the AGA machines to market faster.

<!-- section: $A01000 "A1200 (1992)" -->

The AGA successor to the A500 — and for many, the last great Amiga.

<div class="register-block">
<div class="reg-title">Amiga 1200</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">October 1992 — £399</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val"><span class="highlight">Motorola 68EC020 @ 14 MHz (32-bit, no MMU)</span></span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">AGA — Alice, Lisa, Paula</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">2 MB</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">3.0 (512 KB ROM)</span></div>
<div class="reg-row"><span class="reg-field">Expansion</span><span class="reg-val">PCMCIA Type II slot, 150-pin trapdoor (CPU accelerator)</span></div>
<div class="reg-row"><span class="reg-field">Storage</span><span class="reg-val">3.5" DD floppy + internal IDE (2.5" HD)</span></div>
<div class="reg-row"><span class="reg-field">Form</span><span class="reg-val">All-in-one with integrated keyboard</span></div>
</div>

The A1200 was a generational leap. The **AGA chipset** brought:

- **24-bit color palette** — 16.7 million colors available (up from 4096)
- **Up to 256 colors** on screen simultaneously in standard modes
- **HAM8** — 262,144 colors on screen (up from HAM6's 4096)
- **2 MB Chip RAM** with wider 32-bit fetch for bitplane data
- **Alice** replaced Agnus with improved DMA and 32-bit addressing
- **Lisa** replaced Denise with the expanded color capabilities

The **68EC020** was a cost-reduced 68020 — full 32-bit data and address buses but without the MMU. It ran at 14 MHz, roughly 2.5× the speed of the 68000 in the A500 for most tasks.

The trapdoor slot accepted accelerator boards with 68030, 68040, or even 68060 processors plus Fast RAM, keeping the A1200 relevant well into the late 1990s. With a Blizzard 1260 accelerator, an A1200 could outperform many contemporary PCs.

<!-- section: $A01000 "A4000 (1992)" -->

The flagship AGA desktop — Commodore's final high-end Amiga.

<div class="register-block">
<div class="reg-title">Amiga 4000</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">October 1992</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val"><span class="highlight">Motorola 68040 @ 25 MHz (A4000/040) or 68EC030 @ 25 MHz (A4000/030)</span></span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">AGA — Alice, Lisa, Paula</span></div>
<div class="reg-row"><span class="reg-field">Chip RAM</span><span class="reg-val">2 MB</span></div>
<div class="reg-row"><span class="reg-field">Fast RAM</span><span class="reg-val">4–16 MB on-board (SIMM slots)</span></div>
<div class="reg-row"><span class="reg-field">Kickstart</span><span class="reg-val">3.0 / 3.1 (512 KB ROM)</span></div>
<div class="reg-row"><span class="reg-field">Expansion</span><span class="reg-val">4× Zorro III slots, 3× ISA, 1× CPU slot, 1× video slot</span></div>
<div class="reg-row"><span class="reg-field">Storage</span><span class="reg-val">3.5" DD/HD floppy + internal IDE (3.5" HD)</span></div>
<div class="reg-row"><span class="reg-field">Form</span><span class="reg-val">Desktop / tower (A4000T)</span></div>
</div>

The A4000 combined the AGA chipset with a 68040 processor — the first Amiga with an on-chip FPU and instruction/data caches. It was a serious workstation-class machine aimed at video production and 3D rendering.

The **A4000/030** variant used a 68EC030 at 25 MHz as a cost-reduced option. Both versions had Zorro III expansion for professional peripherals.

The **A4000T** (tower, 1994) was Commodore's last Amiga product, adding more drive bays and a more accessible internal layout. Only a small number were produced before Commodore's bankruptcy in April 1994.

<!-- section: $A01000 "CDTV & CD32 (1991–1993)" -->

Two multimedia/console variants based on existing Amiga hardware.

<div class="register-block">
<div class="reg-title">CDTV (Commodore Dynamic Total Vision)</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">March 1991 — $999</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68000 @ 7.09 MHz</span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val">OCS</span></div>
<div class="reg-row"><span class="reg-field">RAM</span><span class="reg-val">1 MB Chip RAM</span></div>
<div class="reg-row"><span class="reg-field">Media</span><span class="reg-val">CD-ROM drive + optional floppy</span></div>
<div class="reg-row"><span class="reg-field">Form</span><span class="reg-val">Black set-top box with infrared remote</span></div>
</div>

The CDTV was an A500 in a VCR-style case with a CD-ROM drive, positioned as a multimedia living room device. It predated the market by several years and sold poorly — consumers didn't yet understand what a multimedia player was for.

<div class="register-block">
<div class="reg-title">Amiga CD32</div>
<div class="reg-row"><span class="reg-field">Released</span><span class="reg-val">September 1993 — £299</span></div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">Motorola 68EC020 @ 14 MHz</span></div>
<div class="reg-row"><span class="reg-field">Chipset</span><span class="reg-val"><span class="highlight">AGA — same as A1200</span></span></div>
<div class="reg-row"><span class="reg-field">RAM</span><span class="reg-val">2 MB Chip RAM</span></div>
<div class="reg-row"><span class="reg-field">Media</span><span class="reg-val">2× CD-ROM drive</span></div>
<div class="reg-row"><span class="reg-field">Additional</span><span class="reg-val">Akiko chip (chunky-to-planar conversion)</span></div>
<div class="reg-row"><span class="reg-field">Form</span><span class="reg-val">Game console with gamepad</span></div>
</div>

The CD32 was essentially an A1200 without a keyboard, packaged as a game console. It was the world's first 32-bit CD-ROM console, beating the 3DO and PlayStation to market. It included a custom **Akiko** chip that performed chunky-to-planar pixel format conversion in hardware — useful for porting PC games that used chunky pixel formats.

The CD32 sold reasonably well in Europe but was blocked from the US market by a patent lawsuit from Cad Track. Commodore's bankruptcy in April 1994 ended production.

<!-- section: $A01000 "Chipset Comparison" -->

<div class="bit-table compare-table">
<div class="bit-header">
<span class="bit-num">Feature</span>
<span class="bit-dir">OCS (1985)</span>
<span class="bit-name">ECS (1990)</span>
<span class="bit-func">AGA (1992)</span>
</div>
<div class="bit-row"><span class="bit-num">Max Chip RAM</span><span class="bit-dir">512 KB</span><span class="bit-name">2 MB</span><span class="bit-func">2 MB</span></div>
<div class="bit-row"><span class="bit-num">Color palette</span><span class="bit-dir">4,096</span><span class="bit-name">4,096</span><span class="bit-func">16.7 million</span></div>
<div class="bit-row"><span class="bit-num">Colors on screen</span><span class="bit-dir">32 (HAM: 4096)</span><span class="bit-name">32 (HAM: 4096)</span><span class="bit-func">256 (HAM8: 262,144)</span></div>
<div class="bit-row"><span class="bit-num">Lores</span><span class="bit-dir">320×256</span><span class="bit-name">320×256</span><span class="bit-func">320×256</span></div>
<div class="bit-row"><span class="bit-num">Hires</span><span class="bit-dir">640×256</span><span class="bit-name">640×256</span><span class="bit-func">640×256</span></div>
<div class="bit-row"><span class="bit-num">Super Hires</span><span class="bit-dir">—</span><span class="bit-name">1280×256</span><span class="bit-func">1280×256</span></div>
<div class="bit-row"><span class="bit-num">Productivity</span><span class="bit-dir">—</span><span class="bit-name">640×480i</span><span class="bit-func">640×480i</span></div>
<div class="bit-row"><span class="bit-num">Bitplanes</span><span class="bit-dir">6</span><span class="bit-name">6</span><span class="bit-func">8</span></div>
<div class="bit-row"><span class="bit-num">Sprites</span><span class="bit-dir">8 (3 colors)</span><span class="bit-name">8 (3 colors)</span><span class="bit-func">8 (15 colors)</span></div>
<div class="bit-row"><span class="bit-num">Sprite width</span><span class="bit-dir">16px</span><span class="bit-name">16px</span><span class="bit-func">16/32/64px</span></div>
<div class="bit-row"><span class="bit-num">Fetch width</span><span class="bit-dir">16-bit</span><span class="bit-name">16-bit</span><span class="bit-func">32/64-bit</span></div>
<div class="bit-row"><span class="bit-num">Agnus</span><span class="bit-dir">8361/8370</span><span class="bit-name">8372/8375</span><span class="bit-func">Alice (8374)</span></div>
<div class="bit-row"><span class="bit-num">Denise</span><span class="bit-dir">8362</span><span class="bit-name">8373</span><span class="bit-func">Lisa (4203)</span></div>
<div class="bit-row"><span class="bit-num">Paula</span><span class="bit-dir">8364</span><span class="bit-name">8364</span><span class="bit-func">8364</span></div>
</div>

Paula remained unchanged across all three generations. The audio subsystem — 4 channels of 8-bit DMA-driven PCM — was never upgraded in hardware, which became a growing limitation as 16-bit audio became standard on competing platforms.
