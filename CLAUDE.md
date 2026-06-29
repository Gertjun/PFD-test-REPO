# NVPF Process Flow Diagram — Claude Code Project

## What this project is

A standalone HTML/SVG process flow diagram for the **NVPF (formerly VPO4) production process**, built entirely in a single HTML file. The diagram is intended to be published on a website. It was developed iteratively over a long chat session in Claude.ai and is being continued here in Claude Code.

The output file is `nvpf_process_flow.html`. This is the only deliverable — no build system, no dependencies, no frameworks. Everything is inline HTML + SVG.

---

## Project docs

| File | Purpose |
|------|---------|
| `CLAUDE.md` | This file — primary context for Claude Code |
| `docs/LAYOUT.md` | SVG coordinate system, column positions, block sizing rules |
| `docs/PROCESS_CONTENT.md` | All 33 steps, their phases, colours, and special blocks |
| `docs/BRANCH_LOGIC.md` | QC1 branch, Decision R&D sub-branch, acceptance/rejection flows |
| `docs/SUBPROCESS.md` | Premix Preparation side column details |
| `docs/CHANGELOG.md` | History of every structural change made in the original chat |

---

## Current state of the file

- **33 steps** across 3 phases, all rendered in a single `<svg viewBox="0 0 800 3426">`
- All blocks have step labels (STEP N), consistent internal text centering
- 32px gaps between blocks (some slightly varying due to patching history)
- QC5 monitoring branch off Wet milling (Phase 3, side column)
- Premix Preparation subprocess column (Phase 2, side column)
- Full QC1 branch with DRB → Decision R&D → Scrap / acceptance loop-back

## Known issues / next tasks

- Some connector gaps may still be slightly inconsistent — a full layout rebuild from the generator script in `docs/LAYOUT.md` would clean these up permanently
- The subprocess column background band positioning has been patched several times; may benefit from a clean redraw
- Consider adding rollback checkpoints as git commits

---

## How to edit

**Never patch y-coordinates manually** across multiple blocks. Any change that affects block height or position will cascade. Instead, use the layout engine pattern described in `docs/LAYOUT.md` to regenerate the SVG from scratch.

The pattern used in the last rebuild:

```python
# Accumulate y position as you go
y = 0
# emit a block
emit(f'<rect x="160" y="{y}" width="280" height="{h}" .../>')
y += h
# emit a connector
emit(f'<line x1="300" y1="{y}" x2="300" y2="{y+GAP}" .../>')
y += GAP
```

---

## File structure inside the HTML

```
<html>
  <head> — CSS only (no JS, no external deps) </head>
  <body>
    <header>  — title + subtitle
    <div class="card">
      <svg viewBox="0 0 800 3426">
        <!-- defs: markers ar, ar-a, ar-r, ar-m -->
        <!-- Phase 1 blocks (STEP 1–11) -->
        <!-- QC1 branch (acceptance left, rejection right) -->
        <!-- Subprocess column (Premix Preparation) -->
        <!-- Phase 2 blocks (STEP 12–23) -->
        <!-- Phase 3 blocks (STEP 24–33) -->
      </svg>
    </div>
    <div class="legend">
    <footer>
  </body>
</html>
```
