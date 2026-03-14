---
title: "Disk Controller"
addr: "$DFF020"
description: "Amiga floppy disk controller — DMA transfers, MFM encoding, sync word detection, and disk register programming in Paula."
order: 13
---

<!-- section: $DFF020 "Disk Controller Overview" -->

The Amiga's floppy disk controller is a specialized circuit built into the Paula custom chip. It handles the low-level data stream between the disk drive and Chip RAM through a dedicated DMA channel. The controller manages reading and writing of raw encoded data — MFM or GCR — and provides hardware sync word detection for reliable sector location.

The disk hardware is split into two functional areas:

1. **Drive control signals** — motor on/off, head step, side select, drive select — managed through CIA-B port lines (covered in the CIA documentation).
2. **Data transfer** — the encoded bitstream between drive head and memory, managed by Paula's disk controller and its DMA channel.

The controller can read or write an entire track in a single DMA operation. A typical Amiga track contains 11 sectors of 512 bytes each (5632 data bytes per track), though the raw encoded track is considerably larger due to MFM encoding overhead, gaps, and headers.

### Disk Controller Registers

<div class="register-block">
<div class="reg-title">Disk DMA and Control Registers</div>
<div class="reg-row"><span class="reg-field">$DFF020</span><span class="reg-val">DSKPTH — Disk DMA pointer (high word, bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF022</span><span class="reg-val">DSKPTL — Disk DMA pointer (low word, bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF024</span><span class="reg-val">DSKLEN — Disk DMA transfer length and control (write only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF01A</span><span class="reg-val">DSKBYTR — Disk data byte and status (read only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF07E</span><span class="reg-val">DSKSYNC — Disk sync word (write only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF026</span><span class="reg-val">DSKDAT — Disk DMA data write (write only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF008</span><span class="reg-val">DSKDAT — Disk DMA data read (read only, early-read register)</span></div>
</div>

<!-- section: $DFF020 "DMA Pointer and Length" -->

Before initiating any disk DMA transfer, the buffer address and transfer length must be configured.

### DSKPTH / DSKPTL — DMA Pointer ($DFF020 / $DFF022, write)

This register pair holds the 19-bit Chip RAM address where disk data will be read from or written to. The pointer is loaded as two separate word writes, high word first:

```asm
    move.l #DiskBuffer,$DFF020    ; load DSKPTH and DSKPTL in one MOVE.L
```

The buffer must reside in Chip RAM (first 512 KB on OCS, 1 MB on ECS, 2 MB on AGA).

### DSKLEN — Transfer Length and Control ($DFF024, write)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">DMAEN</span><span class="bit-name">Enable disk DMA (must be written twice to activate)</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">14</span><span class="bit-dir">WRITE</span><span class="bit-name">Set to 1 for write, 0 for read</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13–0</span><span class="bit-dir">LENGTH</span><span class="bit-name">Number of words to transfer (max 16383)</span><span class="bit-func"></span></div>
</div>

### LENGTH Field

The lower 14 bits specify the number of 16-bit words to transfer between the disk and memory.

### WRITE Bit

When WRITE is set to 1, the controller operates in write mode — data flows from memory to the disk. When cleared, data flows from disk to memory (read mode). For safety, WRITE should only be set when an actual write operation is intended.

### DMAEN — DMA Enable (Double-Write Protection)

The DMAEN bit is the master enable for disk DMA. As a critical safety measure, **DMAEN must be written to 1 twice in succession** before the DMA transfer will actually begin. This prevents an accidental write from triggering a potentially destructive disk operation. A single erroneous write to DSKLEN cannot start a transfer.

Additionally, the global disk DMA enable bit (DSKEN, bit 4) in the DMACON register must be set.

<!-- section: $DFF020 "DMA Initialization Sequence" -->

The correct procedure for starting a disk DMA operation follows a strict sequence to prevent data corruption:

### Read Operation

```asm
    ; 1. Disable any previous DMA
    move.w #$4000,$DFF024      ; clear DMAEN in DSKLEN

    ; 2. Ensure global disk DMA is enabled
    move.w #$8010,$DFF096      ; set DSKEN (bit 4) in DMACON

    ; 3. Set the buffer address
    move.l #DiskBuffer,$DFF020 ; DSKPTH/DSKPTL

    ; 4. Write DSKLEN with desired length and DMAEN
    move.w #$8000+NumWords,$DFF024  ; DMAEN=1, WRITE=0, LENGTH=NumWords

    ; 5. Write the same value again (double-write to activate)
    move.w #$8000+NumWords,$DFF024

    ; 6. Wait for DSKBLK interrupt (DMA complete)
    ;    ... (poll INTREQR bit 1 or use interrupt handler) ...

    ; 7. Disable DMA as safety measure
    move.w #$4000,$DFF024      ; clear DMAEN
```

### Write Operation

```asm
    ; 1. Disable any previous DMA
    move.w #$4000,$DFF024      ; clear DMAEN

    ; 2. Enable global disk DMA
    move.w #$8010,$DFF096      ; set DSKEN in DMACON

    ; 3. Set the buffer address (source data in Chip RAM)
    move.l #WriteBuffer,$DFF020

    ; 4. Write DSKLEN with DMAEN + WRITE + length
    move.w #$C000+NumWords,$DFF024  ; DMAEN=1, WRITE=1, LENGTH=NumWords

    ; 5. Write same value again
    move.w #$C000+NumWords,$DFF024

    ; 6. Wait for DSKBLK interrupt
    ;    ... (critical: do NOT stop DMA prematurely during writes!) ...

    ; 7. Disable DMA
    move.w #$4000,$DFF024
```

**Warning:** Interrupting a write operation before completion can destroy the data on the current track. Always wait for the DSKBLK interrupt before disabling DMA after a write.

### DSKBLK Interrupt

When the controller has transferred the number of words specified in LENGTH, it triggers the **DSKBLK** (Disk Block Finished) interrupt — bit 1 in INTREQ/INTEN. This signals that the DMA operation is complete and the buffer contains valid data (for reads) or that the data has been fully written to disk (for writes).

<!-- section: $DFF020 "DSKBYTR — Disk Status" -->

The DSKBYTR register provides real-time status of the disk controller and allows byte-level CPU polling of the data stream (though DMA is the normal transfer method).

<div class="register-block">
<div class="reg-title">Disk Byte and Status Register</div>
<div class="reg-row"><span class="reg-field">$DFF01A</span><span class="reg-val">DSKBYTR — Disk data byte and status (read only)</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">BYTEREADY</span><span class="bit-name">Set when a complete data byte is available in bits 7–0</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">14</span><span class="bit-dir">DMAON</span><span class="bit-name">Disk DMA is active (DMAEN in DSKLEN and DSKEN in DMACON both set)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">DSKWRITE</span><span class="bit-name">Reflects WRITE bit from DSKLEN (1 = write mode)</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">12</span><span class="bit-dir">WORDEQUAL</span><span class="bit-name">Current disk word matches DSKSYNC value</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11–8</span><span class="bit-dir">—</span><span class="bit-name">Unused</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–0</span><span class="bit-dir">DATA</span><span class="bit-name">Current data byte from disk</span><span class="bit-func"></span></div>
</div>

### CPU Polling Mode

Using BYTEREADY and DATA, the CPU can read disk data one byte at a time without DMA. Each time a complete byte arrives from the disk, BYTEREADY is set. Reading DSKBYTR clears BYTEREADY. This method is much slower than DMA and is rarely used in practice, but it is available for diagnostic or special-purpose code.

### WORDEQUAL

The WORDEQUAL bit is set briefly (for approximately 2 microseconds) whenever the incoming disk data matches the sync word stored in DSKSYNC. Because this bit is set for such a short time, it is difficult to catch by polling; the DSKSYNC interrupt (INTREQ bit 12) is more reliable.

<!-- section: $DFF020 "Sync Word and DSKSYNC" -->

The **DSKSYNC** register allows the disk controller to begin DMA transfer at a specific position in the data stream rather than at an arbitrary point.

<div class="register-block">
<div class="reg-title">Disk Sync Register</div>
<div class="reg-row"><span class="reg-field">$DFF07E</span><span class="reg-val">DSKSYNC — Disk sync pattern word (write only)</span></div>
</div>

When disk DMA is initiated, the controller reads data from the disk but does **not** immediately write it to memory. Instead, it compares each incoming word against the value in DSKSYNC. Only when a match is found does the actual memory transfer begin. This mechanism allows the controller to lock onto a specific synchronization marker at the start of a data block.

### Standard Amiga Sync Word

The standard MFM sync word used by AmigaDOS is **$4489**. This is a self-synchronizing pattern that cannot occur in normal MFM-encoded data, making it a reliable marker for sector boundaries.

```asm
    move.w #$4489,$DFF07E      ; set standard Amiga MFM sync word
```

### DSKSYNC Interrupt

When the disk data matches DSKSYNC, the controller generates a **DSKSYNC interrupt** — bit 12 in INTREQ/INTEN. This occurs independently of whether DMA transfer has begun. Software can use this interrupt to detect the start of a sector without actually reading the data.

The WORDSYNC bit (bit 10) in ADKCON must be set to enable sync word matching. When WORDSYNC is cleared, the controller begins DMA immediately without waiting for a sync word.

<!-- section: $DFF020 "MFM Encoding and ADKCON" -->

Data cannot be written to a floppy disk in its raw binary form. It must first be encoded using a scheme that guarantees sufficient signal transitions for the drive's read circuitry to maintain clock synchronization. The Amiga supports two encoding methods:

- **MFM** (Modified Frequency Modulation) — the standard encoding used by AmigaDOS and most Amiga software.
- **GCR** (Group Code Recording) — an alternative encoding used by some other systems.

The encoding mode and related parameters are configured through bits in the ADKCON register.

<div class="register-block">
<div class="reg-title">ADKCON Disk-Related Bits</div>
<div class="reg-row"><span class="reg-field">$DFF09E</span><span class="reg-val">ADKCON — Audio/Disk control (write only, bit 15 = SET/CLR)</span></div>
<div class="reg-row"><span class="reg-field">$DFF010</span><span class="reg-val">ADKCONR — Audio/Disk control readback (read only)</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">SET/CLR</span><span class="bit-name">1 = set bits, 0 = clear bits (write only)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">PRECOMP1</span><span class="bit-name">Precompensation select (high bit)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">13</span><span class="bit-dir">PRECOMP0</span><span class="bit-name">Precompensation select (low bit)</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">12</span><span class="bit-dir">MFMPREC</span><span class="bit-name">Encoding mode: 0 = GCR, 1 = MFM</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">WORDSYNC</span><span class="bit-name">Enable sync word matching (1 = enabled)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">MSBSYNC</span><span class="bit-name">Enable MSB synchronization (GCR mode)</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">8</span><span class="bit-dir">FAST</span><span class="bit-name">Disk clock rate: 1 = 2 us/bit (MFM), 0 = 4 us/bit (GCR)</span><span class="bit-func"></span></div>
</div>

### Precompensation

When writing data to the inner tracks of a floppy disk, the higher bit density can cause adjacent magnetic transitions to shift each other. Precompensation adjusts the timing of write pulses to counteract this effect.

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit 14</span>
<span class="bit-dir">Bit 13</span>
<span class="bit-name">Precompensation Time</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">0</span><span class="bit-name">None (disabled)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">1</span><span class="bit-name">140 nanoseconds</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">0</span><span class="bit-name">280 nanoseconds</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">1</span><span class="bit-name">560 nanoseconds</span><span class="bit-func"></span></div>
</div>

### Standard MFM Configuration

For normal Amiga disk operations (880 KB double-density disks), the standard ADKCON configuration is:

```asm
    ; Enable MFM mode, WORDSYNC, FAST clock rate
    move.w #$9500,$DFF09E      ; SET + MFMPREC + WORDSYNC + FAST
```

This sets MFM encoding, enables sync word detection for sector-aligned reads, and selects the 2-microsecond-per-bit clock rate appropriate for MFM on double-density media.

### MFM Encoding Principle

In MFM encoding, each data bit is accompanied by a clock bit. The clock bit is set to 1 only when both the current data bit and the preceding data bit are 0. This guarantees that there is never more than three consecutive zeros in the encoded bitstream, maintaining reliable clock recovery by the drive hardware.

The encoding rule:

- Data bit **1** is encoded as **01** (no clock bit needed).
- Data bit **0** preceded by data bit **1** is encoded as **00** (no clock bit).
- Data bit **0** preceded by data bit **0** is encoded as **10** (clock bit inserted).

This means each data byte becomes 16 bits on disk (8 data bits + 8 clock bits, interleaved). Software must perform the MFM encoding/decoding — the controller only handles the physical read/write of the encoded bitstream.

<!-- section: $DFF020 "Data Registers" -->

The disk controller uses dedicated data registers for the actual byte-level transfer between the DMA channel and the disk drive.

### DSKDAT — Write Data ($DFF026, write)

This register holds the data to be written to the disk. During a DMA write operation, the DMA controller fills this register automatically from memory at the rate determined by the disk clock.

### DSKDAT — Read Data ($DFF008, read)

This register holds the data read from the disk. It is an **early-read register**, which means it is exclusively used by the DMA controller and cannot be read by the CPU. The CPU should use DSKBYTR for byte-level access to disk data, or more typically, let DMA transfer the data directly to a memory buffer.

### Complete Read Example

The following code reads one raw MFM track from the disk on drive 0:

```asm
ReadTrack:
    lea    $DFF000,a5

    ; Disable previous DMA
    move.w #$4000,DSKLEN(a5)       ; clear DMAEN

    ; Set buffer pointer
    move.l #TrackBuffer,DSKPTH(a5)

    ; Set sync word
    move.w #$4489,DSKSYNC(a5)      ; standard AmigaDOS sync

    ; Configure ADKCON for MFM + sync
    move.w #$7F00,ADKCON(a5)       ; clear disk bits first
    move.w #$9500,ADKCON(a5)       ; set MFMPREC + WORDSYNC + FAST

    ; Enable disk DMA
    move.w #$8010,DMACON(a5)       ; set DSKEN

    ; Start DMA read: 0x1900 words = 6400 words = 12800 bytes (one track)
    move.w #$8000+$1900,DSKLEN(a5) ; first write
    move.w #$8000+$1900,DSKLEN(a5) ; second write (activates DMA)

    ; Wait for completion
.wait:
    btst   #1,INTREQR+1(a5)       ; test DSKBLK bit
    beq.s  .wait

    ; Clean up
    move.w #$4000,DSKLEN(a5)       ; disable disk DMA
    move.w #$0002,INTREQ(a5)       ; clear DSKBLK interrupt
    rts
```

The buffer now contains the raw MFM-encoded track data, starting from the first occurrence of the sync word $4489. Software must then locate sector headers and decode the MFM data to extract the 512-byte sectors.
