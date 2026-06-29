# Process Content Reference

## Colour scheme

| Category | Fill | Stroke | Text |
|----------|------|--------|------|
| Loading / unloading / mixing (teal) | `#e1f5ee` | `#0f6e56` | `#085041` |
| Transport (purple) | `#eeedfe` | `#534ab7` | `#3c3489` |
| Thermal process (amber) | `#faeeda` | `#854f0b` | `#633806` |
| Milling / wet processing (blue) | `#e6f1fb` | `#185fa5` | `#0c447c` |
| Quality control / decision (grey) | `#eeede9` | `#888780` | `#3d3d3a` |
| Rejection (red) | `#fcebeb` | `#a32d2d` | `#791f1f` |
| Start pill / neutral (light grey) | `#eeede9` | `#888780` | `#3d3d3a` |
| Subprocess blocks (dashed border) | same as category colour | same | same |

Subprocess blocks additionally have `stroke-dasharray="5,3"` on their rect.

Connector colours:
- Plain arrows: `#a8a59e`
- Acceptance: `#3b6d11` (green)
- Rejection: `#a32d2d` (red)
- Monitoring (QC5 dashed): `#888780`

---

## Phase 1 — Synthesis & First Firing

| Step | Name | Colour | Height | Sub-label |
|------|------|--------|--------|-----------|
| Start | Start | Grey pill | 40 | — |
| 1 | Synthesis | Teal | H1=50 | — |
| 2 | Sagger loading with paste | Teal | H1=50 | — |
| 3 | Transport | Purple | H1=50 | — |
| 4 | Sagger loading on kiln | Teal | H1=50 | — |
| 5 | Calcination | Amber | H2=64 | HIGH-TEMP FIRING |
| 6 | Sagger unloading from kiln | Teal | H1=50 | — |
| 7 | Transport | Purple | H1=50 | — |
| 8 | Sagger unloading and paintshaker | Teal | H1=50 | — |
| 9 | Load VPO4 on MPU | Blue | H1=50 | — |
| 10 | VPO4 milling | Blue | H1=50 | — |
| 11 | Quality Control 1 | Grey | H1=50 | — |

After Step 11: **QC1 Branch** (see `docs/BRANCH_LOGIC.md`)

---

## Phase 2 — Mix & Second Firing

Phase 2 label is positioned at y=1160, above the subprocess column.

| Step | Name | Colour | Height | Notes |
|------|------|--------|--------|-------|
| 12 | Weigh in VPO4 | Teal | H1=50 | First block after QC1 branch acceptance path |
| 13 | Mix Eirich R09 | Teal | H1=50 | Subprocess connects to right edge of this block |
| 14 | Quality Control 2 | Grey | H1=50 | Followed by ACCEPTANCE connector |
| 15 | Load mix in Saggers with automatic compactor | Teal | H3=78 | Two-line name |
| 16 | Transport | Purple | H1=50 | — |
| 17 | Sagger loading on kiln | Teal | H1=50 | — |
| 18 | Calcination | Amber | H2=64 | HIGH-TEMP FIRING |
| 19 | Sagger unloading from kiln | Teal | H1=50 | — |
| 20 | Transport | Purple | H1=50 | — |
| 21 | Sagger unloading and crush | Teal | H1=50 | — |
| 22 | Quality Control 3 | Grey | H1=50 | Followed by ACCEPTANCE connector |
| 23 | Transport | Purple | H1=50 | Last Phase 2 block |

---

## Phase 3 — Wet Processing & Drying

| Step | Name | Colour | Height | Notes |
|------|------|--------|--------|-------|
| 24 | Dispersing | Blue | H1=50 | — |
| 25 | Wet milling | Blue | H1=50 | QC5 monitoring branch off right edge |
| 26 | Washing | Blue | H1=50 | — |
| 27 | Load filtercake on drying trays | Teal | H1=50 | — |
| 28 | Transport | Purple | H1=50 | — |
| 29 | Drying | Amber | H2=64 | THERMAL DRYING |
| 30 | Transport | Purple | H1=50 | — |
| 31 | Unload drying trays and crush | Teal | H1=50 | — |
| 32 | Sieving and Packing | Teal | H1=50 | — |
| 33 | Quality Control | Grey | H2=64 | FINAL INSPECTION |

---

## QC5 Monitoring branch (Phase 3, side column)

Connected via dashed grey arrow from the right edge of Wet milling (Step 25).
- Block: cx=646, x=556..736, h=40
- Fill: `#eeede9`, stroke: `#888780`
- Two text lines: "Quality Control 5" (font-size 12) + "monitoring only" (font-size 10, muted)
- No outgoing connection — monitoring only, no acceptance/rejection

```svg
<line x1="440" y1="{wet_mill_centre_y}" x2="554" y2="{wet_mill_centre_y}"
      stroke="#888780" stroke-width="1.5" stroke-dasharray="4,3" marker-end="url(#ar-m)"/>
<rect x="556" y="{wet_mill_y}" width="180" height="50" .../>
```

---

## CSS colour variables

```css
--bg:      #f9f8f6;   /* page background — warm off-white */
--surface: #ffffff;   /* card background */
--border:  #e0ded8;   /* card border */
--muted:   #6b6963;   /* footer / legend text */
```
