# DBE — PC Fan Controller Board (EasyEDA Project)

This repo contains the hardware design files (EasyEDA Pro) for **DBE**, a small circuit board that lets [Home Assistant](https://www.home-assistant.io/) turn 12V PC fans on/off and control their speed, using an ESP8266 (Wemos D1 mini) and [ESPHome](https://esphome.io/).

It was built to control radiator/case fans around the house and scales from a couple of fans up to 15 fans on one board.

**Current revision:** Schematic/PCB revision 15F, PCB layout revision 6, exported 2026-08-08.

- 🔧 Firmware / ESPHome config: see the [ESPHome repo](https://github.com/BarBaar44/ESPHome/tree/main/DBE) (also has a deeper hardware write-up).
- 📦 3D-printable enclosure: see the **FreeCAD** repo.
- 💬 Full design history / troubleshooting story: [Home Assistant Community forum thread — "3 pin pc fan control via mosfet"](https://community.home-assistant.io/t/3-pin-pc-fan-control-via-mosfet/738676).

---

## What this board actually does (plain-English version)

If you don't have an electronics background, here's the short version:

1. **A Wemos D1 mini (ESP8266)** sits on the board and runs the "brains" — it connects to your WiFi and to Home Assistant via ESPHome.
2. **PWM speed control** (making the fan spin faster/slower) is done directly: the ESP8266 sends a fast on/off signal down the fan's yellow "PWM" wire. This is standard for any PC fan with 4 wires and needs no extra components — the fan itself understands this signal.
3. **Turning the fan fully OFF** is the tricky part this whole project is about. Most PC fans are designed to always spin a little bit, even at 0% speed — some fans use the PWM wire itself as a backup ground path, so you can't just cut the power line and expect it to stop. To force a *full, clean* off (so windings don't overheat and fans don't hum at night), this board uses a **relay** — basically a tiny electronically-controlled switch — to physically disconnect the fan's 12V power.
4. The ESP8266 can't switch the relay directly (its output pins are too weak), so it uses a small **transistor (BC337)** as a "helper switch": the ESP8266 flips the transistor, the transistor flips the relay, the relay cuts or restores 12V to the fan.
5. Two small **diodes** protect the circuit from voltage spikes that happen when a coil (the relay) or the fan motor switches off — without them, those spikes can slowly damage the transistor or the ESP8266 over time.

So, in one sentence: *the ESP8266 tells a small transistor to flip a relay, which is the actual switch that turns fan power fully on or off, while fan speed is handled separately over the PWM wire.*

### Why "MosFet" is in the file names
Early versions of this project (see the forum thread) tried to switch the fans with a MOSFET, then a single transistor, before settling on the relay-based design that is actually built and working. The file names still say "MosFet" for historical continuity/consistency with older revisions — **the design in this repo uses a relay, not a MOSFET**, for the fan power switching stage.

---

## Board layout overview

![PCB layout](PCB_PCB_DBE%20MosFet_6_2026-08-08.png)

Board size: **74.0 mm × 52.6 mm**, 2-layer PCB (top + bottom copper, no inner layers), SMD parts assembled on the top side only.

| Reference | What it is | Plain-English role |
|---|---|---|
| **DC2 / P1** | Power input connectors | Where 12V comes into the board |
| **D1 mini** (with ANTENNA label) | Wemos D1 mini (ESP8266) footprint | The microcontroller running ESPHome / talking to Home Assistant over WiFi |
| **RESET / USB** | D1 mini's reset button and USB port cutouts | For flashing/reflashing firmware and manual reset, accessible at the board edge |
| **Q2** | Transistor (BC337 driver stage) | The "helper switch" the ESP8266 uses to control the relay |
| **R3** | ~1kΩ resistor | Protects the ESP8266 GPIO pin driving the transistor's base |
| **Relay** (center of board) | 12V relay, e.g. SRA-12VDC-CL | The actual switch that connects/disconnects 12V to the fans |
| **D2 / D3** | Flyback diodes (1N4007 type) | Protect the transistor and relay coil from voltage spikes when switching |
| **U3 (IN / OUT)** | Large terminal block | Lets you pass 12V + GND through to another board, or bring in/out power for a chain of boards |
| **Two 3-pin fan headers (L / D / N)** | Fan output connectors | Connect standard 3-pin PC fans (Line / Data-PWM / Ground) |
| **4-pin fan connector (S / T / L / N, near K2)** | Fan output connector with tach | Connects a 4-pin PC fan; adds a Tach (speed-sense) wire so ESPHome can read RPM |
| **R1, U5** | Small resistor/diode near the fan headers | Signal conditioning for the PWM/tach lines |

> Note: this table describes the physical layout and reference designators visible on the PCB render. For exact net names, trace widths, and drill sizes, open the `.epro2` project file in EasyEDA Pro — the PNG is a quick-reference render, not the source of truth.

---

## Fan connector pinout

| Pin | Meaning |
|---|---|
| **L** | Line — 12V power to the fan |
| **N** | Negative — ground |
| **D** (or **S**) | PWM speed control signal |
| **T** | Tach — RPM sense signal (only on the 4-pin header; optional) |

**Wiring notes learned the hard way (see forum thread for full story):**
- Common ground between the 12V supply and the ESP8266 is essential — always verify with a multimeter (should read close to 0V between the two grounds).
- The Tach (RPM) line should be pulled up to **3.3V**, not 5V, or you risk damaging the ESP8266 GPIO pin.
- Temperature sensors (DS18B20) on this board are powered from 3.3V, not 5V.
- Many "3-pin" and even "4-pin" PC fans don't fully stop spinning at 0% PWM by design — this is normal fan behavior, not a fault in this board. That's exactly why this board switches fan power with a relay instead of relying on PWM alone.

---

## Files in this repo

| File | Description |
|---|---|
| `ProPrj_DBE MosFet_15F_2026-08-08.epro2` | EasyEDA Pro project source (schematic + PCB + symbols/footprints). Open this in EasyEDA Pro to view or edit the full design. |
| `Gerber_PCB_DBE_MosFet_6_2026-08-08.zip` | Manufacturing output: Gerbers (top/bottom copper, silkscreen, solder mask, top paste mask, board outline, document layer), PTH/NPTH/via drill files, JLCPCB flying-probe test config, and ordering instructions. Ready to send to a PCB fab (e.g. JLCPCB). |
| `PCB_PCB_DBE MosFet_6_2026-08-08.png` | Rendered preview image of the PCB layout — useful for a quick look without opening EasyEDA. |

---

## Sizing the relay and transistor for your fan count

The forum thread this project came out of includes a real failure case worth repeating: an earlier revision drove **15 fans (0.16A each, ~2.5A total)** through a small transistor pair with no relay, and the transistor overheated and started to smell during testing. Lessons baked into this design:

- Don't drive fan current directly through a small signal transistor — use it only to switch a relay coil.
- Pick a relay rated for at least ~3× your expected total fan current if it isn't rated for inductive loads (a small margin of safety).
- Always include a flyback diode across the relay coil, and a separate one across the fan's power leads.

---

## Background / full troubleshooting history

This design went through several iterations — MOSFET → NPN transistor → NPN+PNP transistor pair → relay — each with real failure modes discovered along the way (wrong transistor types, short circuits from misplaced flyback diodes, fans that wouldn't fully stop, an overheating transistor at high fan counts). If you want the full story, including the reasoning and dead ends, see the original Home Assistant Community thread:

👉 [**"3 pin pc fan control via mosfet"** — community.home-assistant.io](https://community.home-assistant.io/t/3-pin-pc-fan-control-via-mosfet/738676)

---

## Revision history

- **2025-01-06** — Initial schematic/project upload (`ProPrj_DBE MosFet_15F_2025-01-06.epro`).
- **2026-08-08** — Refreshed project export (`.epro2`), added Gerber manufacturing package and PCB render (rev 6).
