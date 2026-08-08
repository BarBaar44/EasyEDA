# DBE EasyEDA Project

EasyEDA (Pro) hardware design files for the **DBE** MOSFET board.

- See the **FreeCAD** repo for the enclosure.
- See the **ESPHome** repo for the firmware/config files that run on this hardware.

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

## Revision history

- **2025-01-06** — Initial schematic/project upload (`ProPrj_DBE MosFet_15F_2025-01-06.epro`).
- **2026-08-08** — Refreshed project export (`.epro2`), added Gerber manufacturing package and PCB render (rev 6).
