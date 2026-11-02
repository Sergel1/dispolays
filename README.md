# Dispolays - Week 4 Hardware Build

## Project overview

Dispolays is a compact battery-powered e-paper device built around an ESP32-C3 SuperMini. Week 4 focused on the custom electronics carrier PCB and the first parametric enclosure revision.

The current design includes:

- ESP32-C3 SuperMini on removable 1x8 socket strips
- 1.54-inch, 200x200 black-and-white e-paper display interface
- MCP73831 single-cell LiPo charger
- SS14 Schottky battery power path
- Battery-voltage sensing circuit
- Two side buttons
- Four electrical test points
- Two-layer custom PCB
- Parametric PETG enclosure model in SolidWorks

> **Project status:** Digital design/prototype stage. No physical components have been received or tested yet. The PCB, component footprints, enclosure fit, charging circuit, and firmware still require physical verification before manufacturing or battery connection.

---

## Week 4 results

### PCB and schematic

The KiCad design contains:

- `U1` - ESP32-C3 SuperMini
- `U2` - MCP73831T-2ACI/OT, SOT-23-5, 4.2 V LiPo charger
- `D1` - SS14 Schottky diode, SMA package
- `J1` - 2-pin JST-PH battery connector
- `J2` - 1x8, 2.54 mm e-paper connection header
- `SW1`, `SW2` - side-actuated buttons
- `R1`, `R2` - 1 MOhm battery divider
- `R3`, `R4` - 100 kOhm external button pull-ups
- `R5` - 5.1 kOhm charger-programming resistor
- `C1`, `C2` - 4.7 uF charger capacitors
- `C3` - 100 nF battery-sense filter capacitor
- `C4` - 100 uF bulk capacitor
- `TP1` - 3V3
- `TP2` - GND
- `TP3` - BAT+
- `TP4` - 5V

The PCB is approximately `38 x 30 x 1.6 mm`, uses two copper layers, and includes three M2 mechanical mounting holes.

### GPIO assignment

| Function | ESP32-C3 GPIO |
|---|---:|
| VBAT_SENSE | GPIO1 |
| BTN_A | GPIO3 |
| BTN_B | GPIO4 |
| EPD_DC | GPIO5 |
| EPD_SCK | GPIO6 |
| EPD_MOSI | GPIO7 |
| EPD_CS | GPIO10 |
| EPD_BUSY | GPIO20 |
| EPD_RST | GPIO21 |

The following pins are intentionally unused:

```text
GPIO0
GPIO2
GPIO8
GPIO9
```

### E-paper connector pinout

| J2 pin | Signal |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | DIN / EPD_MOSI |
| 4 | CLK / EPD_SCK |
| 5 | CS / EPD_CS |
| 6 | DC / EPD_DC |
| 7 | RST / EPD_RST |
| 8 | BUSY / EPD_BUSY |

### Power design

The MCP73831 charging current is set by `R5 = 5.1 kOhm`, targeting approximately `196 mA`.

The battery path is:

```text
BAT+ -> SS14 anode
SS14 cathode -> 5V rail
```

Battery measurement is taken before the diode:

```text
BAT+ -> R1 1 MOhm -> VBAT_SENSE -> R2 1 MOhm -> GND
                              |
                           C3 100 nF
                              |
                             GND
```

### PCB routing

- Signal traces: approximately `0.25 mm`
- Power traces: approximately `0.50 mm`
- GND zones on both `F.Cu` and `B.Cu`
- GND stitching vias between layers
- Antenna rule area blocks copper zones, tracks, and vias
- Current minimum measured antenna clearance: approximately `7.5 mm`

The design target is at least `8 mm`; the enclosure keeps the battery, display cable, screws, and other conductive objects away from the antenna region.

---

## Enclosure design

The enclosure is being developed parametrically in SolidWorks and is intended for PETG printing.

### Current Version 1 envelope

```text
Width:  47.0 mm
Height: 88.0 mm
Depth:  21.5 mm
```

### Current construction

- 2.0 mm side walls
- 1.6 mm front wall
- 2.0 mm rear panel target
- 29 x 29 mm display window
- Upper display and battery zone
- Lower PCB and electronics zone
- Internal separator rib
- Display cable slot and enlarged J2 connector clearance
- Three PCB mounting bosses matching the frozen PCB holes
- Right-side switch slots
- Oversized Version 1 USB-C opening and external cable-housing recess
- Separate back shell planned with a provisional battery pocket

