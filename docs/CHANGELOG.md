# Changelog

Chronological record of all structural changes made during the original Claude.ai chat session.

---

## Initial build

- Created HTML block flow diagram with all Phase 1 blocks (Steps 1–11) as HTML `div` elements
- Styled with CSS variables for colour themes
- Added QC1 branch with Rejection (→ DRB) and Acceptance paths
- Added DRB → Decision R&D → Scrap / Acceptance arc sub-branch

## Added Phase 2 and Phase 3

Steps 12–23 (Phase 2: Mix & Second Firing) and Steps 24–33 (Phase 3: Wet Processing & Drying) added as HTML flow elements below the SVG branch section.

Phase connectors (Phase 1→2, Phase 2→3) added with short SVG connector stubs and HTML phase labels.

## Converted to single SVG

All HTML block elements replaced with a unified SVG, eliminating layout inconsistencies between HTML and SVG coordinates. This was the major architectural change that made all subsequent precise positioning possible.

Key coordinates established at this point:
- viewBox: `0 0 800 2856`
- Primary column: cx=300, x=160..440, w=280
- Block heights: h=40 (standard), h=54 (2-line)
- Gap: 28px

## Added subprocess column (Premix Preparation)

Five blocks added in a side column (cx=646) alongside Phase 2:
1. LOD measurement Cellulose
2. Weigh in NaF & Cellulose
3. Mix NaF & Cellulose
4. Dry Premix
5. Weigh in Premix

Connection: Weigh in Premix → left edge of Mix Eirich R09 (horizontal arrow)

Background band with dashed border and "PREMIX PREPARATION" header added.

## Added QC5 monitoring branch

Quality Control 5 added as a side branch off Wet milling (Step 25) via a dashed grey arrow. Monitoring only — no outgoing connection.

## Block height increase

All primary column blocks increased from h=40 → h=50 (standard) and h=54 → h=64 (two-line). Text positions updated to maintain centering.

Load mix in Saggers (Step 15) increased to h=78 to accommodate 3-line text.
LOD measurement Cellulose (subprocess) increased to h=54.

## Step labels added

All 33 primary-column blocks given a step label (STEP N) in the muted stroke colour, positioned at block_y + 17 for standard blocks.

## Gap increase to 32px

Connector gap increased from 28px to 32px. The SVG was regenerated from scratch using a Python layout engine to ensure all y-coordinates were correct.

## Phase 2 label repositioning

Phase 2 label moved to y=1160 (above the subprocess column) so the entire Premix Preparation subprocess sits visually inside Phase 2.

Phase 2 label is also shown between Step 12 and Step 13 in the flow (removed the duplicate after the label-above-subprocess approach was chosen).

## QC1 branch swap

Acceptance and Rejection branches from QC1 were swapped:
- **LEFT branch**: now ACCEPTANCE (green jog down to Phase 2)
- **RIGHT branch**: now REJECTION → DRB → Decision R&D → sub-branches

## Rollback reference points

The user designated two versions as rollback references during the session:
1. After first complete three-phase vertical layout with subprocess and QC5
2. After adding step labels and increasing block heights (final clean version)

The current file (`nvpf_process_flow.html`) represents the state after all changes above.

---

## Layout generator script (last full rebuild)

The Python script used for the last full SVG rebuild. Copy this into a new file and run it to regenerate a clean SVG.

```python
H1 = 50; H2 = 64; H3 = 78; GAP = 32; CX = 300; BX = 160; BW = 280
TA = 'text-anchor="middle" dominant-baseline="central" font-family="system-ui,sans-serif"'
out = []
def emit(s): out.append(s)

def lbl_y(by, h): return by + round(h * 0.27)
def sub_y(by, h): return by + round(h * 0.50)
def nm_y(by, h):  return by + round(h * 0.74)
def nm1_y(by, h): return by + round(h * 0.44)
def nm2_y(by, h): return by + round(h * 0.66)

def phase_label(y, text):
    emit(f'<line x1="20" y1="{y}" x2="220" y2="{y}" stroke="#e0ded8" stroke-width="1"/>')
    emit(f'<text x="{CX}" y="{y}" {TA} font-size="10" font-weight="500" letter-spacing="1" fill="#6b6963">{text}</text>')
    emit(f'<line x1="380" y1="{y}" x2="580" y2="{y}" stroke="#e0ded8" stroke-width="1"/>')

def blk(y, h, fill, stroke, tfill, step, name, sub=None, name2=None):
    emit(f'<rect x="{BX}" y="{y}" width="{BW}" height="{h}" rx="8" fill="{fill}" stroke="{stroke}" stroke-width="1"/>')
    emit(f'<text x="{CX}" y="{lbl_y(y,h)}" {TA} font-size="9" letter-spacing=".6" fill="{stroke}" opacity=".75">{step}</text>')
    if sub and not name2:
        emit(f'<text x="{CX}" y="{sub_y(y,h)}" {TA} font-size="9" letter-spacing=".6" fill="{stroke}" opacity=".75">{sub}</text>')
        emit(f'<text x="{CX}" y="{nm_y(y,h)}" {TA} font-size="13" font-weight="500" fill="{tfill}">{name}</text>')
    elif name2:
        emit(f'<text x="{CX}" y="{nm1_y(y,h)}" {TA} font-size="13" font-weight="500" fill="{tfill}">{name}</text>')
        emit(f'<text x="{CX}" y="{nm2_y(y,h)}" {TA} font-size="13" font-weight="500" fill="{tfill}">{name2}</text>')
    else:
        emit(f'<text x="{CX}" y="{nm_y(y,h)}" {TA} font-size="13" font-weight="500" fill="{tfill}">{name}</text>')

def arr(y1, y2, col="#a8a59e", mk="url(#ar)"):
    emit(f'<line x1="{CX}" y1="{y1}" x2="{CX}" y2="{y2}" stroke="{col}" stroke-width="1.5" marker-end="{mk}"/>')

# Usage:
# y = 0
# phase_label(16, "PHASE 1 — SYNTHESIS &amp; FIRST FIRING")
# y = 40
# blk(y, H1, '#e1f5ee', '#0f6e56', '#085041', 'STEP 1', 'Synthesis')
# arr(y+H1, y+H1+GAP); y += H1+GAP
# ... etc
# svg = f'<svg viewBox="0 0 800 {y+40}" width="800" ...>\n<defs>...</defs>\n' + '\n'.join(out) + '\n</svg>'
```
