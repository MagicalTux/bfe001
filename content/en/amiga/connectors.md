---
title: "Connectors"
addr: "$CONN01"
description: "The Amiga's external connectors — audio/video, RGB, parallel, serial, floppy, game ports, expansion port, and power supply pinouts."
order: 15
---

<!-- section: $CONN01 "Audio & Video" -->

The audio and video connector arrangement varies across Amiga models. The A500 and A1000 provide composite video via RCA (CINCH) connectors, while the A2000 has no composite video output — only a header for an optional video modulator or Genlock adapter.

### Composite Video

On the A1000, the composite video signal is a full **FBAS** (color composite) signal, compatible with most monitors. On the A500, cost savings resulted in a simpler **BAS** signal (monochrome composite only). Early A1000 models without a French keyboard output NTSC rather than PAL, which appears as black-and-white with interference lines on PAL monitors.

The video output is buffered through a transistor stage with a **75-ohm output impedance**, providing permanent short-circuit protection.

### Stereo Audio

Audio is carried on two RCA connectors on all models:

<div class="register-block">
<div class="reg-title">Audio RCA Connectors</div>
<div class="reg-row"><span class="reg-field">Red</span><span class="reg-val">Right stereo channel</span></div>
<div class="reg-row"><span class="reg-field">White</span><span class="reg-val">Left stereo channel</span></div>
</div>

The outputs have a **1 Kohm output impedance** and can be connected to any standard amplifier (AUX, TAPE, or CD input). They are short-circuit protected with an internal 36-ohm load resistor.

### A1000 TV Modulator Connector

The A1000 has an additional **8-pin DIN** audio/video connector (identical to the C64 modulator connector) intended for an RF modulator to connect a television. It carries both video and audio, plus a +12V supply for the modulator. The audio pins on this connector have a 1 Kohm output impedance but no internal load resistor, resulting in approximately four times the unloaded signal level compared to the RCA outputs.

<div class="register-block">
<div class="reg-title">A1000 DIN-8 TV Modulator Pinout</div>
<div class="reg-row"><span class="reg-field">Pin 1</span><span class="reg-val">— (unused)</span></div>
<div class="reg-row"><span class="reg-field">Pin 2</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">Pin 3</span><span class="reg-val">Left audio channel</span></div>
<div class="reg-row"><span class="reg-field">Pin 4</span><span class="reg-val">Composite video output</span></div>
<div class="reg-row"><span class="reg-field">Pin 5</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">Pin 6</span><span class="reg-val">— (unused)</span></div>
<div class="reg-row"><span class="reg-field">Pin 7</span><span class="reg-val">+12V (modulator power supply)</span></div>
<div class="reg-row"><span class="reg-field">Pin 8</span><span class="reg-val">Right audio channel</span></div>
</div>

<!-- section: $CONN01 "RGB Video" -->

The RGB connector is a **23-pin D-sub** and is identical across all three Amiga models. It supports analog RGB monitors (like the Amiga monitor), digital RGB monitors, and special devices such as Genlock adapters.

