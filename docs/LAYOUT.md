# Layout Reference

## SVG viewport

```
viewBox="0 0 800 3426"   width="800"
```

The diagram is a single tall SVG. All coordinates are absolute.

---

## Block sizing constants

| Constant | Value | Used for |
|----------|-------|---------|
| `H1` | 50px | Standard single-name block |
| `H2` | 64px | 3-line block: STEP label + sub-label + name (Calcination, Drying, QC Final) |
| `H3` | 78px | 4-line block: STEP label + name line 1 + name line 2 (Load mix in Saggers) |
| `GAP` | 32px | Space between primary column blocks (connector length) |

Special cases:
- LOD measurement Cellulose (subprocess): h=54 (two-line name)
- All other subprocess blocks: h=40

---

## Primary column

| Property | Value |
|----------|-------|
| Centre x | 300 |
| Left edge x | 160 |
| Right edge x | 440 |
| Width | 280 |

### Text positioning inside blocks

For **H1 (h=50)** blocks:
- Step label y = `block_y + 17`  (font-size 9, muted)
- Name y = `block_y + 36`  (font-size 13, bold)

For **H2 (h=64)** blocks:
- Step label y = `block_y + 14`
- Sub-label y = `block_y + 32`
- Name y = `block_y + 50`

For **H3 (h=78)** blocks (Load mix in Saggers):
- Step label y = `block_y + 14`
- Name line 1 y = `block_y + 34`
- Name line 2 y = `block_y + 52`

The formula used in the layout engine:
```python
def lbl_y(by, h): return by + round(h * 0.27)
def sub_y(by, h): return by + round(h * 0.50)
def nm_y(by, h):  return by + round(h * 0.74)
def nm1_y(by, h): return by + round(h * 0.44)   # H3 name line 1
def nm2_y(by, h): return by + round(h * 0.66)   # H3 name line 2
```

### Connector arrows

Plain connector (between blocks):
```svg
<line x1="300" y1="{block_bot}" x2="300" y2="{block_bot + GAP}"
      stroke="#a8a59e" stroke-width="1.5" marker-end="url(#ar)"/>
```

Acceptance connector (after QC blocks):
```svg
<!-- short line into badge -->
<line x1="300" y1="{bot}" x2="300" y2="{bot+8}" stroke="#3b6d11" stroke-width="1.5"/>
<!-- badge -->
<rect x="252" y="{bot+8}" width="96" height="18" rx="9" fill="#e1f5ee" stroke="#3b6d11"/>
<text x="300" y="{bot+17}" ...>ACCEPTANCE</text>
<!-- line with arrowhead into next block -->
<line x1="300" y1="{bot+26}" x2="300" y2="{next_block_top}"
      stroke="#3b6d11" stroke-width="1.5" marker-end="url(#ar-a)"/>
```

---

## Branch columns (QC1 branch)

| Column | Centre x | Block x range | Width | Used for |
|--------|----------|---------------|-------|---------|
| Left branch | 160 | 60–260 | 200 | DRB, Decision R&D |
| Right branch | 440 | 340–540 | 200 | (previously Weigh in VPO4) |
| Left sub-branch | 380 | 333–429 | badge only | Acceptance arc |
| Right sub-branch | 500 | 440–560 | 120 | Scrap |

The QC1 branch starts at QC1 block bottom:
- Stem down 20px to split bar
- Split bar: x=160 to x=440
- LEFT: Acceptance badge → jog down → Phase 2 entry
- RIGHT: Rejection badge → DRB (h=40) → connector → Decision R&D (h=40) → sub-split

---

## Subprocess column (Premix Preparation)

| Property | Value |
|----------|-------|
| Centre x | 646 |
| Left edge x | 556 |
| Right edge x | 736 |
| Width | 180 |
| Background band | x=548, width=196 |

The subprocess column is positioned so **Weigh in Premix centre aligns with Mix Eirich R09 centre** (y=1491 currently). The horizontal connection arrow goes from x=556 (left edge of Weigh in Premix) to x=441 (right edge of Mix Eirich R09) at the shared y.

---

## Phase labels

Phase labels use decorative lines of fixed equal width either side:
```svg
<line x1="20"  y1="{y}" x2="220" y2="{y}" stroke="#e0ded8" stroke-width="1"/>
<text x="300"  y="{y}" ... font-size="10" letter-spacing="1" fill="#6b6963">{LABEL}</text>
<line x1="380" y1="{y}" x2="580" y2="{y}" stroke="#e0ded8" stroke-width="1"/>
```

Current phase label positions:
- Phase 1: y=16
- Phase 2: y=1160 (positioned to sit above the subprocess column)
- Phase 3: determined by Transport block (Step 23) bottom + spacing

---

## SVG marker definitions

```svg
<marker id="ar"   ...>  <!-- plain grey arrow  -->
<marker id="ar-a" ...>  <!-- acceptance green  -->
<marker id="ar-r" ...>  <!-- rejection red     -->
<marker id="ar-m" ...>  <!-- monitoring grey   -->
```

All use `context-stroke` (except ar-a, ar-r, ar-m which have hardcoded colours):
- `ar-a`: stroke `#3b6d11`
- `ar-r`: stroke `#a32d2d`
- `ar-m`: stroke `#888780`

---

## Layout engine (Python)

The cleanest way to regenerate the SVG is with the Python layout engine. The key pattern:

```python
out = []
def emit(s): out.append(s)

y = 0  # current y cursor

# Phase label
emit(phase_label(y, "PHASE 1 — ..."))
y += 32 + 8  # label height + padding

# Block
emit(blk(y, H1, fill, stroke, text_fill, 'STEP N', 'Block Name'))
y += H1  # advance past block

# Connector
emit(arr(y, y + GAP))
y += GAP  # advance past connector

# Build SVG
svg = f'<svg viewBox="0 0 800 {y + 40}" ...>\n' + '\n'.join(out) + '\n</svg>'
```

The last full rebuild script is captured in `docs/CHANGELOG.md`.
