# EV Battery Low-Voltage Warning System (LTspice)

A comparator-based warning circuit that lights a **green LED** when the EV battery is above **12 V** and a **red LED** when it drops below 12 V. Simulated in LTspice XVII.

**Author:** Darwin Danielraj B

---

## Aim
Design and simulate a low-voltage warning system for an EV battery with a 12 V threshold.

## Tools and components

| Item | Value |
|---|---|
| Software | LTspice XVII |
| Battery (V1) | PWL: 14.4 V down to 9 V over 10 s |
| Divider | R1 = 200 kΩ, R2 = 100 kΩ |
| Reference (V2) | 4 V DC |
| Comparator (U1) | Op-amp powered from Vbat and GND |
| LED resistors | R3 = R4 = 2.2 kΩ |
| Green LED (D1) | `DLED` model |
| Red LED (D2) | `DRED` model |
| Analysis | `.tran 0 10` |

## Principle
- R1 and R2 scale the battery voltage down by 3.
- The op-amp compares the scaled voltage (Vdiv) with a 4 V reference (Vref).
- The output drives the LEDs in opposite ways: high lights green, low lights red.

## Design calculation
Threshold = 12 V

`Vdiv = Vbat x R2 / (R1 + R2) = 12 x 100k / 300k = 4 V`

| Vbat | Vdiv |
|---|---|
| 14.4 V | 4.8 V |
| 12 V | 4.0 V (threshold) |
| 9 V | 3.0 V |

## Circuit

<img width="1917" height="991" alt="EV-Battery-Low-Voltage-Warning circuit" src="https://github.com/user-attachments/assets/2c72f6a6-88b4-4fb7-a418-f02ec0528337" />


- **Green LED path:** Vout, R3, D1, GND
- **Red LED path:** Vbat, R4, D2, Vout

## Working logic

```
when simulation starts
forever
  Vdiv = Vbat / 3
  if Vdiv > 4 V                  (battery above 12 V)
      Vout = HIGH
      D1 green = ON
      D2 red   = OFF
  else                           (battery below 12 V)
      Vout = LOW
      D2 red   = ON
      D1 green = OFF
```

## Simulation setup
1. Battery source: `PWL(0 14.4 3 13 5 12 7 11 9 10 10 9)`
2. Net labels: `Vbat`, `Vdiv`, `Vref`, `Vout`
3. Directive: `.tran 0 10`
4. Plotted: `V(vbat)`, `V(vdiv)`, `V(vout)`, `I(D1)`, `I(D2)`

## Results

<img width="1917" height="1013" alt="simulation" src="https://github.com/user-attachments/assets/bec1413c-1de9-4b1d-98ef-bfe2170158a6" />


| Time | Vbat | Vdiv | Vout | I(D1) green | I(D2) red |
|---|---|---|---|---|---|
| 0 to 5 s | 14.4 V to 12 V | 4.8 V to 4 V | High | about 5 mA | 0 |
| 5 s | 12 V | 4 V | Switches | falls to 0 | rises from 0 |
| 5 to 10 s | 12 V to 9 V | 4 V to 3 V | 0 V | 0 | about 4.4 to 3.1 mA |

## Observations
- Vdiv is always Vbat / 3.
- Vdiv crosses 4 V at **t = 5 s**, exactly when Vbat = 12 V.
- Vout drops to 0 V at that instant, so green turns OFF and red turns ON.
- LED current changes slowly with battery voltage because of the fixed series resistors.

## Conclusion
The circuit detects when the battery falls below 12 V. Above the threshold only the green LED conducts, and below it only the red LED conducts.

## Limitations and future work
- No hysteresis, so the LEDs may flicker near 12 V. A ~1 MΩ positive-feedback resistor from Vout to the + input would fix this.
- A second comparator (for example at 10 V) could add a critical alert with a buzzer.

## How to run
1. Install [LTspice](https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html).
2. Open `EV-Battery-Low-Voltage-Warning.asc`.
3. Click Run, then click the nets and LEDs to plot.