<div class="register-block">
<div class="reg-title">RGB Connector Pinout (23-pin D-sub)</div>
<div class="reg-row"><span class="reg-field">Pin 1</span><span class="reg-val">XCLK — External clock input (active when XCLKEN is LOW)</span></div>
<div class="reg-row"><span class="reg-field">Pin 2</span><span class="reg-val">XCLKEN — External clock enable (active LOW)</span></div>
<div class="reg-row"><span class="reg-field">Pin 3</span><span class="reg-val">R — <span class="highlight">Analog Red</span> (75 ohm, transistor-buffered)</span></div>
<div class="reg-row"><span class="reg-field">Pin 4</span><span class="reg-val">G — <span class="highlight">Analog Green</span> (75 ohm, transistor-buffered)</span></div>
<div class="reg-row"><span class="reg-field">Pin 5</span><span class="reg-val">B — <span class="highlight">Analog Blue</span> (75 ohm, transistor-buffered)</span></div>
<div class="reg-row"><span class="reg-field">Pin 6</span><span class="reg-val">DI — Digital Intensity</span></div>
<div class="reg-row"><span class="reg-field">Pin 7</span><span class="reg-val">DB — Digital Blue (TTL level, 47 ohm)</span></div>
<div class="reg-row"><span class="reg-field">Pin 8</span><span class="reg-val">DG — Digital Green (TTL level, 47 ohm)</span></div>
<div class="reg-row"><span class="reg-field">Pin 9</span><span class="reg-val">DR — Digital Red (TTL level, 47 ohm)</span></div>
<div class="reg-row"><span class="reg-field">Pin 10</span><span class="reg-val">CSYNC — <span class="highlight">Composite sync</span> (75 ohm, transistor-buffered)</span></div>
<div class="reg-row"><span class="reg-field">Pin 11</span><span class="reg-val">HSY — Horizontal sync (bidirectional, TTL level, 47 ohm)</span></div>
<div class="reg-row"><span class="reg-field">Pin 12</span><span class="reg-val">VSY — Vertical sync (bidirectional, TTL level, 47 ohm)</span></div>
<div class="reg-row"><span class="reg-field">Pin 13</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">Pin 14</span><span class="reg-val">ZD — Zero detect (background indicator for Genlock)</span></div>
<div class="reg-row"><span class="reg-field">Pin 15</span><span class="reg-val">C1U — 3.58 MHz clock output</span></div>
<div class="reg-row"><span class="reg-field">Pin 16–20</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">Pin 21</span><span class="reg-val">-5V</span></div>
<div class="reg-row"><span class="reg-field">Pin 22</span><span class="reg-val">+12V</span></div>
<div class="reg-row"><span class="reg-field">Pin 23</span><span class="reg-val">+5V</span></div>
</div>

### Analog RGB Signals

For connection to an analog RGB monitor (such as the Commodore 1084), pins 3 (R), 4 (G), 5 (B), and 10 (CSYNC) are used. Denise's digital RGB output is converted to analog through a **4-bit DAC**. The composite sync signal from Agnus is a mix of the vertical and horizontal sync signals. All four outputs have a **75-ohm output impedance** and are transistor-buffered for short-circuit protection.

### Digital RGB Signals

Pins 6–9 provide **digital RGB signals** for digital monitors. These come from Denise's digital RGB output, buffered through a **74HC244**. The color signals are connected to Denise's upper bits (e.g., DB is connected to B3). All four signals have a **47-ohm output impedance** at TTL levels.

### Sync Signals

The HSY and VSY pins are **bidirectional**. In normal operation, they output horizontal and vertical sync from Agnus (TTL level, 47-ohm impedance, directly connected to Agnus pins). Sync signals are **active-low** — normally at 5V, going to 0V during sync pulses.

When Agnus's **Genlock bit** is enabled, these pins switch to input mode. The Amiga then synchronizes its video timing to external sync signals fed in through HSY and VSY — enabling genlocking with an external video source.

### Zero Detect (ZD)

The ZD signal goes LOW whenever the currently displayed pixel is the background color (color register 0). This is used by Genlock adapters to key external video behind the Amiga's display. During vertical blank (VSY=0), ZD carries the **GAUD bits** (Genlock Audio Enable) from register $100 (BPLCON0), used as an audio switch by the Genlock interface.

### External Clock

The **C1U** pin outputs a 3.58 MHz clock identical to the custom chip CLK signal. The **XCLK** and **XCLKEN** pins allow feeding an external master clock into the Amiga. All Amiga clocks derive from a 28 MHz oscillator, which can be replaced by an external clock via XCLK when XCLKEN is driven LOW. Pin 13 (GND) serves as the clock ground reference.

<!-- section: $CONN01 "Parallel Port (Centronics)" -->

The Centronics parallel port uses a **25-pin D-sub** connector. On the A500 and A2000, the pinout is PC-compatible — standard PC parallel printer cables work directly. The A1000 has a different pinout (notably pin 23 carries +5V instead of GND), so connecting a standard cable would cause a short circuit. A custom cable is required for A1000 printer connections.