### Selected display target

The enclosure is being designed around a Waveshare-style 1.54-inch black-and-white e-paper module:

```text
Driver-board outline: 48 x 33 mm
Resolution:           200 x 200
Active area:          approximately 27.6 x 27.6 mm
Interface:            SPI
```

The display orientation is rotated inside the portrait enclosure so the 33 mm dimension runs across the enclosure width.

### Important enclosure assumptions

The following values are provisional until physical components arrive:

- ESP32-C3 SuperMini outline and pin-row spacing
- Socket and assembled SuperMini stack height
- USB-C connector centre height
- Display thickness and active-area offset
- Display cable and connector shape
- Side-switch actuator dimensions
- LiPo dimensions and connector polarity
- C4 height
- Battery-pocket dimensions


## Files included

### KiCad source

Include the complete KiCad project, not only Gerbers:

```text
*.kicad_pro
*.kicad_sch
*.kicad_pcb
project symbol and footprint libraries
```

### Fabrication files

Includes:

```text
F.Cu Gerber
B.Cu Gerber
F.Mask Gerber
B.Mask Gerber
F.Silkscreen Gerber
B.Silkscreen Gerber, if used
Edge.Cuts Gerber
PTH drill file
NPTH drill file
```

### CAD source

Includes:

```text
Native SolidWorks part files
Native SolidWorks assembly
PCB STEP export
Enclosure STEP export, still unfinished
Front-shell STL, still finished
Back-shell STL, finished
Assembly render/ screenshot
```


---

## Verification completed

- Custom ESP32-C3 SuperMini schematic symbol created
- Custom provisional SuperMini footprint created
- MCP73831 changed to the five-pin SOT-23 package
- Schematic ERC reviewed
- PCB DRC reviewed
- No unconnected nets reported after zone refill
- GND pours added to both copper layers
- Antenna keepout added
- PCB imported into SolidWorks enclosure assembly
- Three PCB mounting bosses aligned with PCB mounting holes
- J2/separator interference identified and cleared
- Side-button openings added
- USB-C access opening added

---

## Verification still required

Before PCB ordering:

- [ ] Purchase and measure the real ESP32-C3 SuperMini
- [ ] Print the SuperMini footprint at 1:1 scale and test all 16 pins
- [ ] Confirm the selected 1x8 female socket height and fit
- [ ] Select switches that exactly match the frozen PCB footprints
- [ ] Confirm the physical display harness fits J2
- [ ] Verify J1 battery connector polarity
- [ ] Select the exact protected single-cell LiPo
- [ ] Select a 100 uF capacitor matching the PCB footprint
- [ ] Verify all enclosure-to-PCB interference results
- [ ] Complete the back shell and provisional battery pocket
- [ ] Export final STEP and STL files
- [ ] Inspect Gerbers and drill files in a Gerber viewer

Before connecting a LiPo:

- [ ] Check continuity between 5V, 3V3, BAT+, and GND
- [ ] Assemble and test without the SuperMini or battery first
- [ ] Apply current-limited 5 V at TP4
- [ ] Confirm U2 does not overheat
- [ ] Verify BAT+ is approximately 4.2 V with no battery
- [ ] Confirm battery-plug polarity with a multimeter
- [ ] Measure initial charge current, target approximately 196 mA
- [ ] Verify the SS14 power-path voltage drop

Firmware / breadboard tests still required:

- [ ] T-00 SuperMini identity, sleep current, and wireless test
- [ ] T-01 e-paper full-refresh test
- [ ] T-02 partial refresh and ghosting test
- [ ] T-03 button wake test
- [ ] T-04 battery ADC test

---

## Known limitations

1. No physical parts were available during Week 4.
2. The SuperMini footprint is provisional and must be verified before fabrication.
3. The enclosure uses conservative Version 1 dimensions and is not print-ready as a final product.
4. The battery, switches, socket strips, display harness, and C4 package are not yet physically verified.
5. The PCB uses three mounting holes instead of four.
7. The PCB STEP model does not fully represent the removable SuperMini socket stack.
---

## Safety note

This design was costume made please beware and dont try home unless yu ready to risk i all (praying hand meoji but i cant add it through my laptop forgor)

---

## Author

Sergel Hasabella  
Week 4 hardware design submission  

