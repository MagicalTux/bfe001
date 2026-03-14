---
title: "Audio System"
addr: "$DFF0A0"
description: "The Amiga audio system — 4 DMA-driven channels with 8-bit samples, stereo output, volume control, and inter-channel modulation."
order: 10
---

<!-- section: $DFF0A0 "Audio Overview" -->

The Amiga's audio system consists of **4 independent DMA channels**, each capable of playing digitized 8-bit sound samples from Chip RAM. The system outputs **stereo** audio with a fixed channel-to-speaker assignment:

- **Left speaker**: channels 0 and 3
- **Right speaker**: channels 1 and 2

Each channel has its own sample pointer, length counter, volume control, and period (sample rate) register. The hardware handles continuous playback automatically via DMA — the CPU only needs to set up the registers and enable DMA.

### How Sound Generation Works

The Amiga stores digitized waveforms in memory as sequences of **8-bit signed values** (two's complement, range -128 to +127). A digital-to-analog converter (DAC) reads these samples at a programmable rate and produces an analog voltage output. The output is then sent through a low-pass filter to the audio output jacks.

Sound samples are stored in Chip RAM as pairs of bytes packed into 16-bit words. The DMA controller reads one word at a time — the **high byte** (bits 15–8) is output first, followed by the **low byte** (bits 7–0). For this reason, waveform data must always consist of an **even number** of samples.

The three fundamental parameters of a sound — **frequency** (pitch), **amplitude** (volume), and **timbre** (waveform shape) — are all under software control. Frequency is set via the period register, amplitude via the volume register, and timbre is determined by the waveform data itself.

<!-- section: $DFF0A0 "Per-Channel Registers" -->

Each audio channel has 5 registers occupying 16 bytes. The four channels are laid out at consecutive 16-byte offsets starting at $DFF0A0.

<div class="register-block">
<div class="reg-title">Channel 0 Registers</div>
<div class="reg-row"><span class="reg-field">$DFF0A0</span><span class="reg-val">AUD0LCH — Sample pointer, high word (bits 16–18)</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A2</span><span class="reg-val">AUD0LCL — Sample pointer, low word (bits 0–15)</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A4</span><span class="reg-val">AUD0LEN — Sample length in words (1 word = 2 samples)</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A6</span><span class="reg-val">AUD0PER — Sample period (playback rate)</span></div>
<div class="reg-row"><span class="reg-field">$DFF0A8</span><span class="reg-val">AUD0VOL — Volume (0–64)</span></div>
<div class="reg-row"><span class="reg-field">$DFF0AA</span><span class="reg-val">AUD0DAT — Audio data register (write triggers DMA transfer)</span></div>
</div>

<div class="register-block">
<div class="reg-title">Channel 1 Registers</div>
<div class="reg-row"><span class="reg-field">$DFF0B0</span><span class="reg-val">AUD1LCH — Sample pointer, high word</span></div>
<div class="reg-row"><span class="reg-field">$DFF0B2</span><span class="reg-val">AUD1LCL — Sample pointer, low word</span></div>
<div class="reg-row"><span class="reg-field">$DFF0B4</span><span class="reg-val">AUD1LEN — Sample length in words</span></div>
<div class="reg-row"><span class="reg-field">$DFF0B6</span><span class="reg-val">AUD1PER — Sample period</span></div>
<div class="reg-row"><span class="reg-field">$DFF0B8</span><span class="reg-val">AUD1VOL — Volume (0–64)</span></div>
<div class="reg-row"><span class="reg-field">$DFF0BA</span><span class="reg-val">AUD1DAT — Audio data register</span></div>
</div>

<div class="register-block">
<div class="reg-title">Channel 2 Registers</div>
<div class="reg-row"><span class="reg-field">$DFF0C0</span><span class="reg-val">AUD2LCH — Sample pointer, high word</span></div>
<div class="reg-row"><span class="reg-field">$DFF0C2</span><span class="reg-val">AUD2LCL — Sample pointer, low word</span></div>
<div class="reg-row"><span class="reg-field">$DFF0C4</span><span class="reg-val">AUD2LEN — Sample length in words</span></div>
<div class="reg-row"><span class="reg-field">$DFF0C6</span><span class="reg-val">AUD2PER — Sample period</span></div>
<div class="reg-row"><span class="reg-field">$DFF0C8</span><span class="reg-val">AUD2VOL — Volume (0–64)</span></div>
<div class="reg-row"><span class="reg-field">$DFF0CA</span><span class="reg-val">AUD2DAT — Audio data register</span></div>
</div>

<div class="register-block">
<div class="reg-title">Channel 3 Registers</div>
<div class="reg-row"><span class="reg-field">$DFF0D0</span><span class="reg-val">AUD3LCH — Sample pointer, high word</span></div>
<div class="reg-row"><span class="reg-field">$DFF0D2</span><span class="reg-val">AUD3LCL — Sample pointer, low word</span></div>
<div class="reg-row"><span class="reg-field">$DFF0D4</span><span class="reg-val">AUD3LEN — Sample length in words</span></div>
<div class="reg-row"><span class="reg-field">$DFF0D6</span><span class="reg-val">AUD3PER — Sample period</span></div>
<div class="reg-row"><span class="reg-field">$DFF0D8</span><span class="reg-val">AUD3VOL — Volume (0–64)</span></div>
<div class="reg-row"><span class="reg-field">$DFF0DA</span><span class="reg-val">AUD3DAT — Audio data register</span></div>
</div>

### AUDxLC — Sample Pointer

This 18-bit register pair points to the start of the waveform data in Chip RAM. The DMA controller copies this value into an internal address register before it begins fetching data, so the original AUDxLC value is preserved and can be updated for the next cycle without disrupting current playback.

### AUDxLEN — Sample Length

The length is specified in **words** (not bytes). For example, a 16-sample waveform occupies 8 words:

```asm
MOVE.W #(End-Start)/2,AUD0LEN(a5)
```

When the DMA controller has output `AUDxLEN` words, it reloads AUDxLC and AUDxLEN into its internal registers and restarts from the beginning, creating a continuous loop.

### AUDxPER — Period (Sample Rate)

The period register controls the time between consecutive sample outputs. Each unit equals one bus cycle = **279.365 nanoseconds** (on PAL systems; 279.365 ns corresponds to a 3.579545 MHz clock).

The formula for the period value:

```
Period = 1 / (Frequency * SamplesPerCycle * 279.365e-9)
```

Or equivalently:

```
Period = 3,579,545 / (Frequency * SamplesPerCycle)
```

**Example:** To play a 440 Hz tone using a 16-sample waveform:

```
Period = 1 / (440 * 16 * 0.000000279365) = 508
```

The practical minimum period is **124** (roughly 28,867 Hz sample rate). Below this value, the DMA channel cannot fetch data fast enough — it only gets one DMA slot per raster line — and samples may be repeated. The maximum period is 65,535, allowing extremely slow playback.

### AUDxVOL — Volume

Volume ranges from **0** (silent) to **64** (maximum). Values above 64 are treated as 64. Each channel's volume is independent:

```asm
MOVE.W #32,AUD0VOL(a5)    ; half volume on channel 0
```

For best audio quality, always keep the waveform data at full 8-bit amplitude (-128 to +127) and use the volume register to control loudness. Reducing the waveform amplitude directly would increase the relative quantization noise.

<!-- section: $DFF0A0 "Audio DMA and Interrupts" -->

### Enabling Audio DMA

Audio DMA is controlled by bits 0–3 of the **DMACON** register ($DFF096):

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">DMACON Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Channel</span>
<span class="bit-func">Description</span>
</div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">AUD0EN</span><span class="bit-name">Channel 0</span><span class="bit-func">Enable DMA for audio channel 0</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">AUD1EN</span><span class="bit-name">Channel 1</span><span class="bit-func">Enable DMA for audio channel 1</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">AUD2EN</span><span class="bit-name">Channel 2</span><span class="bit-func">Enable DMA for audio channel 2</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">AUD3EN</span><span class="bit-name">Channel 3</span><span class="bit-func">Enable DMA for audio channel 3</span></div>
</div>

To start playback on channel 0:

```asm
LEA     $DFF000,a5
MOVE.L  #SampleData,AUD0LCH(a5)   ; set sample address
MOVE.W  #(SampleEnd-SampleData)/2,AUD0LEN(a5)  ; set length
MOVE.W  #508,AUD0PER(a5)          ; set period (440 Hz @ 16 samples)
MOVE.W  #64,AUD0VOL(a5)           ; maximum volume
MOVE.W  #$8201,DMACON(a5)         ; enable AUD0EN + DMAEN
```

### DMA Cycle

When audio DMA is enabled, the following sequence occurs:

1. AUDxLC and AUDxLEN are copied to internal registers
2. An **audio interrupt** is triggered (signaling the CPU that new values can be loaded)
3. The DMA controller fetches words from the internal address, one word per raster line
4. Each word's high byte is output first, then the low byte, at the rate set by AUDxPER
5. When the internal word counter reaches zero, AUDxLC and AUDxLEN are reloaded and another interrupt fires

This means the CPU can update AUDxLC and AUDxLEN while DMA is active — the changes take effect at the start of the next cycle. This enables seamless streaming of long samples by alternating between two buffers.

### Audio Interrupts

Each channel has a dedicated interrupt bit in INTREQ/INTENA:

- **Bit 7**: Audio channel 0
- **Bit 8**: Audio channel 1
- **Bit 9**: Audio channel 2
- **Bit 10**: Audio channel 3

All four are **level 4 interrupts**. The interrupt fires each time the DMA controller reloads AUDxLC/AUDxLEN from the registers — essentially once per complete waveform cycle. For high-frequency sounds, interrupts can occur very frequently, so enable them only when needed to avoid saturating the CPU.

<!-- section: $DFF0A0 "Modulation" -->

The Amiga supports inter-channel **modulation**, where one audio channel modifies the period or volume of the next channel in real time. The modulating channel's waveform data is not sent to the DAC — instead it is written directly to the target channel's period or volume register.

The modulation chain is fixed:

- Channel 0 modulates channel 1
- Channel 1 modulates channel 2
- Channel 2 modulates channel 3
- Channel 3 can be set as a modulator but has no target (data is discarded)

When a channel is used as a modulator, its audio output is **automatically disabled**.

### ADKCON — Audio/Disk Control Register

Modulation is configured through the **ADKCON** register ($DFF09E write / $DFF010 read):

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func">Description</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">SET/CLR</span><span class="bit-name">Set/Clear</span><span class="bit-func">1 = set listed bits, 0 = clear listed bits</span></div>
<div class="bit-row"><span class="bit-num">14–8</span><span class="bit-dir">—</span><span class="bit-name">Disk control</span><span class="bit-func">Disk controller bits (not audio-related)</span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">USE3PN</span><span class="bit-name">Ch 3 period mod</span><span class="bit-func">Channel 3 does not modulate anything (no target)</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">USE2P3</span><span class="bit-name">Ch 2 -> Ch 3 period</span><span class="bit-func">Channel 2 modulates the period of channel 3</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">USE1P2</span><span class="bit-name">Ch 1 -> Ch 2 period</span><span class="bit-func">Channel 1 modulates the period of channel 2</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">USE0P1</span><span class="bit-name">Ch 0 -> Ch 1 period</span><span class="bit-func">Channel 0 modulates the period of channel 1</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">USE3VN</span><span class="bit-name">Ch 3 volume mod</span><span class="bit-func">Channel 3 does not modulate anything (no target)</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">USE2V3</span><span class="bit-name">Ch 2 -> Ch 3 volume</span><span class="bit-func">Channel 2 modulates the volume of channel 3</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">USE1V2</span><span class="bit-name">Ch 1 -> Ch 2 volume</span><span class="bit-func">Channel 1 modulates the volume of channel 2</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">USE0V1</span><span class="bit-name">Ch 0 -> Ch 1 volume</span><span class="bit-func">Channel 0 modulates the volume of channel 1</span></div>
</div>

### Period Modulation

When period modulation is enabled (e.g., USE0P1=1), channel 0's waveform data is interpreted as 16-bit period values and written directly to AUD1PER. This creates a **vibrato** effect — the pitch of channel 1 oscillates according to the waveform played on channel 0.

### Volume Modulation

When volume modulation is enabled (e.g., USE0V1=1), channel 0's data words are interpreted as 7-bit volume values (0–64 in bits 6–0) and written to AUD1VOL. This creates a **tremolo** effect or can be used to shape amplitude envelopes (attack/decay/sustain/release).

### Combined Modulation

Both period and volume modulation can be active simultaneously on the same channel pair. In this case, data words alternate:

| Word # | Destination |
|--------|------------|
| 1 | Volume |
| 2 | Period |
| 3 | Volume |
| 4 | Period |
| ... | ... |

This uses the modulating channel's data at twice the rate, so plan waveform lengths accordingly.

<!-- section: $DFF0A0 "Low-Pass Filter" -->

The Amiga includes a **hardware low-pass filter** between the DAC output and the audio output jacks. This filter attenuates frequencies above approximately **4 kHz**, with complete attenuation above **7 kHz**.

### Purpose

When a digital waveform is reconstructed as an analog signal, **aliasing** artifacts appear at frequencies equal to the sum and difference of the sample rate and the intended frequency. The low-pass filter removes these artifacts, producing cleaner audio output. However, it also limits the audio bandwidth — high-frequency harmonics of complex waveforms (square waves, sawtooth, etc.) are lost, and square waves at higher pitches begin to sound like sine waves.

### Filter Control

The filter is controlled by **bit 1 of CIA-A Port B** ($BFE001). This is the same bit that controls the power LED brightness:

- **Bit 1 = 0**: Filter is **enabled** (LED is bright) — default state
- **Bit 1 = 1**: Filter is **disabled** (LED is dim)

```asm
; Disable the low-pass filter
BSET #1,$BFE001
```

The filter should be disabled when playing pre-filtered or high-quality samples where the aliasing is already accounted for, or when maximum frequency range is desired. It should remain enabled for simple synthesized waveforms to avoid audible aliasing.

### Sampling Rate Guidelines

To avoid audible aliasing even with the filter enabled, follow this rule:

```
Sample rate > highest frequency component + 7000 Hz
```

Note that "highest frequency component" includes harmonics of the waveform, not just the fundamental frequency. A square wave at 1 kHz has significant harmonics at 3 kHz, 5 kHz, 7 kHz, and beyond.
