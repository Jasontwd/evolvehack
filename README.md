# evolvehack

**Open-source Flipsky / VESC conversion for the Evolve Hadean electric skateboard.**

This repo documents how a 2022 Evolve Hadean with a failed battery PCB was brought back to life by replacing the proprietary Evolve electronics with open, repairable parts: a Flipsky Dual FSESC6.7 Plus motor controller, a Flipsky VX4 Pro remote and a VESC-compatible BMS, keeping the original deck, trucks and cells.

If your Hadean cuts out mid-ride, powers on only sometimes, or has been quoted a whole new battery for what looks like a connection fault, this is for you.

> **Status: work in progress.** The fault is diagnosed, the cells have been rewired, and the controller, motors and remote are on the board. Wiring, BMS and configuration are being written up as the build progresses. See [Roadmap](#roadmap).

---

## Why this project exists

The board is a second-hand 2022 Hadean with under 900 km on it and a healthy set of cells. It died anyway, and the failure was not the cells, the motors or the controller. It was the circuit board the cells are mounted to.

The stock pack is a single assembly: cells, BMS, power switch input and the mains connector to the motor controller all live on one PCB. When that board cracks, the official fix is a replacement battery. The board was out of warranty, the local repair queue was around two months, and a new battery costs roughly three times what the open-source parts do.

So the board gets an open drivetrain instead: parts that can be bought from more than one supplier, configured with free tools, and repaired one piece at a time.

## The fault

### Symptoms, in the order they showed up

| When | What happened |
|---|---|
| Oct 2025 | Battery cut out at around 10 km range on two rides in a row. |
| Oct 2025 | Pack opened: 12S4P of 21700 cells (16 Ah). Parallel groups measured about 4.18 V each, yet the pack read only 40.8 V with the charger showing green. A healthy full 12S pack should read about 50.4 V. |
| Nov 2025 | Small JST connector from the power switch to the battery PCB found working loose with vibration. Hot glue held it for a couple of test rides, then it failed again. |
| Nov 2025 | Bad joint found at the switch end of the power switch lead. Cut back, re-soldered and heat-shrunk; switch tested good for continuity. Board still would not power on, although the red LED on the BMS was lit. |
| Nov 2025 | Board intermittently powered up, ran its LED sequence, then shut off again with the BMS LED still on. |
| Dec 2025 | Root cause found (below). |

### Root cause

**Cracked internal traces in the main battery PCB, around cell groups B8 and B9.**

The cracks are inside the board's copper pathways, not at a solder joint, so they are invisible from the surface. Flexing the end of the battery assembly by hand switches the board on and off. On the road, deck flex and vibration do the same thing, which explains every symptom above: random cut-outs, the low pack voltage reading across a broken series link, and a board that works on the bench and dies on the ride.

The power switch lead and its JST connector were real faults too, and worth checking first because they are quick to fix. They were not the whole story.

### How to check your own board

1. **Measure each parallel group, then the whole pack.** If the groups add up to much more than the pack voltage, a series connection is open somewhere between them.
2. **Tug-test the power switch wires** at the switch end, one at a time, and check the small JST connector on the battery PCB is fully seated.
3. **Flex test.** With the pack out and a meter across the main output, gently flex the PCB along its length. Voltage dropping in and out points to a cracked trace.
4. **Narrow it down.** Probe group to group along the series chain while flexing to find which link is breaking.

## The upgrade

### What stays

- Deck, trucks and wheels
- The original 21700 cells, rewired as a 12S4P pack off the cracked PCB

### What goes

- Evolve battery PCB with integrated BMS
- Evolve motor controller
- Evolve remote and its receiver (the stock remote only talks to the stock controller)
- Stock motors

### What replaces it

| Part | Replacement | Notes |
|---|---|---|
| Motor controller | Flipsky Dual FSESC6.7 Plus with aluminium case | VESC 6-based dual controller, one unit drives both motors |
| Motors | 2 x Glide 6374, 190 KV, 3500 W | From glide-esk8.co.nz |
| Remote | Flipsky VX4 Pro | With its matching receiver |
| BMS | VESC-compatible 12S smart BMS | Separate, replaceable board. Model to be confirmed |
| Cell interconnects | Rewired 12S4P pack | No structural PCB carrying series current |
| Power switch | To be confirmed | Anti-spark switch or loop key |
| Configuration | [VESC Tool](https://vesc-project.com/vesc_tool) | Free, open source |

| Controller | Motor | Remote |
|---|---|---|
| ![Flipsky Dual FSESC6.7 Plus](images/flipsky-dual-fsesc67-plus.jpg) | ![Glide 6374 190 KV motor](images/glide-6374-190kv-motor.jpg) | ![Flipsky VX4 Pro remote](images/flipsky-vx4-pro-remote.jpg) |

### Controller ports

The FSESC6.7 Plus case is labelled with its pinouts, which makes wiring far easier than the stock controller. As printed on the case:

| Port | Pins | Used for |
|---|---|---|
| Hall sensor (one per motor) | 5V, TMP, H1, H2, H3, GND | Motor hall sensors and temperature |
| Receiver | PPM, 5V, GND, TX, RX, BAT | Remote receiver |
| COMM (one per side) | ADC3, ADC2, TX, RX, ADC1, GND, 3.3V, 5V | UART and analogue inputs |
| CAN | 5V, CH, CL, GND | CAN bus accessories, such as a BMS |
| Switch | F-, NO, B+, GND, 5V | Power button |
| USB1 / USB2 | Micro USB | VESC Tool, one port per motor side |

A full bill of materials with part numbers, suppliers and prices will be added once the build is finalised.

## Roadmap

- [x] Diagnose the fault
- [x] Remove cells from the cracked PCB and rewire as 12S4P
- [x] Fit controller, motors and remote
- [ ] Bill of materials with part numbers and costs
- [ ] Wiring diagram: pack, BMS, ESC, motors, charge port
- [ ] Motor phase and hall sensor wiring for the Glide 6374 motors
- [ ] 3D-printable mounts and enclosure parts (STL / STEP)
- [ ] VESC Tool motor detection results and configuration files
- [ ] Remote pairing and throttle curve setup
- [ ] Photos of the teardown and the finished build
- [ ] Ride testing and range figures

## Also in this repo

- `LarsonScannerTrinketM0` is an Arduino sketch for a 16-pixel NeoPixel "Larson scanner" light bar on an Adafruit Trinket M0, adapted from Adafruit's example (MIT). Board lighting, unrelated to the drivetrain.

## Safety

This is high-energy lithium work. A 12S4P pack of 21700 cells can deliver hundreds of amps into a short circuit and will start a fire if mishandled.

- Do not attempt this unless you are comfortable building and repairing lithium packs.
- Insulate everything, work on one connection at a time, and keep metal tools away from exposed cell terminals.
- Fuse the pack and use a BMS with over-current, over-voltage and under-voltage protection.
- Charge somewhere you can deal with a fire, and never unattended while the build is unproven.
- Set conservative current limits first and test at low speed, with protective gear, before riding normally.

Opening the battery or replacing the electronics ends any Evolve warranty or service support for the board. You do this at your own risk.

## Contributing

Issues and pull requests are welcome, especially from other Hadean owners: fault reports, measurements, pinouts, photos of your battery PCB, and VESC configs that work.

## Disclaimer

This is an independent project. It is not affiliated with, endorsed by or supported by Evolve Skateboards, Flipsky or the VESC Project. Product names are trademarks of their respective owners and are used only to describe compatibility.
