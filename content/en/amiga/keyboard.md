---
title: "Keyboard"
addr: "$KBD001"
description: "The Amiga keyboard subsystem — MOS 6500/1 microprocessor, key matrix scanning, serial data transfer protocol, and handshake mechanism."
order: 14
---

<!-- section: $KBD001 "Keyboard Overview" -->

The Amiga keyboard is an **intelligent keyboard** — it contains its own microprocessor that handles all key scanning and delivers only validated key codes to the main computer. Several keyboard revisions exist across the A500, A1000, and A2000, differing mainly in the number of keys and their physical layout. The diagrams below show the American (QWERTY) and French (AZERTY) layouts with their corresponding raw key codes.

The keyboard transmits exclusively **raw key codes**, not ASCII characters. The operating system translates these codes into ASCII using a keymap table. The raw key code space is organized as follows:

<div class="register-block">
<div class="reg-title">Raw Key Code Ranges</div>
<div class="reg-row"><span class="reg-field">$00–$3F</span><span class="reg-val">Alphanumeric and symbol keys — arranged in keyboard row order</span></div>
<div class="reg-row"><span class="reg-field">$40–$4F</span><span class="reg-val">RETURN, TAB, BACKSPACE, and similar control keys</span></div>
<div class="reg-row"><span class="reg-field">$50–$5F</span><span class="reg-val">Function keys (F1–F10) and HELP</span></div>
<div class="reg-row"><span class="reg-field">$60–$67</span><span class="reg-val">Modifier keys — Left/Right Shift, Left/Right Alt, Left/Right Amiga, Control</span></div>
</div>

The keyboard processor can distinguish between **key down** (pressed) and **key up** (released) states. Key codes occupy 7 bits ($00–$7F), with the 8th bit serving as the **key up/down flag**. When bit 7 is 0, the key is pressed; when bit 7 is 1, the key has been released. The keyboard also supports multiple simultaneous key presses — some music software exploits this capability to play chords.

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Position</span>
<span class="bit-name">Name</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">7</span><span class="bit-dir">MSB</span><span class="bit-name">UP/DN</span><span class="bit-func">0 = key down (pressed), 1 = key up (released)</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir"></span><span class="bit-name">K6</span><span class="bit-func">Key code bit 6</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir"></span><span class="bit-name">K5</span><span class="bit-func">Key code bit 5</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir"></span><span class="bit-name">K4</span><span class="bit-func">Key code bit 4</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir"></span><span class="bit-name">K3</span><span class="bit-func">Key code bit 3</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir"></span><span class="bit-name">K2</span><span class="bit-func">Key code bit 2</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir"></span><span class="bit-name">K1</span><span class="bit-func">Key code bit 1</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">LSB</span><span class="bit-name">K0</span><span class="bit-func">Key code bit 0</span></div>
</div>

### Caps Lock Exception

The Caps Lock key is a special case — the keyboard simulates a toggle switch. Pressing Caps Lock activates it and turns on the LED. No **key up** code is sent until the key is pressed a second time, at which point the key up code is transmitted and the LED turns off. In effect, the up/down flag tracks the lock state rather than the physical key position.

<!-- section: $KBD001 "Keyboard Electronics" -->

### The MOS 6500/1 Microprocessor

The heart of the keyboard is the **MOS 6500/1**, a single-chip microcomputer based on the 6502 CPU core. It integrates everything needed to operate independently:

<div class="register-block">
<div class="reg-title">MOS 6500/1 Internal Resources</div>
<div class="reg-row"><span class="reg-field">CPU</span><span class="reg-val">6502 core</span></div>
<div class="reg-row"><span class="reg-field">ROM</span><span class="reg-val">2 KB — contains the keyboard scanning program</span></div>
<div class="reg-row"><span class="reg-field">RAM</span><span class="reg-val">64 bytes static RAM</span></div>
<div class="reg-row"><span class="reg-field">I/O</span><span class="reg-val">4 bidirectional 8-bit ports (PA, PB, PC, PD)</span></div>
<div class="reg-row"><span class="reg-field">Timer</span><span class="reg-val">16-bit counter with external clock input</span></div>
<div class="reg-row"><span class="reg-field">Clock</span><span class="reg-val">On-chip oscillator — 3 MHz crystal, 1.5 MHz internal clock (divided by 2)</span></div>
</div>

