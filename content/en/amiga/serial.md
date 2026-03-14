---
title: "Serial Port"
addr: "$DFF030"
description: "Amiga serial port — UART operation, RS232 communication, baud rate configuration, and transmit/receive registers in Paula."
order: 12
---

<!-- section: $DFF030 "Serial Port Overview" -->

The Amiga includes a standard RS232 serial port managed by a **UART** (Universal Asynchronous Receiver/Transmitter) built into the Paula custom chip. The port supports full-duplex communication — data can be transmitted and received simultaneously over separate signal lines.

The RS232 connector carries two categories of signals:

1. **Data signals** — TXD (transmit data) and RXD (receive data), which carry the actual serial bitstream.
2. **Handshake signals** — CTS, RTS, DSR, DTR, etc., which are managed through the CIA chips and were covered in the CIA documentation.

All serial data transfer takes place over the TXD and RXD lines. When connecting two devices, TXD on one device is wired to RXD on the other and vice versa (null-modem / crossover wiring).

### RS232 Data Format

Since only a single wire carries data in each direction, bytes must be serialized bit-by-bit. Because RS232 has no separate clock signal, both sender and receiver must agree on the bit timing — the **baud rate**, which specifies the number of bits transferred per second. Common baud rates are 300, 1200, 2400, 4800, and 9600.

Each byte is framed as follows:

1. **Start bit** — a transition from the idle (high) state to low, signaling the beginning of a byte.
2. **Data bits** — transmitted LSB first, in 8-bit or 9-bit format.
3. **Stop bit(s)** — one or two high bits marking the end of the byte.

The receiver detects the start of each byte by the high-to-low transition between the stop bit of the previous byte and the start bit of the new one.

<!-- section: $DFF030 "UART Registers" -->

The UART in Paula uses three registers for serial data transfer, plus one bit in the ADKCON register for break control.

