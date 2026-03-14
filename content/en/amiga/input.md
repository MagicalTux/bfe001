---
title: "Mouse & Joystick"
addr: "$JOY00A"
description: "Amiga game port input — mouse quadrature decoding, joystick digital reading, paddle analog inputs, and fire button registers."
order: 11
---

<!-- section: $JOY00A "Game Port Overview" -->

The Amiga provides two **game ports** (DB-9 connectors) on the front or side of the machine, each capable of accepting a mouse, a digital joystick, or analog paddles. All three device types share the same physical connector but use different subsets of the port signals. The input hardware is implemented in the Denise and Paula custom chips, with button inputs routed through CIA-A.

Each game port carries four directional signals, two analog inputs, and a primary button line. The directional signals serve double duty: for a mouse, they carry quadrature-encoded pulses; for a joystick, they carry simple switch closures. Denise contains the counters and logic for both modes, accessible through the **JOYxDAT** registers.

### Port Signal Assignment

The nine pins of each DB-9 connector are allocated as follows:

- **Pins 1–4:** Four directional signals (up, down, left, right for joystick; V, VQ, H, HQ for mouse)
- **Pin 5:** Analog pot Y (POTY)
- **Pin 6:** Fire button (directly active on press)
- **Pin 7:** +5V power supply
- **Pin 8:** Ground
- **Pin 9:** Analog pot X (POTX)

<!-- section: $JOY00A "Mouse Quadrature Signals" -->

The mouse uses an optical encoding system to report movement. Inside the mouse, a rubber-coated ball drives two perpendicular axle shafts. Each shaft has a slotted disc at its end that interrupts a light beam as it rotates. Two photo-sensors per disc, offset by half a slot width, generate a pair of quadrature signals per axis:

- **H** and **HQ** (horizontal pulse and horizontal quadrature pulse)
- **V** and **VQ** (vertical pulse and vertical quadrature pulse)

The phase relationship between the pulse and quadrature pulse signals encodes the direction of movement. When the mouse moves right, H leads HQ; when it moves left, HQ leads H. The same principle applies vertically.

### Signal Processing

Denise derives two internal signals from each pulse pair using simple logic:

- **X1** = inverse of HQ
- **X0** = H XOR HQ (equals 1 whenever H and HQ differ)

The truth table for horizontal signals:

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">H</span>
<span class="bit-dir">HQ</span>
<span class="bit-name">X0 (H XOR HQ)</span>
<span class="bit-func">X1 (HQ)</span>
</div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">0</span><span class="bit-name">0</span><span class="bit-func">0</span></div>
<div class="bit-row"><span class="bit-num">0</span><span class="bit-dir">1</span><span class="bit-name">1</span><span class="bit-func">1</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">0</span><span class="bit-name">1</span><span class="bit-func">0</span></div>
<div class="bit-row"><span class="bit-num">1</span><span class="bit-dir">1</span><span class="bit-name">0</span><span class="bit-func">1</span></div>
</div>

These derived signals drive an internal 8-bit counter that increments for rightward/downward movement and decrements for leftward/upward movement. The counter value represents the current relative position of the mouse. The mouse generates approximately 200 pulses per inch (about 79 per centimeter).

<!-- section: $JOY00A "JOYxDAT Registers" -->

Two read-only registers in Denise hold the mouse/joystick counter values, one per game port:

<div class="register-block">
<div class="reg-title">Mouse / Joystick Data Registers</div>
<div class="reg-row"><span class="reg-field">$DFF00A</span><span class="reg-val">JOY0DAT — Game port 0 data (read only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF00C</span><span class="reg-val">JOY1DAT — Game port 1 data (read only)</span></div>
</div>

Each register packs two 8-bit counters into a single 16-bit word:

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">15–8</span>
<span class="bit-name">7–0</span>
<span class="bit-func">Access</span>
</div>
<div class="bit-row"><span class="bit-num">Field</span><span class="bit-dir">Y7–Y0</span><span class="bit-name">X7–X0</span><span class="bit-func">Read only</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Description</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">15–8</span><span class="bit-dir">Y7–Y0</span><span class="bit-name">Vertical movement counter (Y axis)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–0</span><span class="bit-dir">X7–X0</span><span class="bit-name">Horizontal movement counter (X axis)</span><span class="bit-func"></span></div>
</div>

### Counter Overflow Handling

Since each counter is only 8 bits (0–255), it overflows after roughly 3–4 cm of mouse travel. To track absolute position, software must poll the counters frequently and detect overflows. The operating system samples JOYxDAT during the **vertical blank interrupt**, which guarantees that movement between two samples never exceeds 127 counter steps.

The direction of movement is determined by comparing the current counter value with the previous one:

- **Difference between -127 and +127:** No overflow. Positive = right/down, negative = left/up.
- **Difference > +127:** Underflow occurred. Actual movement = -(256 - difference).
- **Difference < -127:** Overflow occurred. Actual movement = 256 + difference.

### JOYTEST — Counter Preset

The counters can be preset by writing to the **JOYTEST** register. This sets both JOY0DAT and JOY1DAT simultaneously.

<div class="register-block">
<div class="reg-title">Counter Preset Register</div>
<div class="reg-row"><span class="reg-field">$DFF036</span><span class="reg-val">JOYTEST — Set mouse counter values (write only)</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Description</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">15–10</span><span class="bit-dir">Y7–Y2</span><span class="bit-name">Vertical counter preset (upper 6 bits)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">9–8</span><span class="bit-dir">—</span><span class="bit-name">Not used</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–2</span><span class="bit-dir">X7–X2</span><span class="bit-name">Horizontal counter preset (upper 6 bits)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">1–0</span><span class="bit-dir">—</span><span class="bit-name">Not used</span><span class="bit-func"></span></div>
</div>

Only the upper 6 bits of each counter can be preset. The two lowest bits of each counter come directly from the mouse signal lines and cannot be written by software.

<!-- section: $JOY00A "Joystick Reading" -->

A digital joystick uses simple switch closures that produce the same signals as the mouse quadrature lines. Because the signals are static (not pulsed), Denise's counters settle to fixed values that encode the switch positions. Joystick direction is decoded directly from the JOYxDAT bits:

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Direction</span>
<span class="bit-dir">Condition</span>
<span class="bit-name">JOYxDAT Bits</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">Right</span><span class="bit-dir">X1 = 1</span><span class="bit-name">Bit 1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">Left</span><span class="bit-dir">Y1 = 1</span><span class="bit-name">Bit 9</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">Backward (down)</span><span class="bit-dir">X0 XOR X1 = 1</span><span class="bit-name">Bit 0 XOR Bit 1</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">Forward (up)</span><span class="bit-dir">Y0 XOR Y1 = 1</span><span class="bit-name">Bit 8 XOR Bit 9</span><span class="bit-func"></span></div>
</div>

Left and right are simple single-bit tests. Forward and backward require an XOR operation between adjacent bits. Here is the standard joystick test routine for game port 1:

```asm
TestJoystick:
    move.w $DFF00C,d0          ; read JOY1DAT into d0
    btst   #1,d0               ; test bit 1 (X1)
    bne    right               ; if set, joystick is pushed right
    btst   #9,d0               ; test bit 9 (Y1)
    bne    left                ; if set, joystick is pushed left
    move.w d0,d1               ; copy to d1
    lsr.w  #1,d1               ; shift right: X1 aligns with X0, Y1 with Y0
    eor.w  d0,d1               ; XOR: d1.bit0 = X0^X1, d1.bit8 = Y0^Y1
    btst   #0,d1               ; test X0 XOR X1
    bne    backward            ; if set, joystick is pushed backward (down)
    btst   #8,d1               ; test Y0 XOR Y1
    bne    forward             ; if set, joystick is pushed forward (up)
    bra    center              ; joystick is in neutral position
```

This routine does not handle diagonal positions. To support diagonals, test each axis independently rather than using an if-else chain.

<!-- section: $JOY00A "Paddle / Analog Inputs" -->

Each game port provides two analog inputs (POTX and POTY) for connecting proportional controllers such as paddles or analog joysticks. A paddle contains a variable resistor (potentiometer) whose position determines a resistance value. Analog joysticks work similarly, with one potentiometer per axis.

### Analog Measurement Principle

The Amiga uses a capacitor-based timing method to measure resistance. Each of the four analog inputs is connected internally to a capacitor (47 nF) between the input pin and ground. The measurement cycle works as follows:

1. Paula discharges all four capacitors by briefly pulling the inputs to ground, and resets the counter registers.
2. The capacitors begin charging through the external potentiometers.
3. A counter increments once per display line while charging continues.
4. When the voltage on a capacitor crosses a threshold, the corresponding counter stops.

A low resistance charges the capacitor quickly (low counter value). A high resistance charges slowly (high counter value). The potentiometers must have a maximum resistance of 470 k-ohm (+/-10%).

### POTxDAT Registers

<div class="register-block">
<div class="reg-title">Analog Input Registers</div>
<div class="reg-row"><span class="reg-field">$DFF012</span><span class="reg-val">POT0DAT — Game port 0 analog values (read only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF014</span><span class="reg-val">POT1DAT — Game port 1 analog values (read only)</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Description</span>
<span class="bit-func"></span>
</div>
<div class="bit-row"><span class="bit-num">15–8</span><span class="bit-dir">Y7–Y0</span><span class="bit-name">POTY counter value (right paddle / Y axis)</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–0</span><span class="bit-dir">X7–X0</span><span class="bit-name">POTX counter value (left paddle / X axis)</span><span class="bit-func"></span></div>
</div>

### POTGO / POTGOR — Control and Readback

The **POTGO** register controls the analog measurement cycle and can also configure the analog lines as general-purpose digital I/O.

<div class="register-block">
<div class="reg-title">Analog Control Registers</div>
<div class="reg-row"><span class="reg-field">$DFF034</span><span class="reg-val">POTGO — Analog port control (write only)</span></div>
<div class="reg-row"><span class="reg-field">$DFF016</span><span class="reg-val">POTGOR (POTINP) — Analog port status (read only)</span></div>
</div>

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Bit</span>
<span class="bit-dir">Name</span>
<span class="bit-name">Function</span>
<span class="bit-func"></span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">15</span><span class="bit-dir">OUTRY</span><span class="bit-name">Game port 1 POTY set as output</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">14</span><span class="bit-dir">DATRY</span><span class="bit-name">Game port 1 POTY data bit</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">13</span><span class="bit-dir">OUTRX</span><span class="bit-name">Game port 1 POTX set as output</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">12</span><span class="bit-dir">DATRX</span><span class="bit-name">Game port 1 POTX data bit</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">11</span><span class="bit-dir">OUTLY</span><span class="bit-name">Game port 0 POTY set as output</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">10</span><span class="bit-dir">DATLY</span><span class="bit-name">Game port 0 POTY data bit</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">9</span><span class="bit-dir">OUTLX</span><span class="bit-name">Game port 0 POTX set as output</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">8</span><span class="bit-dir">DATLX</span><span class="bit-name">Game port 0 POTX data bit</span><span class="bit-func"></span></div>
<div class="bit-row"><span class="bit-num">7–1</span><span class="bit-dir">—</span><span class="bit-name">Unused</span><span class="bit-func"></span></div>
<div class="bit-row bit-highlight"><span class="bit-num">0</span><span class="bit-dir">START</span><span class="bit-name">Discharge capacitors and start measurement</span><span class="bit-func"></span></div>
</div>

Writing to POTGO resets both POTxDAT counters. Normally, the START bit is set to 1 during the vertical blank. During the active display, the capacitors charge and the counters run. By the next vertical blank, the POTxDAT registers contain the final measured values.

Setting an OUTxx bit to 1 switches the corresponding line to digital output mode, disconnecting it from the capacitor measurement circuit. The DATxx bit value in POTGO is then driven on the pin. Reading DATxx from POTGOR always reflects the current logic level of the pin, regardless of mode.

**Note:** When using the analog ports as digital I/O, the 47 nF capacitors on the lines cause a settling delay of up to 300 microseconds after each signal transition.

<!-- section: $JOY00A "Fire Buttons" -->

Each input device has one or more buttons. The primary fire button (or left mouse button) is active-low and directly connected to CIA-A.

### Game Port 0 Buttons

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Button</span>
<span class="bit-dir">Register</span>
<span class="bit-name">Bit</span>
<span class="bit-func">Active</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">Left mouse / Fire</span><span class="bit-dir">CIA-A PRA ($BFE001)</span><span class="bit-name">Bit 6</span><span class="bit-func">Low (0 = pressed)</span></div>
<div class="bit-row"><span class="bit-num">Right mouse</span><span class="bit-dir">POTGOR ($DFF016)</span><span class="bit-name">DATLY (bit 10)</span><span class="bit-func">Low (0 = pressed)</span></div>
<div class="bit-row"><span class="bit-num">Third mouse button</span><span class="bit-dir">POTGOR ($DFF016)</span><span class="bit-name">DATLX (bit 8)</span><span class="bit-func">Low (0 = pressed)</span></div>
<div class="bit-row"><span class="bit-num">Left paddle button</span><span class="bit-dir">JOY0DAT ($DFF00A)</span><span class="bit-name">Bit 9</span><span class="bit-func">High (1 = pressed)</span></div>
<div class="bit-row"><span class="bit-num">Right paddle button</span><span class="bit-dir">JOY0DAT ($DFF00A)</span><span class="bit-name">Bit 1</span><span class="bit-func">High (1 = pressed)</span></div>
</div>

### Game Port 1 Buttons

<div class="bit-table">
<div class="bit-header">
<span class="bit-num">Button</span>
<span class="bit-dir">Register</span>
<span class="bit-name">Bit</span>
<span class="bit-func">Active</span>
</div>
<div class="bit-row bit-highlight"><span class="bit-num">Left mouse / Fire</span><span class="bit-dir">CIA-A PRA ($BFE001)</span><span class="bit-name">Bit 7</span><span class="bit-func">Low (0 = pressed)</span></div>
<div class="bit-row"><span class="bit-num">Right mouse</span><span class="bit-dir">POTGOR ($DFF016)</span><span class="bit-name">DATRY (bit 14)</span><span class="bit-func">Low (0 = pressed)</span></div>
<div class="bit-row"><span class="bit-num">Third mouse button</span><span class="bit-dir">POTGOR ($DFF016)</span><span class="bit-name">DATRX (bit 12)</span><span class="bit-func">Low (0 = pressed)</span></div>
<div class="bit-row"><span class="bit-num">Left paddle button</span><span class="bit-dir">JOY1DAT ($DFF00C)</span><span class="bit-name">Bit 9</span><span class="bit-func">High (1 = pressed)</span></div>
<div class="bit-row"><span class="bit-num">Right paddle button</span><span class="bit-dir">JOY1DAT ($DFF00C)</span><span class="bit-name">Bit 1</span><span class="bit-func">High (1 = pressed)</span></div>
</div>

Unless otherwise noted, buttons are **active-low** (0 = pressed). The exceptions are the paddle buttons, which are active-high (1 = pressed). To read the fire button on port 1 (standard joystick port):

```asm
    btst #7,$BFE001        ; test CIA-A PRA bit 7
    beq  fire_pressed      ; branch if zero (button pressed)
```

The right mouse button and third mouse button are read through the POTGOR register. To use them, the corresponding OUTxx bit in POTGO must be cleared (input mode), which is the default state.