The 6500/1 requires only a 5V supply and a crystal. The Amiga keyboard uses a **3 MHz quartz crystal**, yielding an internal clock frequency of **1.5 MHz**.

A **556 dual timer** IC generates the RESET signal for the 6500/1.

### Key Matrix

The keys are organized into two groups:

**Direct keys:** The seven modifier keys — Left Shift, Right Shift, Left Alt, Right Alt, Left Amiga, Right Amiga, and Control — are connected directly to the lower bits of port PB.

**Matrix keys:** All other keys are arranged in a **6-column by 15-row matrix**. The six columns connect to port PA signals PA2–PA7, configured as inputs. The 15 rows are driven by ports C and D (pin PD7, which would be a 16th row, is disconnected in later revisions).

### Scanning Process

When the 6500/1 scans the keyboard, it activates each row by driving it LOW. Since the port C and D outputs are **open-collector** without internal pull-up resistors, outputs set to 1 are effectively inactive (high-impedance). With a row driven LOW, the processor reads the six column inputs. The column inputs have **internal pull-up resistors**, so an unpressed key reads HIGH.

Each key connects a column to a row at the intersection point. When a key in the active row is pressed, the corresponding column input is pulled LOW. After scanning all rows, the processor knows the complete state of every key. If any state has changed since the previous scan, the corresponding key codes are transmitted to the Amiga.

<!-- section: $KBD001 "Serial Data Transfer" -->

### Physical Connection

The keyboard connects to the Amiga via a **four-wire cable**. Two wires carry the 5V power supply for the keyboard electronics. Data transfer uses the remaining two lines:

<div class="register-block">
<div class="reg-title">Keyboard Signal Lines</div>
<div class="reg-row"><span class="reg-field">KDAT</span><span class="reg-val">Keyboard data line — connected to CIA-A SP (serial port) input</span></div>
<div class="reg-row"><span class="reg-field">KCLK</span><span class="reg-val">Keyboard clock line — connected to CIA-A CNT (counter) input</span></div>
</div>

### Transfer Protocol

Data transfer is **unidirectional** — from the keyboard to the Amiga. The 6500/1 places each data bit on the KDAT line and accompanies it with a LOW clock pulse on KCLK.

**Timing per bit:**
- KCLK LOW pulse duration: **20 microseconds**
- Interval between clock pulses: **40 microseconds**
- Total time per bit: **60 microseconds**
- Time for one complete byte (8 bits): **480 microseconds**
- Effective baud rate: **16,666 baud**

The data signals are **active-low** (LOW = logic 1, HIGH = logic 0).

### Bit Transmission Order

The bits are **not** sent in standard MSB-first order (7-6-5-4-3-2-1-0). Instead, the byte is **rotated left by one position** before transmission, yielding the order: **6-5-4-3-2-1-0-7**. The key up/down flag (bit 7) is always transmitted last.

For example, the raw key code for 'J' is `10100110`. After left rotation, it is transmitted as `01001101`.

This rotation ensures that during synchronization errors, the last bit received is always the up/down flag. Since the keyboard sends all 1s during resynchronization, any garbled byte will appear as a **key up** code — which is harmless. A false key down would be far more disruptive.

### CIA Shift Register Reception

The CIA-A shift register captures one bit on each clock pulse from KCLK. After eight pulses, a complete byte is available and the CIA generates a **level 2 interrupt**. The operating system's interrupt handler then:

1. Reads the serial data register
2. Inverts and rotates the byte right to recover the original key code
3. Sends the handshake pulse
4. Processes the key code

<!-- section: $KBD001 "Handshake Protocol" -->

After transmitting all 8 bits, the keyboard waits for a **handshake** from the Amiga. The Amiga acknowledges by pulling the KDAT line LOW for approximately **75 microseconds**. This signals the keyboard that the byte was received and it may send the next code.

