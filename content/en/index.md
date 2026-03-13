---
title: "BFE001 — Mark Karpelès"
description: "BFE001 — The story of a mind that thinks in wireframes, from the ZX Spectrum to the Amiga and beyond."
---

<!-- section: $000000 "Who I Am" -->

I'm **Mark Karpelès**. I'm a systems thinker. I've spent most of
my life building mental models of how things work—from the internals of a
CPU down to transistors, from door handle mechanisms to distributed software
architectures. Understanding the mechanism is the reward. Once a system becomes
predictable, my brain moves on to the next unknown.

This site is named after `$BFE001`, a hardware register on the
Amiga's CIA 8520 chip. Writing bit 1 of that address toggled the
power LED and the audio low-pass filter. Some games used it to simulate the
muffled sound of passing through a tunnel. Of all the things I coded on the
Amiga, that single instruction—`MOVE.b #$02,$BFE001`—stuck
with me across decades. It felt like reaching into the machine and changing
its physical state with a line of code.

That feeling—of seeing inside a system, understanding its structure,
and knowing exactly which wire to pull—is how I've experienced the world
for as long as I can remember.

<!-- section: $BFE001 "Why This Name" -->

The Amiga mapped its hardware into the CPU's address space. Writing to a memory
address didn't just store a value—it changed the physical state of the
machine. `$BFE001` was the address of CIA-A's Port A register,
which controlled the keyboard, the game port buttons, the power LED, and the
audio low-pass filter.

<div class="register-block">
    <div class="reg-title">$BFE001 — CIA-A Port A (PRA)</div>
    <div class="reg-row">
        <span class="reg-field">Bit 7–2</span>
        <span class="reg-val">Game port, keyboard serial data, overlay</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Bit 1</span>
        <span class="reg-val"><span class="highlight">LED control / audio low-pass filter toggle</span></span>
    </div>
    <div class="reg-row">
        <span class="reg-field">Bit 0</span>
        <span class="reg-val">Overlay bit (ROM/RAM mapping at $000000)</span>
    </div>
</div>

Games would toggle bit 1 to create effects like the muffled sound of
a tunnel. On the Amiga 2000, it dimmed the power LED instead of
fully turning it off. A single bit, a single address, and the machine changed
its physical behavior.

That instruction—`MOVE.b #$02,$BFE001`—was one of the
first moments I felt the boundary between software and hardware dissolve.
It's the reason this site exists. It represents a way of thinking about
computers: not as black boxes, but as structures you can see into, understand,
and manipulate at every level.

<div class="pullquote">
Those were the good times. The machine was simple enough that you could
understand the whole stack—from application code down to hardware
registers. Modern systems are far more powerful, but vastly more opaque.
</div>
