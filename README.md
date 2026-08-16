# DBE EasyEDA Project

EasyEDA (Pro) hardware design files for the **DBE** board - a relay-switched fan power controller (see naming note below).

- See the **FreeCAD** repo for the enclosure.
- See the **ESPHome** repo for the firmware/config files that run on this hardware, including a [detailed write-up of the switching hardware design](https://github.com/BarBaar44/ESPHome/tree/main/DBE#hardware-relay-switched-fan-power-not-mosfet).

> **Naming note:** files in this repo are named "MosFet" for historical continuity - that's what the board was originally designed around early in development. The design actually built and in use switches fan power via a **relay** (driven by a small BC337 transistor stage), not a direct MOSFET drive. See the ESPHome repo link above for the full history of why the design moved from MOSFET → direct transistor switching → relay switching, worked out together with the Home Assistant Community forum (["3 pin pc fan control via mosfet"](https://community.home-assistant.io/t/3-pin-pc-fan-control-via-mosfet/738676)).

## Current revision

Schematic/PCB revision **15F**, PCB layout revision **6**, exported **2026-08-08**.
All three files below are matched to this same revision/date.

## Files

| File | Description |
|---|---|
| `ProPrj_DBE MosFet_15F_2026-08-08.epro2` | EasyEDA Pro project source (schematic + PCB + symbols/footprints). Open this in EasyEDA Pro to edit the design. |
| `Gerber_PCB_DBE_MosFet_6_2026-08-08.zip` | Manufacturing output: Gerbers (top/bottom copper, silkscreen, solder mask, top paste mask, board outline, document layer), PTH/NPTH/via drill files, JLCPCB flying-probe test config, and ordering instructions. Ready to send to a fab. |
| `PCB_PCB_DBE MosFet_6_2026-08-08.png` | Rendered preview image of the PCB layout for quick visual reference without opening EasyEDA. |

## Board notes

- 2-layer board (top + bottom copper only, no inner layers).
- Top paste mask only — SMD components are assembled on the top side only.
- Fan power switching is via a 12V relay (driven by a BC337 NPN transistor stage off the ESP GPIO), not a MOSFET - see the naming note above. PWM speed control is separate and unrelated to this switching stage.

## Revision history

- **2025-01-06** — Initial schematic/project upload (`ProPrj_DBE MosFet_15F_2025-01-06.epro`).
- **2026-08-08** — Refreshed project export (`.epro2`), added Gerber manufacturing package and PCB render (rev 6).
