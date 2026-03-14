---
title: "CIA 8520"
addr: "$BFE001"
description: "The two MOS 8520 Complex Interface Adapters — parallel ports, timers, event counter, serial shift register, and interrupt control."
order: 2
---

<!-- section: $BFE001 "CIA Overview" -->

The Amiga uses two **MOS 8520** Complex Interface Adapters (CIAs) for peripheral I/O that does not require the speed of custom chip DMA. The 8520 is a derivative of the well-known 6526 used in the Commodore 64, differing only in registers 8–11 (the event counter replaces the 6526's TOD clock).

Each 8520 provides:

- Two 8-bit programmable parallel ports (PA and PB)
- Two 16-bit countdown timers (Timer A and Timer B)
- A 24-bit event counter with alarm function
- A bidirectional serial shift register
- An interrupt control register (ICR) with 5 interrupt sources

The 8520 was designed for 8-bit 6502-family processors and communicates with the 68000 synchronously, clocked by the 68000's E clock at approximately 716 kHz.

### The Two CIAs

<div class="register-block">
<div class="reg-title">CIA Base Addresses</div>
<div class="reg-row"><span class="reg-field"><span class="highlight">$BFE001</span></span><span class="reg-val">CIA-A — connected to data bus D0–D7 (odd addresses, 256-byte spacing)</span></div>
<div class="reg-row"><span class="reg-field">$BFD000</span><span class="reg-val">CIA-B — connected to data bus D8–D15 (even addresses, 256-byte spacing)</span></div>
</div>

The address <span class="highlight">$BFE001</span> — the base of CIA-A — is where this site gets its name. It is the first address the system reads to check the keyboard, disk status, joystick fire buttons, and the power LED state. For any Amiga programmer, this address is instantly recognizable.

CIA-A sits on the lower byte of the data bus (D0–D7), so all its registers appear at odd addresses. CIA-B sits on the upper byte (D8–D15), so its registers appear at even addresses. The chip select signals are derived from address lines A12 (CIA-A, active low) and A13 (CIA-B, active low), with registers spaced 256 bytes apart ($100) using address lines A8–A11 as the internal register select (A0–A3 on the chip).

### Interrupt Routing

- **CIA-A** IRQ connects to PAULA's **INT2** (PORTS, level 2 interrupt)
- **CIA-B** IRQ connects to PAULA's **INT6** (EXTER, level 6 interrupt)

<!-- section: $BFE001 "Register Map" -->

Each CIA has 16 registers (register 11/$B is unused). Due to the 256-byte spacing, the full register maps span a significant address range.

<div class="register-block">
<div class="reg-title">CIA-A Register Addresses</div>
<div class="reg-row"><span class="reg-field"><span class="highlight">$BFE001</span></span><span class="reg-val">PRA — Port A data register</span></div>
<div class="reg-row"><span class="reg-field">$BFE101</span><span class="reg-val">PRB — Port B data register</span></div>
<div class="reg-row"><span class="reg-field">$BFE201</span><span class="reg-val">DDRA — Port A data direction register</span></div>
<div class="reg-row"><span class="reg-field">$BFE301</span><span class="reg-val">DDRB — Port B data direction register</span></div>
<div class="reg-row"><span class="reg-field">$BFE401</span><span class="reg-val">TALO — Timer A low byte</span></div>
<div class="reg-row"><span class="reg-field">$BFE501</span><span class="reg-val">TAHI — Timer A high byte</span></div>
<div class="reg-row"><span class="reg-field">$BFE601</span><span class="reg-val">TBLO — Timer B low byte</span></div>
<div class="reg-row"><span class="reg-field">$BFE701</span><span class="reg-val">TBHI — Timer B high byte</span></div>
<div class="reg-row"><span class="reg-field">$BFE801</span><span class="reg-val">EVENT LO — Event counter bits 0–7</span></div>
<div class="reg-row"><span class="reg-field">$BFE901</span><span class="reg-val">EVENT MID — Event counter bits 8–15</span></div>
<div class="reg-row"><span class="reg-field">$BFEA01</span><span class="reg-val">EVENT HI — Event counter bits 16–23</span></div>
<div class="reg-row"><span class="reg-field">$BFEB01</span><span class="reg-val">— (unused)</span></div>
<div class="reg-row"><span class="reg-field">$BFEC01</span><span class="reg-val">SP — Serial data register (keyboard)</span></div>
<div class="reg-row"><span class="reg-field">$BFED01</span><span class="reg-val">ICR — Interrupt control register</span></div>
<div class="reg-row"><span class="reg-field">$BFEE01</span><span class="reg-val">CRA — Control register A</span></div>
<div class="reg-row"><span class="reg-field">$BFEF01</span><span class="reg-val">CRB — Control register B</span></div>
</div>

<div class="register-block">
<div class="reg-title">CIA-B Register Addresses</div>
<div class="reg-row"><span class="reg-field">$BFD000</span><span class="reg-val">PRA — Port A data register</span></div>
<div class="reg-row"><span class="reg-field">$BFD100</span><span class="reg-val">PRB — Port B data register</span></div>
<div class="reg-row"><span class="reg-field">$BFD200</span><span class="reg-val">DDRA — Port A data direction register</span></div>
<div class="reg-row"><span class="reg-field">$BFD300</span><span class="reg-val">DDRB — Port B data direction register</span></div>
<div class="reg-row"><span class="reg-field">$BFD400</span><span class="reg-val">TALO — Timer A low byte</span></div>
<div class="reg-row"><span class="reg-field">$BFD500</span><span class="reg-val">TAHI — Timer A high byte</span></div>
<div class="reg-row"><span class="reg-field">$BFD600</span><span class="reg-val">TBLO — Timer B low byte</span></div>
<div class="reg-row"><span class="reg-field">$BFD700</span><span class="reg-val">TBHI — Timer B high byte</span></div>
<div class="reg-row"><span class="reg-field">$BFD800</span><span class="reg-val">EVENT LO — Event counter bits 0–7</span></div>
<div class="reg-row"><span class="reg-field">$BFD900</span><span class="reg-val">EVENT MID — Event counter bits 8–15</span></div>
<div class="reg-row"><span class="reg-field">$BFDA00</span><span class="reg-val">EVENT HI — Event counter bits 16–23</span></div>
<div class="reg-row"><span class="reg-field">$BFDB00</span><span class="reg-val">— (unused)</span></div>
<div class="reg-row"><span class="reg-field">$BFDC00</span><span class="reg-val">SP — Serial data register</span></div>
<div class="reg-row"><span class="reg-field">$BFDD00</span><span class="reg-val">ICR — Interrupt control register</span></div>
<div class="reg-row"><span class="reg-field">$BFDE00</span><span class="reg-val">CRA — Control register A</span></div>
<div class="reg-row"><span class="reg-field">$BFDF00</span><span class="reg-val">CRB — Control register B</span></div>
</div>

<!-- section: $BFE001 "Parallel Ports" -->

Each CIA has two 8-bit bidirectional parallel ports, PA and PB. The direction of each individual bit is controlled by the corresponding Data Direction Register (DDR): a 0 bit configures the pin as **input**, a 1 bit configures it as **output**. Reading a port register always returns the actual state of the pins, regardless of direction setting.

### CIA-A Port A (<span class="highlight">$BFE001</span>)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Dir</span>
<span class="bit-name">Signal</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">IN</span><span class="bit-name">/FIR1</span><span class="bit-func">Joystick port 1, fire button (active low)</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">IN</span><span class="bit-name">/FIR0</span><span class="bit-func">Joystick port 0, fire button (active low)</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">IN</span><span class="bit-name">/RDY</span><span class="bit-func">Disk drive ready (active low)</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">IN</span><span class="bit-name">/TK0</span><span class="bit-func">Disk head at track 0 (active low)</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">IN</span><span class="bit-name">/WPRO</span><span class="bit-func">Disk write-protected (active low)</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">IN</span><span class="bit-name">/CHNG</span><span class="bit-func">Disk changed (active low)</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">1</span><span class="bit-dir">OUT</span><span class="bit-name">/LED</span><span class="bit-func">Power LED control (0 = LED on)</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">OUT</span><span class="bit-name">/OVL</span><span class="bit-func">Memory overlay (ROM at $000000 when set)</span></div>
</div>

The DDRA default is $03 (bits 0–1 output, bits 2–7 input). The /OVL bit controls whether Kickstart ROM is mapped at address $000000 (overlay active) or not, critical during the boot sequence. The /LED bit directly controls the power LED — the familiar disk activity blink is simply software toggling this bit.

### CIA-A Port B ($BFE101)

Port B of CIA-A is connected to the **parallel (Centronics) port**. All 8 bits carry data for printer communication. The handshaking signals PC (/DRDY — data ready) and FLAG (/ACK — acknowledge) coordinate the transfer.

### CIA-B Port A ($BFD000)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Dir</span>
<span class="bit-name">Signal</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">OUT</span><span class="bit-name">/DTR</span><span class="bit-func">Serial port DTR output</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">OUT</span><span class="bit-name">/RTS</span><span class="bit-func">Serial port RTS output</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">IN</span><span class="bit-name">/CD</span><span class="bit-func">Serial port carrier detect</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">IN</span><span class="bit-name">/CTS</span><span class="bit-func">Serial port clear to send</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">IN</span><span class="bit-name">/DSR</span><span class="bit-func">Serial port data set ready</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">OUT</span><span class="bit-name">SEL</span><span class="bit-func">Parallel port SELECT</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">OUT</span><span class="bit-name">POUT</span><span class="bit-func">Parallel port paper out</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">IN</span><span class="bit-name">BUSY</span><span class="bit-func">Parallel port busy</span></div>
</div>

DDRA default: $C0 (bits 6–7 output, rest input).

### CIA-B Port B ($BFD100)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Dir</span>
<span class="bit-name">Signal</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">OUT</span><span class="bit-name">/MTR</span><span class="bit-func">Disk drive motor control</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">OUT</span><span class="bit-name">/SEL3</span><span class="bit-func">Select drive DF3:</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">OUT</span><span class="bit-name">/SEL2</span><span class="bit-func">Select drive DF2:</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">OUT</span><span class="bit-name">/SEL1</span><span class="bit-func">Select drive DF1:</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">OUT</span><span class="bit-name">/SEL0</span><span class="bit-func">Select internal drive DF0:</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">OUT</span><span class="bit-name">/SIDE</span><span class="bit-func">Disk side select (0 = upper, 1 = lower)</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">OUT</span><span class="bit-name">DIR</span><span class="bit-func">Disk head step direction (0 = outward, 1 = inward)</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">OUT</span><span class="bit-name">/STEP</span><span class="bit-func">Disk head step pulse (active low)</span></div>
</div>

DDRB default: $FF (all outputs). This port controls the entire floppy disk drive mechanism.

### Handshaking (PC and FLAG)

Data transfers on port B can be coordinated using two handshaking signals. The **PC** (port control) pin goes low on the third clock cycle after any access to port B, signaling that data has been written. The **FLAG** pin triggers when its input transitions from high to low, setting the FLAG bit in the ICR. Connecting PC of one CIA to FLAG of another creates an efficient hardware handshake.

On CIA-A: SP = KDAT (keyboard serial data), CNT = KCLK (keyboard clock), PC = /DRDY, FLAG = /ACK.
On CIA-B: FLAG = /INDEX (disk index pulse), PC is unused.

<!-- section: $BFE001 "Timers" -->

Each CIA contains two independent 16-bit countdown timers. A timer counts down from a preset value to zero, at which point it sets a flag in the ICR and optionally generates an interrupt.

### Timer Registers

Each timer uses two 8-bit registers. When **read**, they return the current counter value. When **written**, the value is stored in a latch (prescaler) — it is not loaded into the counter immediately.

<div class="register-block">
<div class="reg-title">Timer Register Addresses (CIA-A)</div>
<div class="reg-row"><span class="reg-field">$BFE401</span><span class="reg-val">TALO — Timer A, low byte (read: counter; write: latch)</span></div>
<div class="reg-row"><span class="reg-field">$BFE501</span><span class="reg-val">TAHI — Timer A, high byte (read: counter; write: latch)</span></div>
<div class="reg-row"><span class="reg-field">$BFE601</span><span class="reg-val">TBLO — Timer B, low byte (read: counter; write: latch)</span></div>
<div class="reg-row"><span class="reg-field">$BFE701</span><span class="reg-val">TBHI — Timer B, high byte (read: counter; write: latch)</span></div>
</div>

### Reading the Counter Safely

Because the timer decrements asynchronously, reading the high and low bytes separately can produce a race condition. For example, if the counter is at $0100, reading the high byte gives $01, but if the counter decrements before the low byte is read, the low byte returns $FF — yielding an incorrect value of $01FF. The safe method: read high, read low, read high again. If the high byte changed, repeat the process.

### Clock Sources

**Timer A** has two input modes controlled by the INMODE bit in CRA:

- INMODE = 0: decrement on every clock cycle (~716 kHz from the E clock)
- INMODE = 1: decrement on each positive edge of the CNT signal

**Timer B** has four input modes controlled by bits 6–5 of CRB:

- 00: clock cycle (~716 kHz)
- 01: CNT signal positive edges
- 10: Timer A underflow (chains A+B into a 32-bit timer)
- 11: Timer A underflow gated by CNT high (measures CNT pulse duration)

### Run Modes

The **RUNMODE** bit selects between:

- **Continuous mode** (RUNMODE = 0): after reaching zero, the counter reloads from the latch and continues counting
- **One-shot mode** (RUNMODE = 1): after reaching zero, the counter stops and the START bit is cleared

### Loading the Counter

The latch value is transferred into the counter in three ways:

1. **Force load:** setting the LOAD bit (strobe) in the control register immediately loads the latch value
2. **Underflow:** the latch is automatically reloaded when the counter reaches zero
3. **Auto-load on stop:** writing to the timer high byte while the timer is stopped triggers an automatic load

When writing timer values, always write the **high byte first**, then the **low byte**, to avoid triggering unintended auto-loads.

### Timer Output to Port B

Each timer can optionally output to a port B pin: Timer A to PB6, Timer B to PB7. This is enabled by the PBON bit in the control register. The OUTMODE bit selects:

- **Pulse mode** (OUTMODE = 0): a one-cycle positive pulse on each underflow
- **Toggle mode** (OUTMODE = 1): the output toggles on each underflow

<!-- section: $BFE001 "Control Registers" -->

### Control Register A (CRA, register $E)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Values</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">TODIN</span><span class="bit-name">0=60Hz, 1=50Hz</span><span class="bit-func">TOD input frequency (event counter clock select)</span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">SPMODE</span><span class="bit-name">0=input, 1=output</span><span class="bit-func">Serial port direction</span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">INMODE</span><span class="bit-name">0=clock, 1=CNT</span><span class="bit-func">Timer A clock source</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">4</span><span class="bit-dir">LOAD</span><span class="bit-name">strobe</span><span class="bit-func">Force load latch into counter (write 1 to trigger)</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">RUNMODE</span><span class="bit-name">0=continuous, 1=one-shot</span><span class="bit-func">Timer A run mode</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">OUTMODE</span><span class="bit-name">0=pulse, 1=toggle</span><span class="bit-func">Timer A output waveform on PB6</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">PBON</span><span class="bit-name">0=off, 1=on</span><span class="bit-func">Enable Timer A output on PB6</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">START</span><span class="bit-name">0=stop, 1=start</span><span class="bit-func">Start/stop Timer A</span></div>
</div>

### Control Register B (CRB, register $F)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Values</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">ALARM</span><span class="bit-name">0=TOD write, 1=alarm write</span><span class="bit-func">Event counter access mode: write counter or alarm</span></div>
<div class="bit-row"><span class="bit-num">6–5</span><span class="bit-dir">INMODE</span><span class="bit-name">00/01/10/11</span><span class="bit-func">Timer B clock source (see above)</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">4</span><span class="bit-dir">LOAD</span><span class="bit-name">strobe</span><span class="bit-func">Force load latch into counter (write 1 to trigger)</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">RUNMODE</span><span class="bit-name">0=continuous, 1=one-shot</span><span class="bit-func">Timer B run mode</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">OUTMODE</span><span class="bit-name">0=pulse, 1=toggle</span><span class="bit-func">Timer B output waveform on PB7</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">PBON</span><span class="bit-name">0=off, 1=on</span><span class="bit-func">Enable Timer B output on PB7</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">START</span><span class="bit-name">0=stop, 1=start</span><span class="bit-func">Start/stop Timer B</span></div>
</div>

### Timer Usage on the Amiga

- **CIA-A Timer A** is used continuously by the OS for keyboard communication
- **CIA-A Timer B** is available for general use
- **CIA-B Timer A** is used for serial port baud rate generation
- **CIA-B Timer B** is used by the Blitter in synchronous mode

<!-- section: $BFE001 "Event Counter & Serial Port" -->

### Event Counter (Registers $8–$A)

The 8520 replaces the 6526's TOD (Time of Day) clock with a simple 24-bit event counter. It counts from 0 to 16,777,215 ($FFFFFF), incrementing on each positive edge of the TOD input signal. After reaching $FFFFFF, it wraps to 0.

On the Amiga:
- **CIA-A** event counter receives 50 Hz pulses from the power supply (mains frequency), functioning as a simple elapsed-time counter.
- **CIA-B** event counter receives horizontal sync pulses at 15,625 Hz, providing a high-resolution timing source.

### Reading and Writing

The counter latches its value when the **MSB** (register $A) is read. Subsequent reads of registers $9 and $8 return the latched value while the internal counter continues running. The latch is released when the **LSB** (register $8) is read, ready for the next snapshot.

When writing, the counter stops after writing the MSB. It resumes when the LSB is written. Always write MSB first, then mid byte, then LSB.

### Alarm Function

Setting bit 7 (ALARM) in CRB causes writes to registers $8–$A to set the alarm value instead of the counter value. When the counter reaches the alarm value, the Alarm bit in the ICR is set and an interrupt can be generated. Reading registers $8–$A always returns the current counter value, regardless of the ALARM bit.

### Serial Shift Register (Register $C)

The serial port consists of a data register (SDR) and an internal shift register. The direction is controlled by the SPMODE bit in CRA:

**Input mode** (SPMODE = 0): data on the SP pin is shifted in on each positive edge of the CNT signal. After 8 bits, the shift register transfers to SDR and the SP interrupt flag is set.

**Output mode** (SPMODE = 1): Timer A controls the baud rate. Data written to SDR is transferred to the shift register and clocked out on SP, MSB first, at half the Timer A frequency. The CNT pin outputs the clock signal. Maximum output rate is one quarter of the 8520's clock frequency.

On CIA-A, SP carries keyboard serial data (KDAT) and CNT carries the keyboard clock (KCLK).

<!-- section: $BFE001 "Interrupt Control Register" -->

The ICR (register $D) manages five interrupt sources through a dual-register structure: a **data register** (read) and a **mask register** (write), both accessed at the same address.

### Reading the ICR (Data Register)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Source</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">7</span><span class="bit-dir">IR</span><span class="bit-name">—</span><span class="bit-func">Interrupt occurred (any enabled source fired)</span></div>
<div class="bit-row"><span class="bit-num">6–5</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">Always 0 (unused)</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">FLAG</span><span class="bit-name">FLAG pin</span><span class="bit-func">Negative edge detected on FLAG input</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">SP</span><span class="bit-name">Serial</span><span class="bit-func">Shift register full (input) or empty (output)</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">ALARM</span><span class="bit-name">Counter</span><span class="bit-func">Event counter matched alarm value</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">TB</span><span class="bit-name">Timer B</span><span class="bit-func">Timer B underflow (reached zero)</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">TA</span><span class="bit-name">Timer A</span><span class="bit-func">Timer A underflow (reached zero)</span></div>
</div>

Reading the ICR returns the data register and **clears all bits**, including IR. If you need the value later, save it to RAM immediately after reading.

### Writing the ICR (Mask Register)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Values</span>
<span class="bit-func">Function</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">7</span><span class="bit-dir">S/C</span><span class="bit-name">0=clear, 1=set</span><span class="bit-func">Set/Clear control for mask bits</span></div>
<div class="bit-row"><span class="bit-num">6–5</span><span class="bit-dir">—</span><span class="bit-name">—</span><span class="bit-func">Unused</span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">FLAG</span><span class="bit-name">mask</span><span class="bit-func">Enable/disable FLAG interrupt</span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">SP</span><span class="bit-name">mask</span><span class="bit-func">Enable/disable serial port interrupt</span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">ALARM</span><span class="bit-name">mask</span><span class="bit-func">Enable/disable alarm interrupt</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">TB</span><span class="bit-name">mask</span><span class="bit-func">Enable/disable Timer B interrupt</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">TA</span><span class="bit-name">mask</span><span class="bit-func">Enable/disable Timer A interrupt</span></div>
</div>

The mask register uses the same SET/CLR mechanism as DMACON. When S/C = 1, bits set in the written value are **enabled** in the mask. When S/C = 0, they are **disabled**. Bits written as 0 are never affected.

An interrupt is generated (IRQ pin goes low) only when a data register bit is set **and** the corresponding mask bit is enabled. When the ICR is read (clearing the data register), the IRQ line returns high.

```asm
    ; Enable Timer A interrupt on CIA-A:
    MOVE.B #$81,$BFED01    ; S/C=1, TA=1 → enable TA in mask

    ; Disable both timer interrupts:
    MOVE.B #$03,$BFED01    ; S/C=0, TA=1, TB=1 → clear TA and TB in mask
```

### CIA Interrupt Flow

When a CIA interrupt fires, the following sequence occurs:

1. A source sets its bit in the CIA's ICR data register
2. If the corresponding mask bit is set, the IR bit (bit 7) is set and IRQ goes low
3. PAULA detects the IRQ and sets the corresponding INTREQ bit (bit 3 for CIA-A, bit 13 for CIA-B)
4. If INTENA allows it, the 68000 receives the interrupt
5. The handler reads the CIA's ICR to identify the source (this clears the CIA-side flags)
6. The handler clears the INTREQ bit in PAULA
