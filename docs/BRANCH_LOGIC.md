# Branch Logic

## QC1 Branch

After Step 11 (Quality Control 1), the flow splits into two branches:

```
Quality Control 1
       |
    [stem 20px]
       |
  ─────────────────────
  |                   |
ACCEPTANCE         REJECTION
  |                   |
  | (green)           | (red)
  |                   ▼
  |                  DRB
  |                   |
  |               Decision R&D
  |               /         \
  |          ACCEPTANCE   REJECTION
  |              |             |
  |         [dotted arc]     Scrap
  |              |
  └──────────────┘
       |
    [green arrow]
       ▼
  Phase 2 entry
  (Weigh in VPO4)
```

### LEFT branch: Acceptance path

From the split bar (cx=160), the acceptance path:
1. Acceptance badge (green pill)
2. Long vertical green line jogs down to `jog_y`
3. Horizontal line right to primary column centre (x=300)
4. Short arrow down into Phase 2 entry block (Weigh in VPO4)

The acceptance arc from Decision R&D sub-branch also terminates at `jog_y` on the primary column, merging cleanly.

### RIGHT branch: Rejection path

From the split bar (cx=440):
1. Rejection badge (red pill)
2. Arrow into **DRB** block (x=340..540, w=200, h=40)
3. Plain arrow into **Decision R&D** block (x=340..540, w=200, h=40)
4. Sub-split into two further branches

### Decision R&D sub-branch

Sub-split is symmetric around cx=440, branches at cx=380 (left) and cx=500 (right):

**LEFT sub (cx=380): Acceptance**
- Acceptance badge
- Dotted green arc path: goes straight down to `jog_y`, then horizontally left to primary column x=300
- This merges with the main acceptance path at the same `jog_y` horizontal level
- Path: `M380,{arc_start} L380,{jog_y} L300,{jog_y}` with `stroke-dasharray="5,3"` and `marker-end="url(#ar-a)"`

**RIGHT sub (cx=500): Rejection → Scrap**
- Rejection badge
- Arrow into **Scrap** block (x=440..560, w=120, h=40)
- Dead end — no further connection

### Key y coordinates (current)

These shift whenever Phase 1 blocks are resized. Always derive from QC1 block bottom:

```
qc1_bot     = QC1 block bottom
split_y     = qc1_bot + 20          (split bar)
jog_y       = split_y + 308         (horizontal merge point for acceptance paths)
p2_entry_y  = jog_y + GAP           (top of Weigh in VPO4 / Phase 2 first block)
drb_y       = split_y + 52          (DRB block top)
drd_y       = drb_y + 40 + 28       (Decision R&D block top)
ss_y        = drd_y + 40 + 18       (sub-split bar y)
scrap_y     = ss_y + 52             (Scrap block top)
```

### SVG elements for the branch

```svg
<!-- Stem from QC1 -->
<line x1="300" y1="{qc1_bot}" x2="300" y2="{split_y}" stroke="#a8a59e" .../>
<!-- Split bar -->
<line x1="160" y1="{split_y}" x2="440" y2="{split_y}" stroke="#a8a59e" .../>

<!-- LEFT: Acceptance badge -->
<line x1="160" y1="{split_y}" x2="160" y2="{split_y+14}" stroke="#3b6d11" .../>
<rect x="109" y="{split_y+14}" width="102" height="18" rx="9" fill="#e1f5ee" stroke="#3b6d11"/>
<text x="160" y="{split_y+23}" ...>ACCEPTANCE</text>
<!-- Jog down and right -->
<line x1="160" y1="{split_y+32}" x2="160" y2="{jog_y}" stroke="#3b6d11" .../>
<line x1="160" y1="{jog_y}" x2="300" y2="{jog_y}" stroke="#3b6d11" .../>
<line x1="300" y1="{jog_y}" x2="300" y2="{jog_y+GAP}" stroke="#3b6d11" marker-end="url(#ar-a)"/>

<!-- RIGHT: Rejection badge -->
<line x1="440" y1="{split_y}" x2="440" y2="{split_y+14}" stroke="#a32d2d" .../>
<rect x="395" y="{split_y+14}" width="90" height="18" rx="9" fill="#fcebeb" stroke="#a32d2d"/>
<text x="440" y="{split_y+23}" ...>REJECTION</text>
<line x1="440" y1="{split_y+32}" x2="440" y2="{split_y+52}" stroke="#a32d2d" marker-end="url(#ar-r)"/>

<!-- DRB block -->
<rect x="340" y="{drb_y}" width="200" height="40" rx="8" fill="#fcebeb" stroke="#a32d2d"/>
<text x="440" y="{drb_y+20}" ...>DRB</text>

<!-- Connector DRB → Decision R&D -->
<line x1="440" y1="{drb_y+40}" x2="440" y2="{drd_y}" stroke="#a8a59e" marker-end="url(#ar)"/>

<!-- Decision R&D block -->
<rect x="340" y="{drd_y}" width="200" height="40" rx="8" fill="#eeede9" stroke="#888780"/>
<text x="440" y="{drd_y+20}" ...>Decision R&amp;D</text>

<!-- Sub-split -->
<line x1="440" y1="{drd_y+40}" x2="440" y2="{ss_y}" stroke="#a8a59e" .../>
<line x1="380" y1="{ss_y}" x2="500" y2="{ss_y}" stroke="#a8a59e" .../>

<!-- LEFT-sub: Acceptance arc -->
<line x1="380" y1="{ss_y}" x2="380" y2="{ss_y+14}" stroke="#3b6d11" .../>
<rect x="333" y="{ss_y+14}" width="96" height="18" rx="9" fill="#e1f5ee" stroke="#3b6d11"/>
<text x="381" y="{ss_y+23}" ...>ACCEPTANCE</text>
<path d="M380,{ss_y+32} L380,{jog_y} L300,{jog_y}"
      fill="none" stroke="#3b6d11" stroke-dasharray="5,3" marker-end="url(#ar-a)"/>

<!-- RIGHT-sub: Rejection → Scrap -->
<line x1="500" y1="{ss_y}" x2="500" y2="{ss_y+14}" stroke="#a32d2d" .../>
<rect x="455" y="{ss_y+14}" width="90" height="18" rx="9" fill="#fcebeb" stroke="#a32d2d"/>
<text x="500" y="{ss_y+23}" ...>REJECTION</text>
<line x1="500" y1="{ss_y+32}" x2="500" y2="{scrap_y}" stroke="#a32d2d" marker-end="url(#ar-r)"/>
<rect x="440" y="{scrap_y}" width="120" height="40" rx="8" fill="#fcebeb" stroke="#a32d2d"/>
<text x="500" y="{scrap_y+20}" ...>Scrap</text>
```

---

## QC2 Acceptance connector

After Quality Control 2 (Step 14), an ACCEPTANCE connector leads into Step 15:

```svg
<line x1="300" y1="{qc2_bot}" x2="300" y2="{qc2_bot+8}" stroke="#3b6d11" .../>
<rect x="252" y="{qc2_bot+8}" width="96" height="18" rx="9" .../>
<text x="300" y="{qc2_bot+17}" ...>ACCEPTANCE</text>
<line x1="300" y1="{qc2_bot+26}" x2="300" y2="{step15_top}" stroke="#3b6d11" marker-end="url(#ar-a)"/>
```

Note: there is no REJECTION path shown from QC2. Rejection handling for QC2 is not depicted in the diagram.

## QC3 Acceptance connector

Same pattern after Quality Control 3 (Step 22), leading into Transport (Step 23).