### Synchronization Recovery

Proper data transfer requires that the transmitter and receiver are in sync — the bit positions must align. A loss of synchronization can occur if the Amiga is powered off while the keyboard is sending, or if the keyboard is plugged in while the Amiga is running. The Amiga itself has no way to detect a sync error; this responsibility falls entirely on the keyboard.

After each byte transfer, the keyboard waits a maximum of **145 milliseconds** for the handshake signal. If no handshake arrives within this time, the keyboard assumes a transmission error and enters a special **resynchronization mode**:

1. The keyboard sets KDAT to 1 and sends a single clock pulse
2. It waits another 145 ms for a handshake
3. This is repeated until a handshake is received, confirming synchronization is restored

During resynchronization, the keyboard transmits only 1-bits. Since the last bit received is always the up/down flag (due to the left rotation), any incorrectly received byte will be interpreted as a **key up** event — which is safe to ignore.

<!-- section: $KBD001 "Special Codes" -->

The keyboard communicates several special conditions to the Amiga using reserved key codes in the range $F9–$FE:

<div class="register-block">
<div class="reg-title">Special Keyboard Codes</div>
<div class="reg-row"><span class="reg-field">$F9</span><span class="reg-val"><span class="highlight">Last key code was invalid</span> — sent after resynchronization is complete. The Amiga recognizes that a previous transmission was lost. The keyboard then retransmits the previously lost code.</span></div>
<div class="reg-row"><span class="reg-field">$FA</span><span class="reg-val"><span class="highlight">Keyboard buffer overflow</span> — the keyboard has a 10-character buffer. When full, $FA warns the Amiga that key codes may be lost if the buffer is not drained.</span></div>
<div class="reg-row"><span class="reg-field">$FC</span><span class="reg-val"><span class="highlight">Self-test failure</span> — the keyboard runs a self-test at power-up (visible as a brief flash of the Caps Lock LED). If the test fails, the keyboard sends $FC and locks up with the LED permanently on.</span></div>
<div class="reg-row"><span class="reg-field">$FD</span><span class="reg-val"><span class="highlight">Power-up key stream start</span> — marks the beginning of a sequence of key codes for keys that were held down during power-up.</span></div>
<div class="reg-row"><span class="reg-field">$FE</span><span class="reg-val"><span class="highlight">Power-up key stream end</span> — marks the end of the power-up key sequence. If no keys were held, $FD and $FE are sent back-to-back.</span></div>
</div>

### Power-Up Sequence

When the Amiga is powered on, the keyboard processor performs its self-test (briefly lighting the Caps Lock LED). Once complete, it transmits the codes of any keys held down during power-up, bracketed by **$FD** (start) and **$FE** (end). If no keys were pressed, only $FD followed immediately by $FE is sent.

<!-- section: $KBD001 "Keyboard Reset" -->

The keyboard can trigger a **hardware reset** of the entire Amiga. Pressing both **Amiga keys** and the **Control key** simultaneously causes the keyboard processor to hold the KCLK line LOW for approximately **0.5 seconds**. This generates a RESET signal that forces a complete system reboot. The reset is triggered when any one of the three keys is released.

The Caps Lock LED blinks during the reset sequence as a visual confirmation.

Since the KCLK signal is connected to the CIA-A CNT pin, it is theoretically possible to trigger a reset through software by appropriately programming the CIA — though this is not a recommended practice.

<div class="register-block">
<div class="reg-title">Reset Key Combination</div>
<div class="reg-row"><span class="reg-field">Keys</span><span class="reg-val">Control + Left Amiga + Right Amiga (all three held simultaneously)</span></div>
<div class="reg-row"><span class="reg-field">Signal</span><span class="reg-val">KCLK held LOW for ~500 ms</span></div>
<div class="reg-row"><span class="reg-field">CIA connection</span><span class="reg-val">KCLK → CIA-A CNT pin</span></div>
<div class="reg-row"><span class="reg-field">Effect</span><span class="reg-val"><span class="highlight">Full system reset — triggered on key release</span></span></div>
</div>