<div class="register-block">
<div class="reg-title">Serial Port Registers</div>
<div class="reg-row"><span class="reg-field">$DFF030</span><span class="reg-val">SERDAT — Serial transmit data (write only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF018</span><span class="reg-val">SERDATR — Serial receive data + status (read only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF032</span><span class="reg-val">SERPER — Serial period / baud rate (write only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF09E</span><span class="reg-val">ADKCON — Bit 11 (UARTBRK): force TXD low (write only)</span></div>
</div>

### SERDATR — Receive Data and Status ($DFF018, read)

This register contains both the received data byte and status flags for the UART.

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">OVRUN</span><span class="bit-name">Overrun — shift register full before buffer was read</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">14</span><span class="bit-dir">RBF</span><span class="bit-name">Receive Buffer Full — data ready to read</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">13</span><span class="bit-dir">TBE</span><span class="bit-name">Transmit Buffer Empty — ready for next byte</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">TSRE</span><span class="bit-name">Transmit Shift Register Empty — transmission complete</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">11</span><span class="bit-dir">RXD</span><span class="bit-name">Current level of the RXD signal line</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">—</span><span class="bit-name">Unused</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9</span><span class="bit-dir">STP</span><span class="bit-name">Stop bit</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">STP/DB8</span><span class="bit-name">Stop bit (8-bit mode) or data bit 8 (9-bit mode)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7</span><span class="bit-dir">DB7</span><span class="bit-name">Received data bit 7</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">6</span><span class="bit-dir">DB6</span><span class="bit-name">Received data bit 6</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">5</span><span class="bit-dir">DB5</span><span class="bit-name">Received data bit 5</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">4</span><span class="bit-dir">DB4</span><span class="bit-name">Received data bit 4</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">3</span><span class="bit-dir">DB3</span><span class="bit-name">Received data bit 3</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">2</span><span class="bit-dir">DB2</span><span class="bit-name">Received data bit 2</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">DB1</span><span class="bit-name">Received data bit 1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">DB0</span><span class="bit-name">Received data bit 0</span><span class="bit-func"></span></div>
</div>

### SERPER — Period Register ($DFF032, write)

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">LONG</span><span class="bit-name">Set to 1 for 9-bit receive mode</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">14–0</span><span class="bit-dir">RATE</span><span class="bit-name">15-bit period value determining baud rate</span><span class="bit-func"></span></div>
</div>

<!-- section: $DFF030 "Receiving Data" -->

Serial reception proceeds in two stages, using a shift register and a data buffer.

### Stage 1: Shift Register

Incoming bits on the RXD pin are clocked into a shift register at the rate determined by SERPER. The start bit triggers the process: the UART samples RXD at the center of each bit period to reconstruct a parallel data word.

### Stage 2: Data Buffer

When the shift register is full (all data bits plus stop bit received), its contents are transferred to the receive buffer — the data bits in SERDATR. The shift register is then immediately free to begin receiving the next byte.

### Data Format

The data width depends on the LONG bit in SERPER:

- **LONG = 0 (8-bit mode):** Bits 7–0 of SERDATR contain the 8 data bits. Bit 8 holds the stop bit. Bit 9 holds the second stop bit (if present).
- **LONG = 1 (9-bit mode):** Bits 8–0 contain 9 data bits. Bit 9 holds the stop bit.

### Status Flags

**RBF** (Receive Buffer Full) is set to 1 when a complete data word is transferred from the shift register to the buffer. This flag also exists as bit 11 in INTREQ/INTEN, generating an interrupt. After reading the data, software must clear RBF by writing to INTREQ:

```asm
    move.w #$0800,$DFF09C      ; clear RBF in INTREQ (and SERDATR)
```

### Overrun Detection

If the receive buffer is not read before the shift register fills again, an **overrun** occurs. The OVRUN bit in SERDATR is set to 1, indicating that data has been lost. When RBF is finally cleared, the shift register contents are transferred to the buffer and OVRUN is reset. RBF then goes high again immediately, since the buffer now contains the (late) data from the shift register.

To avoid overruns, always read SERDATR and clear RBF promptly — ideally in an RBF interrupt handler.

<!-- section: $DFF030 "Transmitting Data" -->

Serial transmission also uses a two-stage pipeline: a data buffer (SERDAT) and an output shift register.

### Writing Data

The transmit data format in SERDAT depends on the desired word length and number of stop bits. The data bits occupy the lower portion of the register, followed immediately by one or two stop bits (set to 1). All higher bits must be 0.

**8-bit data, 2 stop bits:**

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">15–10</span>
<span class="bit-name">9–8</span>
<span class="bit-func">7–0</span>
</div>
<div class="bit-row"><span class="bit-num">Value</span><span class="bit-dir">0 0 0 0 0 0</span><span class="bit-name">1 1 (stop bits)</span><span class="bit-func">D7–D0 (data)</span></div>
</div>

**8-bit data, 1 stop bit:**

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">15–9</span>
<span class="bit-name">8</span>
<span class="bit-func">7–0</span>
</div>
<div class="bit-row"><span class="bit-num">Value</span><span class="bit-dir">0 0 0 0 0 0 0</span><span class="bit-name">1 (stop bit)</span><span class="bit-func">D7–D0 (data)</span></div>
</div>

**9-bit data, 1 stop bit:**

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">15–10</span>
<span class="bit-name">9</span>
<span class="bit-func">8–0</span>
</div>
<div class="bit-row"><span class="bit-num">Value</span><span class="bit-dir">0 0 0 0 0 0</span><span class="bit-name">1 (stop bit)</span><span class="bit-func">D8–D0 (data)</span></div>
</div>

Note that the LONG bit in SERPER only affects reception. The transmit format is determined entirely by the value written to SERDAT.

### Transmit Status Flags

**TBE** (Transmit Buffer Empty) is set to 1 when the data in SERDAT has been transferred to the output shift register. The buffer is then ready for the next word. TBE is also bit 0 in INTREQ/INTEN. Like RBF, it must be cleared via INTREQ after servicing.

**TSRE** (Transmit Shift Register Empty) is set to 1 when the shift register has finished sending all bits and no new data is waiting in the buffer. This indicates that the transmission is truly complete. TSRE is reset when TBE is cleared.

### UARTBRK — Break Signal

Bit 11 (UARTBRK) of the ADKCON register, when set, forces the TXD output line low and halts serial transmission. This generates a "break" condition on the RS232 line, which the remote device can detect. Clear the bit to resume normal operation.

<div class="register-block">
<div class="reg-title">ADKCON Serial Bit</div>
<div class="reg-row"><span class="reg-field">$DFF09E</span><span class="reg-val">ADKCON bit 11 — UARTBRK: <span class="highlight">set to force TXD low (break condition)</span></span></div>
<div class="reg-row"><span class="reg-field">$DFF010</span><span class="reg-val">ADKCONR bit 11 — read back UARTBRK state</span></div>
</div>

<!-- section: $DFF030 "Baud Rate Calculation" -->

The lower 15 bits of SERPER (bits 14–0, the RATE field) specify the bit period as a number of bus clock cycles. One bus clock cycle lasts approximately **279.365 nanoseconds** (based on the PAL system clock of 3.546895 MHz).

The formula to convert a desired baud rate to the SERPER value is:

<div class="mem-block">
SERPER = (1 / (baud_rate * 279.365 * 10^-9)) - 1
</div>

Or equivalently:

<div class="mem-block">
SERPER = (3,579,545 / baud_rate) - 1     (NTSC clock)
SERPER = (3,546,895 / baud_rate) - 1     (PAL clock)
</div>

### Common Baud Rates

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Baud Rate</span>
<span class="bit-dir">SERPER Value (PAL)</span>
<span class="bit-name">SERPER Value (NTSC)</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">300</span><span class="bit-dir">11822</span><span class="bit-name">11931</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1200</span><span class="bit-dir">2955</span><span class="bit-name">2982</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">2400</span><span class="bit-dir">1477</span><span class="bit-name">1491</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">4800</span><span class="bit-dir">738</span><span class="bit-name">745</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9600</span><span class="bit-dir">369</span><span class="bit-name">372</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">19200</span><span class="bit-dir">184</span><span class="bit-name">186</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">31250 (MIDI)</span><span class="bit-dir">112</span><span class="bit-name">113</span><span class="bit-func"></span></div>
</div>

The calculated value is rounded to the nearest integer before writing to SERPER:

```asm
    move.w #745,$DFF032        ; set 4800 baud (NTSC), LONG=0 (8-bit)
```

For 9-bit receive mode, set bit 15:

```asm
    move.w #$8000+745,$DFF032  ; set 4800 baud, LONG=1 (9-bit receive)
```

<!-- section: $DFF030 "Interrupts and Programming" -->

The UART generates two interrupt signals, both managed through INTREQ/INTEN:

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">INTREQ Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Condition</span>
<span class="bit-func">Level</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">0</span><span class="bit-dir">TBE</span><span class="bit-name">Transmit buffer empty — ready for next word</span><span class="bit-func">1</span></div>
<div class="bit-row bit-highlight"><span class="bit-num">11</span><span class="bit-dir">RBF</span><span class="bit-name">Receive buffer full — data available</span><span class="bit-func">5</span></div>
</div>

### Typical Receive Handler

```asm
SerialReceiveISR:
    move.w $DFF018,d0          ; read SERDATR
    btst   #15,d0              ; check OVRUN
    bne    .overrun            ; handle overrun condition
    and.w  #$00FF,d0           ; extract 8 data bits
    ; ... process received byte in d0 ...
    move.w #$0800,$DFF09C      ; clear RBF in INTREQ
    rte

.overrun:
    move.w #$0800,$DFF09C      ; clear RBF to recover
    ; ... signal overrun error ...
    rte
```

### Typical Transmit Sequence

```asm
SendByte:
    ; d0.b = byte to send
    and.w  #$00FF,d0           ; mask to 8 bits
    or.w   #$0100,d0           ; add 1 stop bit (bit 8 = 1)
    move.w d0,$DFF030          ; write to SERDAT
    ; TBE interrupt will fire when buffer is free for next byte
    rts
```

The UART in Paula handles start bit insertion and bit serialization automatically. Software only needs to write the data word (with stop bits) to SERDAT and wait for TBE before sending the next byte.
