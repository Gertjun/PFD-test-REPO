# Subprocess Column — Premix Preparation

## Purpose

The Premix Preparation subprocess is a parallel preparation track that runs alongside Phase 2. It feeds into **Mix Eirich R09 (Step 13)** — the output of the subprocess (Weigh in Premix) connects horizontally to the right edge of Mix Eirich R09.

This is a subprocess (not part of the main flow sequence), shown with:
- **Dashed block borders** (`stroke-dasharray="5,3"`) to distinguish from primary blocks
- A subtle **green-tinted background band** with a dashed outline
- A **"PREMIX PREPARATION" header** label at the top of the band

## Blocks (top to bottom)

| Block | Height | Fill | Stroke | Text lines |
|-------|--------|------|--------|------------|
| LOD measurement Cellulose | 54 | `#e1f5ee` | `#0f6e56` | "LOD measurement" / "Cellulose" |
| Weigh in NaF & Cellulose | 40 | `#e1f5ee` | `#0f6e56` | "Weigh in NaF" / "& Cellulose" |
| Mix NaF & Cellulose | 40 | `#e1f5ee` | `#0f6e56` | "Mix NaF" / "& Cellulose" |
| Dry Premix | 40 | `#faeeda` | `#854f0b` | "Dry Premix" |
| Weigh in Premix | 40 | `#e1f5ee` | `#0f6e56` | "Weigh in Premix" |

Connectors between subprocess blocks: 28px gaps, plain grey arrows (`#a8a59e`).

## Column coordinates

```
cx  = 646
x1  = 556  (block left edge)
x2  = 736  (block right edge)
w   = 180
```

Background band:
```
x = 548, width = 196  (8px padding either side of blocks)
```

## Alignment with primary column

**Critical constraint**: Weigh in Premix (last block) must have its **vertical centre aligned** with Mix Eirich R09's vertical centre. The horizontal connection arrow runs at this shared y.

```
weigh_premix_centre_y  ==  mix_eirich_centre_y
```

The connection arrow:
```svg
<line x1="556" y1="{shared_y}" x2="441" y2="{shared_y}"
      stroke="#a8a59e" stroke-width="1.5" marker-end="url(#ar)"/>
```
(from left edge of Weigh in Premix → right edge of Mix Eirich R09)

## Computing subprocess start position

Given `mix_eirich_y` (top of Mix Eirich R09 block, h=50):

```python
mix_eirich_centre = mix_eirich_y + 25

sub_heights = [54, 40, 40, 40, 40]  # LOD to Weigh in Premix
sub_gaps    = [28, 28, 28, 28]
sub_total   = sum(sub_heights) + sum(sub_gaps)  # 326px

# Weigh in Premix centre must equal mix_eirich_centre
# Weigh in Premix top = sub_start + sub_total - 40
# Its centre = sub_start + sub_total - 40 + 20 = sub_start + sub_total - 20
# So: sub_start + sub_total - 20 == mix_eirich_centre
sub_start = mix_eirich_centre - sub_total + 20
```

## Background band positioning

```python
bg_pad = 18
band_top    = sub_start - bg_pad
band_height = sub_total + bg_pad * 2

header_y = band_top + 12  # "PREMIX PREPARATION" text
```

## Phase 2 label positioning

The Phase 2 label must appear **above the subprocess column** so the subprocess is visually "inside" Phase 2. The label is currently at y=1160, with the subprocess starting around y=1185.

If you regenerate the layout, ensure:
```
phase2_label_y  <  sub_start - 20
```

## SVG template

```svg
<!-- Background band -->
<rect x="548" y="{band_top}" width="196" height="{band_height}"
      rx="10" fill="#f5f9f4" stroke="#c8e6c0" stroke-width="1" stroke-dasharray="4,3"/>
<!-- Header -->
<text x="646" y="{header_y}" text-anchor="middle" dominant-baseline="central"
      font-family="system-ui,sans-serif" font-size="9" font-weight="500"
      letter-spacing="0.8" fill="#3b6d11" opacity="0.8">PREMIX PREPARATION</text>

<!-- LOD measurement Cellulose (h=54) -->
<rect x="556" y="{sy}" width="180" height="54" rx="8"
      fill="#e1f5ee" stroke="#0f6e56" stroke-width="1" stroke-dasharray="5,3"/>
<text x="646" y="{sy + round(54*0.36)}" ...>LOD measurement</text>
<text x="646" y="{sy + round(54*0.66)}" ...>Cellulose</text>
<line x1="646" y1="{sy+54}" x2="646" y2="{sy+54+28}" stroke="#a8a59e" marker-end="url(#ar)"/>
<!-- sy += 54 + 28 -->

<!-- Weigh in NaF & Cellulose (h=40) -->
<rect x="556" y="{sy}" width="180" height="40" rx="8"
      fill="#e1f5ee" stroke="#0f6e56" stroke-width="1" stroke-dasharray="5,3"/>
<text x="646" y="{sy + 15}" ...>Weigh in NaF</text>
<text x="646" y="{sy + 27}" ...>&amp; Cellulose</text>
<line x1="646" y1="{sy+40}" x2="646" y2="{sy+40+28}" stroke="#a8a59e" marker-end="url(#ar)"/>
<!-- sy += 40 + 28 -->

<!-- Mix NaF & Cellulose (h=40) — same pattern -->
<!-- Dry Premix (h=40) — amber colour -->
<!-- Weigh in Premix (h=40) — no trailing connector, has horizontal connection instead -->

<!-- Final connection to Mix Eirich R09 -->
<line x1="556" y1="{premix_centre_y}" x2="441" y2="{premix_centre_y}"
      stroke="#a8a59e" stroke-width="1.5" marker-end="url(#ar)"/>
```
