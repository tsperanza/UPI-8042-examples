# UPI-41 / 8742 Timing Model

This document explains the internal timing model of the Intel UPI-41 / UPI-42 family, with emphasis on instruction execution time and the internal timer behavior.

Understanding this timing model is essential for writing accurate delay routines and for correctly interfacing with external hardware such as LCD controllers.

All explanations are based on the official Intel documentation and verified on real hardware.

---

## 1. Clock Source and Instruction Timing

The system described in this repository uses a **4 MHz crystal oscillator** connected to the UPI.

The UPI does **not** execute instructions directly at the oscillator frequency.

The timing chain is as follows:
Oscillator → ÷3 → State Time → 5 States → 1 Instruction Cycle

---

### 1.1 Oscillator Period

With a 4 MHz crystal:
Tosc = 1 / 4 MHz = 250 ns

---

### 1.2 State Time

According to the datasheet, the oscillator clock is internally divided by **3** to generate the *state time*.
Tinstruction = 5 × 750 ns = 3.75 µs

📌 **Important:**  
This value applies to most instructions.  
Taken branches and some special instructions may take additional cycles.

---

## 2. Internal Timer Operation

The UPI includes an internal 8-bit timer register called **T**.

### 2.1 Timer Increment Rate

The datasheet states that the timer register is incremented once every **32 instruction cycles**.

This means:
Timer increment period = 32 × 3.75 µs = 120 µs
So the timer increases by 1 every **120 microseconds**.

---

### 2.2 Timer Overflow

The timer is an 8-bit register:

- It increments from 0x00 to 0xFF
- After 0xFF, it overflows to 0x00
- When this happens, the **TF (Timer Flag)** is set

The overflow **does not generate an interrupt**.
It must be polled using the `JTF` instruction.

---

## 3. Using the Timer for Precise Delays

Because the timer increments every 120 µs, it can be used to generate accurate millisecond delays.

### 3.1 1 Millisecond Delay Calculation

To generate a 1 ms delay:
1 ms / 120 µs ≈ 8.33 timer counts

Using 8 counts:
8 × 120 µs = 960 µs

Using 9 counts:
9 × 120 µs = 1080 µs

To obtain a delay as close as possible to 1 ms, the timer is preloaded so that it overflows after **8 increments**.

### 3.2 Timer Preload Value

Timer overflow occurs at 256.

To get 8 increments:
256 − 8 = 248 (0xF8)

So loading the timer with **248** produces a delay of approximately **960 µs**, which is sufficiently accurate and repeatable.

---

## 4. 1 ms Delay Routine Example

```asm
;
; 1 ms delay routine using internal timer
; Input: R0 = number of milliseconds
; Modifies: R0, A, T
;

delay:
    mov a, #248      ; preload timer
    mov t, a
    strt t           ; start timer

wait_tf:
    jtf wait_done    ; wait for overflow
    jmp wait_tf

wait_done:
    stop tcnt        ; stop timer
    djnz r0, delay
    ret