<div class="register-block">
<div class="reg-title">Centronics Parallel Port Pinout (A500/A2000)</div>
<div class="reg-row"><span class="reg-field">Pin 1</span><span class="reg-val">STROBE — Data valid (output, active LOW, 1.4 µs pulse)</span></div>
<div class="reg-row"><span class="reg-field">Pin 2</span><span class="reg-val">Data bit 0 (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 3</span><span class="reg-val">Data bit 1 (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 4</span><span class="reg-val">Data bit 2 (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 5</span><span class="reg-val">Data bit 3 (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 6</span><span class="reg-val">Data bit 4 (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 7</span><span class="reg-val">Data bit 5 (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 8</span><span class="reg-val">Data bit 6 (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 9</span><span class="reg-val">Data bit 7 (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 10</span><span class="reg-val">/ACK — Acknowledge from printer (input, active LOW, 1 µs pulse)</span></div>
<div class="reg-row"><span class="reg-field">Pin 11</span><span class="reg-val">BUSY — Printer busy (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 12</span><span class="reg-val">POUT — Paper out (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 13</span><span class="reg-val">SELECT — Printer online (bidirectional)</span></div>
<div class="reg-row"><span class="reg-field">Pin 14</span><span class="reg-val">+5V</span></div>
<div class="reg-row"><span class="reg-field">Pin 15</span><span class="reg-val">— (unused)</span></div>
<div class="reg-row"><span class="reg-field">Pin 16</span><span class="reg-val">RESET (output)</span></div>
<div class="reg-row"><span class="reg-field">Pin 17–25</span><span class="reg-val">GND</span></div>
</div>

### CIA-A Connection Mapping

All parallel port signals are routed through CIA-A:

<div class="register-block">
<div class="reg-title">Centronics to CIA-A Signal Mapping</div>
<div class="reg-row"><span class="reg-field">STROBE (pin 1)</span><span class="reg-val">CIA-A pin 18 — PC (handshake output)</span></div>
<div class="reg-row"><span class="reg-field">Data 0–7 (pins 2–9)</span><span class="reg-val">CIA-A PB0–PB7 (pins 10–17)</span></div>
<div class="reg-row"><span class="reg-field">/ACK (pin 10)</span><span class="reg-val">CIA-A PB8 (pin 24)</span></div>
<div class="reg-row"><span class="reg-field">BUSY (pin 11)</span><span class="reg-val">CIA-B PA0 and SP (pins 2, 39)</span></div>
<div class="reg-row"><span class="reg-field">POUT (pin 12)</span><span class="reg-val">CIA-B PA1 and CNT (pins 3, 40)</span></div>
<div class="reg-row"><span class="reg-field">SELECT (pin 13)</span><span class="reg-val">CIA-B PA2 (pin 4)</span></div>
</div>

### Transfer Protocol

When a valid data byte is placed on pins 2–9, the STROBE signal pulses LOW for **1.4 microseconds**, signaling the printer that data is ready. The printer acknowledges by pulsing /ACK LOW for **1 microsecond**. If the printer asserts BUSY, the Amiga waits until BUSY is deasserted before sending the next byte. POUT indicates paper-out, and SELECT indicates whether the printer is online (LOW) or offline (HIGH).

The parallel port is well-suited for expansion hardware such as sound digitizers, since all data pins can be programmed as either inputs or outputs through the CIA direction registers.

<!-- section: $CONN01 "Serial Port (RS-232)" -->

The serial port uses a **25-pin D-sub** connector and provides a full RS-232 interface plus several non-standard signals.

<div class="register-block">
<div class="reg-title">RS-232 Serial Port Pinout (A500/A2000)</div>
<div class="reg-row"><span class="reg-field">Pin 1</span><span class="reg-val">GND — Frame ground (protective earth)</span></div>
<div class="reg-row"><span class="reg-field">Pin 2</span><span class="reg-val">TXD — <span class="highlight">Transmit Data</span> (output, from Paula)</span></div>
<div class="reg-row"><span class="reg-field">Pin 3</span><span class="reg-val">RXD — <span class="highlight">Receive Data</span> (input, to Paula)</span></div>
<div class="reg-row"><span class="reg-field">Pin 4</span><span class="reg-val">RTS — Request To Send (output)</span></div>
<div class="reg-row"><span class="reg-field">Pin 5</span><span class="reg-val">CTS — Clear To Send (input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 6</span><span class="reg-val">DSR — Data Set Ready (input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 7</span><span class="reg-val">GND — Signal ground</span></div>
<div class="reg-row"><span class="reg-field">Pin 8</span><span class="reg-val">CD — Carrier Detect (input, for modems)</span></div>
<div class="reg-row"><span class="reg-field">Pin 9</span><span class="reg-val">+12V</span></div>
<div class="reg-row"><span class="reg-field">Pin 10</span><span class="reg-val">-12V</span></div>
<div class="reg-row"><span class="reg-field">Pin 11</span><span class="reg-val">AUDOUT — Left audio channel output (1 Kohm impedance)</span></div>
<div class="reg-row"><span class="reg-field">Pin 12–17</span><span class="reg-val">— (unused)</span></div>
<div class="reg-row"><span class="reg-field">Pin 18</span><span class="reg-val">AUDIN — Audio input (47 ohm, connected to Paula AUDR)</span></div>
<div class="reg-row"><span class="reg-field">Pin 19</span><span class="reg-val">— (unused)</span></div>
<div class="reg-row"><span class="reg-field">Pin 20</span><span class="reg-val">DTR — Data Terminal Ready (output)</span></div>
<div class="reg-row"><span class="reg-field">Pin 21</span><span class="reg-val">RI — Ring Indicator (input, linked to Centronics SELECT via transistor)</span></div>
<div class="reg-row"><span class="reg-field">Pin 22–25</span><span class="reg-val">— (unused)</span></div>
</div>

### RS-232 Signal Routing

The data lines TXD and RXD connect directly to Paula's serial data pins. The five handshaking signals are routed through **CIA-B port A**:

<div class="register-block">
<div class="reg-title">RS-232 Handshake to CIA-B Mapping</div>
<div class="reg-row"><span class="reg-field">DSR</span><span class="reg-val">CIA-B PA3</span></div>
<div class="reg-row"><span class="reg-field">CTS</span><span class="reg-val">CIA-B PA4</span></div>
<div class="reg-row"><span class="reg-field">CD</span><span class="reg-val">CIA-B PA5</span></div>
<div class="reg-row"><span class="reg-field">RTS</span><span class="reg-val">CIA-B PA6</span></div>
<div class="reg-row"><span class="reg-field">DTR</span><span class="reg-val">CIA-B PA7</span></div>
</div>

RS-232 signals are not connected directly to the CIAs — they pass through **level converters** (1488 output drivers, 1489A input receivers) that translate between TTL and RS-232 voltage levels. The output drivers use +12V to -5V swing. Input receivers accept -12V to +0.5V as LOW, and 3V to 25V as HIGH.

RS-232 conventions require handshake signals to be active HIGH, while TXD and RXD use negative logic for mark (1). Since the output drivers invert the signal, the corresponding CIA-B port bits are **active-low** — a 0 in the CIA register produces a HIGH on the RS-232 line.

### Audio Pass-through

The AUDOUT pin carries the left audio channel with a 1 Kohm output impedance. The AUDIN pin (47-ohm impedance) feeds directly into Paula's right audio input (AUDR). An external audio signal entering through AUDIN passes through Paula's low-pass filter and emerges from the right audio RCA output.

### A1000 Differences

The A1000 serial port has additional non-standard signals:

<div class="register-block">
<div class="reg-title">A1000-Specific Serial Port Pins</div>
<div class="reg-row"><span class="reg-field">Pin 14</span><span class="reg-val">-5V</span></div>
<div class="reg-row"><span class="reg-field">Pin 15</span><span class="reg-val">AUDOUT (audio output, replaces pin 11)</span></div>
<div class="reg-row"><span class="reg-field">Pin 16</span><span class="reg-val">AUDIN (audio input, replaces pin 18)</span></div>
<div class="reg-row"><span class="reg-field">Pin 17</span><span class="reg-val">E — 716 KHz bus clock (buffered from 68000)</span></div>
<div class="reg-row"><span class="reg-field">Pin 18</span><span class="reg-val">/INT2 — Level 2 interrupt input (connected to Paula)</span></div>
<div class="reg-row"><span class="reg-field">Pin 21</span><span class="reg-val">+5V</span></div>
<div class="reg-row"><span class="reg-field">Pin 23</span><span class="reg-val">+12V</span></div>
<div class="reg-row"><span class="reg-field">Pin 24</span><span class="reg-val">MCLK — 3.58 MHz master clock</span></div>
<div class="reg-row"><span class="reg-field">Pin 25</span><span class="reg-val">/RESET</span></div>
</div>

<!-- section: $CONN01 "External Floppy Drive" -->

The external floppy connector is a **23-pin D-sub** and uses the Shugart bus standard, supporting up to three external drives (DF1–DF3) in addition to the internal drive (DF0).

<div class="register-block">
<div class="reg-title">External Floppy Connector Pinout (23-pin D-sub)</div>
<div class="reg-row"><span class="reg-field">Pin 1</span><span class="reg-val">/RDY — Drive ready (input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 2</span><span class="reg-val">/DKRD — Disk read data (input, connected to Paula)</span></div>
<div class="reg-row"><span class="reg-field">Pin 3–7</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">Pin 8</span><span class="reg-val">/MTRX — Motor control (output, active LOW)</span></div>
<div class="reg-row"><span class="reg-field">Pin 9</span><span class="reg-val">/SEL2 — Drive DF2 select (output)</span></div>
<div class="reg-row"><span class="reg-field">Pin 10</span><span class="reg-val">/DRES — Drive reset (output, connected to system RESET)</span></div>
<div class="reg-row"><span class="reg-field">Pin 11</span><span class="reg-val">/CHNG — Disk changed (input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 12</span><span class="reg-val">+5V</span></div>
<div class="reg-row"><span class="reg-field">Pin 13</span><span class="reg-val">/SIDE — Side select (output: HIGH = side 0, LOW = side 1)</span></div>
<div class="reg-row"><span class="reg-field">Pin 14</span><span class="reg-val">/WPRO — Write protect (input: LOW = protected)</span></div>
<div class="reg-row"><span class="reg-field">Pin 15</span><span class="reg-val">/TK0 — Track 0 indicator (input: LOW = head on track 0)</span></div>
<div class="reg-row"><span class="reg-field">Pin 16</span><span class="reg-val">/DKWE — Disk write enable (output: LOW = write mode)</span></div>
<div class="reg-row"><span class="reg-field">Pin 17</span><span class="reg-val">/DKWD — Disk write data (output, from Paula)</span></div>
<div class="reg-row"><span class="reg-field">Pin 18</span><span class="reg-val">/STEP — Head step pulse (output)</span></div>
<div class="reg-row"><span class="reg-field">Pin 19</span><span class="reg-val">/DIR — Head direction (output: LOW = inward, HIGH = outward)</span></div>
<div class="reg-row"><span class="reg-field">Pin 20</span><span class="reg-val">/SEL3 — Drive DF3 select (output)</span></div>
<div class="reg-row"><span class="reg-field">Pin 21</span><span class="reg-val">/SEL1 — Drive DF1 select (output)</span></div>
<div class="reg-row"><span class="reg-field">Pin 22</span><span class="reg-val">/INDEX — Index pulse (input, once per disk revolution)</span></div>
<div class="reg-row"><span class="reg-field">Pin 23</span><span class="reg-val">+12V</span></div>
</div>

### Motor Control via Flip-Flop

The MTRX signal does not directly control individual drive motors. Instead, each drive has a **flip-flop** that latches the MTRX state when its SEL line goes LOW. This allows independent motor control for each drive. For example, to start the internal drive's motor: set MTRX to 0, then pulse SEL0 LOW. The flip-flop captures the motor state; subsequently changing MTRX does not affect that drive until SEL0 is pulsed again.

The internal drive's flip-flop is on the motherboard. External drives require their own flip-flop circuit — typically built with a **74LS74** and a **74LS38** NAND gate.

### Drive Identification

When the motor is off and a drive is selected, a special **32-bit identification** mode is available. To initiate identification, the motor signal is briefly toggled to reset the drive's shift register. Then, each data bit is read by cycling SEL LOW and reading the RDY signal. This is repeated 32 times (MSB first). Since signals are active-low, the bits must be inverted.

<div class="register-block">
<div class="reg-title">Drive Identification Codes</div>
<div class="reg-row"><span class="reg-field">$00000000</span><span class="reg-val">No drive connected</span></div>
<div class="reg-row"><span class="reg-field">$FFFFFFFF</span><span class="reg-val"><span class="highlight">Standard 3.5-inch drive</span></span></div>
<div class="reg-row"><span class="reg-field">$55555555</span><span class="reg-val">5.25-inch drive, 2x40 tracks</span></div>
</div>

In practice, only the first two bits need to be read: 00 = no drive, 11 = 3.5-inch, 01 = 5.25-inch.

### CIA Connections

The four input signals (/CHNG, /WPRO, /TK0, /RDY) connect to **CIA-A PA4–PA7**. The eight output signals (/STEP, /DIR, /SIDE, /SEL0–/SEL3, /MTR) come from **CIA-B PB0–PB7**. Read/write data signals (/DKRD, /DKWD, /DKWE) connect directly to Paula. All signals use **open-collector drivers** (7407 type) and are active-low.

<!-- section: $CONN01 "Game Ports" -->

The two game ports are **9-pin D-sub** connectors (DB-9) that accept mice, joysticks, trackballs, paddles, and light pens. **Gameport 0** is on the left, **Gameport 1** on the right. They are structurally identical except that Gameport 0 also connects to Agnus's light pen (LP) input.

<div class="register-block">
<div class="reg-title">Game Port Pinout (9-pin D-sub)</div>
<div class="reg-row"><span class="reg-field">Pin 1</span><span class="reg-val">Forward / Mouse V-pulse (input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 2</span><span class="reg-val">Back / Mouse H-pulse (input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 3</span><span class="reg-val">Left / Mouse VQ-pulse / Paddle left button (input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 4</span><span class="reg-val">Right / Mouse HQ-pulse / Paddle right button (input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 5</span><span class="reg-val">Paddle right pot / Mouse button 3 (I/O)</span></div>
<div class="reg-row"><span class="reg-field">Pin 6</span><span class="reg-val">Fire button / Mouse button 1 / Light pen trigger (I/O)</span></div>
<div class="reg-row"><span class="reg-field">Pin 7</span><span class="reg-val">+5V (current-limited on A1000, direct on A500/A2000)</span></div>
<div class="reg-row"><span class="reg-field">Pin 8</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">Pin 9</span><span class="reg-val">Paddle left pot / Mouse button 2 (I/O)</span></div>
</div>

### Internal Chip Connections

<div class="register-block">
<div class="reg-title">Gameport 0 Signal Routing</div>
<div class="reg-row"><span class="reg-field">Pins 1–4</span><span class="reg-val">Denise — M0V, M0H, M1V, M1H (via multiplexer)</span></div>
<div class="reg-row"><span class="reg-field">Pin 5</span><span class="reg-val">Paula — P0Y (analog potentiometer input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 6</span><span class="reg-val">CIA-A PA6 and Agnus LP (light pen input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 9</span><span class="reg-val">Paula — P0X (analog potentiometer input)</span></div>
</div>

<div class="register-block">
<div class="reg-title">Gameport 1 Signal Routing</div>
<div class="reg-row"><span class="reg-field">Pins 1–4</span><span class="reg-val">Denise — M0V, M0H, M1V, M1H (via multiplexer)</span></div>
<div class="reg-row"><span class="reg-field">Pin 5</span><span class="reg-val">Paula — P1Y (analog potentiometer input)</span></div>
<div class="reg-row"><span class="reg-field">Pin 6</span><span class="reg-val">CIA-A PA7</span></div>
<div class="reg-row"><span class="reg-field">Pin 9</span><span class="reg-val">Paula — P1X (analog potentiometer input)</span></div>
</div>

All button and directional signals are **active-low** — internal switches connect the input pin to GND when pressed (HIGH = open, LOW = closed). The analog inputs (P0X, P0Y, P1X, P1Y) accept **470 Kohm variable resistors** (potentiometers) connected between +5V and the input pin.

### Power Protection

On the A1000, the +5V supply to both game ports is protected by a **current limiter** that separates continuous short-circuit current (400 mA) from peak surges (700 mA). Total current draw across both ports must not exceed **250 mA**. This protection was removed on the A500 and A2000 models.

<!-- section: $CONN01 "Expansion Port" -->

The expansion port is an **86-pin edge connector** that exposes all important bus and control signals from the 68000 processor. It allows connection of memory expansions, accelerator boards, and other peripherals. On the A1000, it is located near the game ports behind a plastic cover. On the A500, it is on the underside of the unit. Pin spacing is 0.1 inch (2.54 mm).

The A2000 has a different arrangement: one 86-pin connector plus five 100-pin **Zorro bus** slots for expansion cards.

<div class="register-block">
<div class="reg-title">Expansion Port Pinout (86-pin, selected signals)</div>
<div class="reg-row"><span class="reg-field">1–4</span><span class="reg-val">GND</span></div>
<div class="reg-row"><span class="reg-field">5–6</span><span class="reg-val">+5V</span></div>
<div class="reg-row"><span class="reg-field">8</span><span class="reg-val">-5V</span></div>
<div class="reg-row"><span class="reg-field">9</span><span class="reg-val">Extension (28 MHz on A2000)</span></div>
<div class="reg-row"><span class="reg-field">10</span><span class="reg-val">+12V</span></div>
<div class="reg-row"><span class="reg-field">14</span><span class="reg-val">/C3 — 3.58 MHz clock phase</span></div>
<div class="reg-row"><span class="reg-field">15</span><span class="reg-val">CDAC — 7.16 MHz clock</span></div>
<div class="reg-row"><span class="reg-field">16</span><span class="reg-val">/C1 — 3.58 MHz clock phase</span></div>
<div class="reg-row"><span class="reg-field">17</span><span class="reg-val">/OVR — Override (auto-configuration)</span></div>
<div class="reg-row"><span class="reg-field">18</span><span class="reg-val">XRDY — Expansion ready</span></div>
<div class="reg-row"><span class="reg-field">19</span><span class="reg-val">/INT2 — Level 2 interrupt (to Paula)</span></div>
<div class="reg-row"><span class="reg-field">22</span><span class="reg-val">/INT6 — Level 6 interrupt (to Paula)</span></div>
<div class="reg-row"><span class="reg-field">21–59</span><span class="reg-val"><span class="highlight">A1–A23 — 68000 address bus</span></span></div>
<div class="reg-row"><span class="reg-field">63–86</span><span class="reg-val"><span class="highlight">PD0–PD15 — 68000 data bus</span></span></div>
<div class="reg-row"><span class="reg-field">40–44</span><span class="reg-val">/IPL0–/IPL2 — Interrupt priority level</span></div>
<div class="reg-row"><span class="reg-field">31–35</span><span class="reg-val">FC0–FC2 — 68000 function codes</span></div>
<div class="reg-row"><span class="reg-field">74</span><span class="reg-val">/AS — Address strobe</span></div>
<div class="reg-row"><span class="reg-field">72</span><span class="reg-val">/UDS — Upper data strobe</span></div>
<div class="reg-row"><span class="reg-field">70</span><span class="reg-val">/LDS — Lower data strobe</span></div>
<div class="reg-row"><span class="reg-field">68</span><span class="reg-val">/R/W — Read/Write</span></div>
<div class="reg-row"><span class="reg-field">66</span><span class="reg-val">/DTACK — Data acknowledge</span></div>
<div class="reg-row"><span class="reg-field">53</span><span class="reg-val">/RES — System reset</span></div>
<div class="reg-row"><span class="reg-field">55</span><span class="reg-val">/HLT — Halt</span></div>
<div class="reg-row"><span class="reg-field">46</span><span class="reg-val">/BERR — Bus error</span></div>
<div class="reg-row"><span class="reg-field">60</span><span class="reg-val">/BR — Bus request</span></div>
<div class="reg-row"><span class="reg-field">64</span><span class="reg-val">/BG — Bus grant</span></div>
<div class="reg-row"><span class="reg-field">62</span><span class="reg-val">/BGACK — Bus grant acknowledge</span></div>
<div class="reg-row"><span class="reg-field">48</span><span class="reg-val">/VPA — Valid peripheral address</span></div>
<div class="reg-row"><span class="reg-field">50</span><span class="reg-val">E — 68000 E clock (716 KHz)</span></div>
<div class="reg-row"><span class="reg-field">51</span><span class="reg-val">/VMA — Valid memory address</span></div>
</div>

### Clock Signals

The expansion port provides several clock references. CDAC runs at 7.16 MHz. /C1 and /C3 are 3.58 MHz signals with different phases. On the A2000, the 28.64 MHz master oscillator is also available (pin 9). The relationship between these clocks and the internal 7M, CCK, and CCKQ signals can be derived from their phase diagrams.

### Configuration Signals

/OVR, XRDY, and PALOPE (A500/A1000 only) serve the **auto-configuration** protocol for expansion cards. Pins marked "extension" are reserved for future use. On the A2000, some have already been allocated (e.g., the 28 MHz clock).

<!-- section: $CONN01 "Power Supply" -->

Each Amiga connector carries one or more supply voltages. The total current budget depends on the model and how many ports are loaded simultaneously.

<div class="register-block">
<div class="reg-title">A1000 Maximum Current Per Connector (Commodore-recommended)</div>
<div class="reg-row"><span class="reg-field">RGB</span><span class="reg-val">+5V: 300 mA | +12V: 175 mA | -5V: 50 mA</span></div>
<div class="reg-row"><span class="reg-field">RS-232</span><span class="reg-val">+5V: 100 mA | +12V: 50 mA | -5V: 50 mA</span></div>
<div class="reg-row"><span class="reg-field">Ext. Floppy</span><span class="reg-val">+5V: 270 mA | +12V: 160 mA</span></div>
<div class="reg-row"><span class="reg-field">Centronics</span><span class="reg-val">+5V: 100 mA</span></div>
<div class="reg-row"><span class="reg-field">Expansion</span><span class="reg-val"><span class="highlight">+5V: 1000 mA</span> | +12V: 50 mA | -5V: 50 mA</span></div>
<div class="reg-row"><span class="reg-field">TV Modulator</span><span class="reg-val">+12V: 60 mA</span></div>
<div class="reg-row"><span class="reg-field">Gameport 0</span><span class="reg-val">+5V: 125 mA</span></div>
<div class="reg-row"><span class="reg-field">Gameport 1</span><span class="reg-val">+5V: 125 mA</span></div>
</div>

These values assume **all connectors are loaded simultaneously**. If some connectors are unused, their current budget is available to others. The A1000 power supply can deliver over 8A during a short circuit, so experimentation should be done with caution.

The **A500** has a smaller power supply — external power is recommended for current-hungry expansions. The **A2000** has a larger supply to support multiple expansion cards, including IBM emulation boards.

Note: the A500 provides **-12V** on its negative rail, while the A1000 provides **-5V**.
