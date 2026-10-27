# common-source-amplifier

[![Simulate](https://github.com/YOUR-USERNAME/common-source-amplifier/actions/workflows/ci.yml/badge.svg)](https://github.com/YOUR-USERNAME/common-source-amplifier/actions)

A **common-source NMOS amplifier** with a resistive load, simulated and characterised in
[ngspice](https://ngspice.sourceforge.io/). Every result is compared against a hand
calculation, and the whole project is checked automatically with one command.

![DC transfer curve](docs/dc_transfer.png)

## Circuit

NMOS `M1` (W/L = 5 µm / 0.36 µm), 10 kΩ drain resistor to VDD = 1.8 V, 1 pF load
capacitor, gate biased at 0.7 V.

## Results

| Quantity | Simulated | Hand calculation |
|---|---|---|
| Bias current | 114 µA | |
| Output bias | 0.658 V (transistor in saturation) | needs Vds > Vov = 0.25 V |
| Transconductance gm | 0.914 mS | |
| Small-signal gain | **18.49 dB (8.4 V/V)** | gm·(Rd ‖ ro) = 18.49 dB |
| Gain from a transient run | 8.41 V/V | matches the AC result |
| -3 dB bandwidth | **17.2 MHz** | 1/(2π·Rout·CL) = 17.3 MHz |
| Unity-gain frequency | 144 MHz | |

![Frequency response](docs/frequency_response.png)

### Gain vs. bandwidth

Sweeping the load resistor shows the classic tradeoff: more gain costs bandwidth, and
the gain-bandwidth product stays almost constant (it is set by gm and the load capacitor).

| Rd | Gain | -3 dB bandwidth | Gain × bandwidth |
|---:|---:|---:|---:|
| 2 kΩ | 5.66 dB | 80.6 MHz | 155 MHz |
| 4 kΩ | 11.39 dB | 41.0 MHz | 152 MHz |
| 6 kΩ | 14.62 dB | 27.8 MHz | 150 MHz |
| 8 kΩ | 16.83 dB | 21.2 MHz | 147 MHz |
| 10 kΩ | 18.49 dB | 17.2 MHz | 145 MHz |

## Run it

Requirements: `ngspice` and Python 3 (`sudo apt install ngspice`). Plots also need `matplotlib`.

```bash
make test     # simulate, compare with hand calculations, check the sweep
make plots    # regenerate docs/*.png
```

`make test` fails if the transistor leaves saturation, the simulated gain or bandwidth
disagrees with the hand calculation, the transient and AC gains differ by more than 10%,
or the gain-bandwidth tradeoff stops behaving as expected.

## Project layout

```
spice/common_source_amp.cir    netlist: bias point, DC sweep, AC sweep, transient
spice/models/generic_180nm.lib transistor models
scripts/check.py               runs ngspice, hand-calc comparison, load sweep
scripts/plot.py                makes the plots in docs/
```

## Limitations

- The transistor models are generic **SPICE Level 1** textbook values, not a foundry
  process, so absolute numbers are illustrative. Replace `generic_180nm.lib` with a real
  PDK or BSIM model for realistic results.
- Gain is modest because the load is a resistor. A current-source load or a cascode would give much more.
- No noise, distortion or temperature analysis yet.

## Ideas to extend

Source degeneration for linearity, a PMOS current-source load, a source-follower output
stage, an op-amp sensor front end, or noise and THD measurements.

## License

MIT
