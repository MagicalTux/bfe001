---
title: "Mind — BFE001"
description: "How my mind works — hypophantasia, wireframe thinking, and spatial memory."
---

<!-- section: $040000 "How My Mind Works" -->

Almost no memory I have involves images. I have a few, but most things I
remember are feelings or information that can't easily be described with words.
If you tell me *"remember that blond girl yesterday"* I'd have no idea
whom you're talking about. I've even realized mid-conversation that some of
my former employees from a decade ago were black, but I didn't know who that
was until I was told their names. My brain simply doesn't store visual
appearance as a retrieval key.

This has a name: **hypophantasia**—a low-imagery cognitive style.
If you ask me to imagine a red apple, I can somehow picture the stem on top,
the bottom indent. But the whole apple? A red circle at best. Organic shapes
collapse into simple geometry. Technical objects—a computer, a house,
a lock mechanism—I can rotate freely in my mind, but they appear as
wireframes: structure without texture.

<div class="pullquote">
My brain represents the world as structure, relationships, and spatial maps—not
as images. Walls, floors, rooms: I can mentally float through every house I've ever
lived in, but in wireframe mode. Geometry without colour.
</div>

Yet my spatial reasoning is strong. I can mentally float around every house
or apartment I ever lived in—placing rooms relative to other rooms across
different floors, locating windows, switches, furniture. If you've ever used
a 3D level editor like Hammer (the one that shipped with Half-Life), imagine
that: brush geometry, entities, relationships. That's roughly what my internal
representation looks like.

Complex systems appear in my mind as something between a spatial diagram
and a logical tree. Nodes and connections, dependencies and constraints—but
the graph can't be forced into 3D space because relationships cross and overlap
in ways that a physical layout can't represent. When I think about software
architecture or infrastructure, I'm navigating that structure.

<div class="mem-block">
<span class="label">COGNITIVE_PROFILE</span> <span class="comment">; summary</span>
  <span class="dim">├──</span> <span class="keyword">spatial_structure</span>    <span class="val">■■■■■■■■■░</span>  <span class="comment">; strong</span>
  <span class="dim">├──</span> <span class="keyword">system_modeling</span>     <span class="val">■■■■■■■■■░</span>  <span class="comment">; strong</span>
  <span class="dim">├──</span> <span class="keyword">mechanistic_reason</span>  <span class="val">■■■■■■■■■■</span>  <span class="comment">; very strong</span>
  <span class="dim">├──</span> <span class="keyword">recognition_memory</span>  <span class="val">■■■■■■■■░░</span>  <span class="comment">; strong</span>
  <span class="dim">├──</span> <span class="keyword">visual_imagery</span>      <span class="val">■■░░░░░░░░</span>  <span class="comment">; weak</span>
  <span class="dim">├──</span> <span class="keyword">face_recall</span>         <span class="val">■░░░░░░░░░</span>  <span class="comment">; very weak</span>
  <span class="dim">└──</span> <span class="keyword">timeline_ordering</span>  <span class="val">■■░░░░░░░░</span>  <span class="comment">; weak</span>
</div>

<!-- section: $080000 "Memory as Snapshots" -->

My memories are frozen instants I can inspect—like breakpoints in a running
program. They're not video playback. Each one is a single frame pinned to a
location on a spatial map, with some metadata attached: who was there, what
happened, the emotional state. But no continuous timeline, no before or after.

<div class="snapshot">
    <p>
        My 7th birthday in Dijon, 41 Rue Neuve Bergère. Parkings owned by my grandmother,
        an event room in the back behind a small area of trees and green. Children from
        school around a table. I laughed so hard that juice came out my nose. I can
        point to the exact location of that table. But there are no faces, no images,
        and no memory of who those children were. Nothing before or after that instant.
    </p>
    <div class="snapshot-meta">LOC: 41 Rue Neuve Bergère, Dijon · AGE: 7 · ANCHOR: spatial + emotional spike</div>
</div>

Time itself is abstract for me. I can map spaces but not changes in space.
I can update the map, but previous versions are usually lost. I have some
memories locked in time because they're attached to a birthday or a known event,
but generally I can't place memories on a timeline. Remembering what I did
yesterday or last week is almost impossible without looking at pictures, unless
something notable happened.

I now offload temporal indexing to technology—mostly Google Photos, where
I can quickly search for objects or places and retrieve timestamps. It's essentially
an external memory system: the brain holds the spatial map and the structural
knowledge, and the phone provides the *when*.

<div class="mem-block">
<span class="label">MEMORY_MODEL</span>
  <span class="dim">├──</span> <span class="keyword">index</span>: <span class="val">spatial (place cells)</span>
  <span class="dim">├──</span> <span class="keyword">format</span>: <span class="val">frozen snapshot + metadata</span>
  <span class="dim">├──</span> <span class="keyword">visual_layer</span>: <span class="val">absent</span>
  <span class="dim">├──</span> <span class="keyword">temporal_tag</span>: <span class="val">sparse, event-anchored only</span>
  <span class="dim">└──</span> <span class="keyword">ext_storage</span>: <span class="val">Google Photos (temporal index)</span>
</div>

<!-- section: $0C0000 "The Curiosity Loop" -->

As a child I would dismantle calculators, remote controllers, and anything
I wanted to understand. More often than not I was met with a boring black
epoxy blob hiding all the interesting parts. But the drive was always the same:
see the mechanism, build the internal model, predict the behavior.

<div class="mem-block">
<span class="label">CURIOSITY_CYCLE</span>
  <span class="val">encounter system</span>
      <span class="dim">↓</span>
  <span class="val">infer internal structure</span>
      <span class="dim">↓</span>
  <span class="val">build mental model</span>
      <span class="dim">↓</span>
  <span class="val">system becomes predictable</span>
      <span class="dim">↓</span>
  <span class="val">interest drops → next unknown</span>
</div>

The flip side is that by wanting to understand and do everything myself,
everything becomes difficult. I once wrote that the geek is a kind of
Shadok: why do it simply when you can make it complicated? You'll spend
all night compiling Firefox from source—patching, debugging, chasing
dependencies—and at 6am, when the sun comes up, you `apt-get install`
the release build and go to sleep. The point was never the browser.
The point was the hundred things you learned along the way. This
eventually led me to build **Azusa**, a full pseudo-Linux distribution
where most GNU components — glibc and all — live under a dedicated
directory, designed to work alongside another distribution. That means
compiling everything myself, some packages far more painful than others.

I now know how the internals of a CPU work—from instructions to registers
to logic gates to transistors—and I have a good idea of how a calculator
or remote controller chip is made without dismantling anything. The same applies
to anything mechanical: door handle mechanisms, locks, machines. I can usually
imagine how something was made just by looking at whatever moving parts are
visible, and quickly reverse-engineer the internal workings.

The same intuition applies to software. I can see inside most systems almost
instantly, getting a good idea of the process used to reach the final product.
I learned to content myself with understanding the process rather than being
able to reproduce it fully. The reward is the model, not the object.

Debugging complex problems works best in the background. I keep them in mind,
and the best place for solutions to appear is usually while taking a shower.
The brain builds a complex relational model, and when I relax, the default
network reorganises it until the correct structure emerges.
