# Turbocharger — Firmware Hardware Reference

Everything you need to know about the chip and pins to write your own firmware
for the **Hakai Labs Turbocharger**. It runs a **Microchip ATtiny804**.

## The chip

| Spec | Value |
|---|---|
| Core | 8-bit AVR, 20 MHz internal clock |
| Flash | 8 KB |
| SRAM | 512 B |
| EEPROM | 128 B |
| Package | 14-pin SOIC |
| Programming | UPDI (single wire, on PA0) |

With only 512 B of RAM, watch out for large buffers — the float-based glitch
engine already uses a good chunk of flash.

## Pinout

```
                  ATtiny804 (14-SOIC)
                  ┌───────────────────┐
            VDD ──┤ 1              14 ├── GND
   Det pot  PA4 ──┤ 2              13 ├── PA3   (unused)
   Gate in  PA5 ──┤ 3              12 ├── PA2   AFR pot
   RNG in   PA6 ──┤ 4              11 ├── PA1   RPM pot
   Det LED  PA7 ──┤ 5              10 ├── PA0   UPDI (programming)
   Turbo in PB3 ──┤ 6               9 ├── PB0   Injection out
   Status   PB2 ──┤ 7               8 ├── PB1   RPM clock out
                  └───────────────────┘
```

| Pin | Signal | Dir | Notes |
|---|---|---|---|
| PA1 | **RPM pot** | Analog in | ADC AIN1. Sets internal clock frequency (~1.5 Hz–1570 Hz, exp curve). |
| PA2 | **AFR pot** | Analog in | ADC AIN2. Sets clock pulse width (PWM duty). |
| PA4 | **Detonation pot** | Analog in | ADC AIN4. Master "chaos" control — selects glitch type/intensity. |
| PA5 | **Gate in** | Digital in | Rising-edge IRQ. Retriggers the clock. Pull-up on; defaults HIGH when unpatched. |
| PA6 | **RNG in** | Analog in | ADC AIN6. Left floating (no pull-up) to read noise for randomness. |
| PA7 | **Detonation LED** | Digital out | Lights while a glitch is firing. |
| PB0 | **Injection out** | Digital out | Detonation glitch pulse. Tied to PB1 through a diode. |
| PB1 | **RPM clock out** | Digital out | PWM on TCA0 **WO1**. Frequency from RPM pot, duty from AFR pot. |
| PB2 | **Status LED** | Digital out | Blinks firmware version at boot; debug. |
| PB3 | **Turbo in** | Digital in | Rising-edge IRQ. External clock tick that drives the glitch timing. |
| PA0 | UPDI | — | Reserved for programming. |
| PA3 | *unused* | — | Free if you want an extra input/output. |

Pull-ups are on for every input except the RNG pin (PA6).

## Peripherals to bring up

**Clock.** Run at 20 MHz: `_PROTECTED_WRITE(CLKCTRL_MCLKCTRLB, 0)` and
`F_CPU = 20000000UL`. Make sure the fuse selects the 20 MHz oscillator or all
timing will be off.

**ADC0.** Single-ended, `VDDREF` reference, `DIV16` prescaler. Read a channel by
setting `ADC0.MUXPOS`: AIN1 = RPM pot, AIN2 = AFR pot, AIN4 = Detonation pot,
AIN6 = RNG.

**TCA0 — single-slope PWM.** Drives the RPM clock on **WO1 (PB1)**, `DIV64`
prescaler. `PER` = frequency, `CMP1` = duty (from the AFR value).

**Port interrupts (both rising-edge).**

- `ISR(PORTB_PORT_vect)` — Turbo In (PB3): advances the glitch clock divider.
- `ISR(PORTA_PORT_vect)` — Gate (PA5): restarts the PWM timer.

## Building & flashing

The firmware uses bare AVR registers (`avr/io.h`, no Arduino HAL), so it builds
under **[megaTinyCore](https://github.com/SpenceKonde/megaTinyCore)**.

1. Install megaTinyCore via the Arduino Boards Manager.
2. Select **ATtiny804**, **Clock: 20 MHz internal**.
3. There's no bootloader — flash over **UPDI (PA0)** with a SerialUPDI adapter,
   jtag2updi, or an Atmel-ICE/SNAP. Use **Sketch → Upload Using Programmer**.

---
*Firmware v3.1 · ATtiny804 · Hakai Labs Turbocharger*
