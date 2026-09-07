# LaTeX Poster for RotateAttention (ECCV 2026 Submission #1552)

Poster for **RotateAttention: RoPE-Aware Rotation and Range Rectification for INT4 Quantized Attention in Video Generation**
(Yaofu Liu, Wanli Lan, Jinxi Li, Binhang Yuan, Harry Yang), built with the
[PS-FCN Poster LaTeX (baposter)](https://github.com/guanyingc/PS-FCN_Poster_LaTex) template style.

## Files

- `poster_landscape.tex` — main poster source (A0 landscape, baposter).
- `poster_landscape.pdf` — compiled poster.
- `baposter.cls` — poster class (slightly patched, see below).
- `algorithm2e.sty`, `Makefile` — support files from the template.
- `images/` — figures extracted from the paper (overview, rotation strategies,
  LLM-vs-video-DiT comparison, Q/K incoherence plots, quantitative table data
  and video qualitative comparisons).

## Build

```bash
cd RotateAttention_Poster_LaTex
pdflatex -interaction=nonstopmode poster_landscape.tex   # run 2-3 times
# or
make landscape
```

Requires: TeX Live (or MacTeX) with `times`, `ae`, `booktabs`, `multirow`,
`colortbl`, `multicol`, `url`, `graphicx` and PGF/TikZ.

## Note on baposter.cls

The bundled `baposter.cls` contains one small fix: the poster header builds the
eye-catcher and right-logo boxes (`\setbox\baposter@titleimage@left/right`) *before*
entering the `tikzpicture` instead of inside it. On current TeX Live, boxes that
are `\setbox`'d inside a tikzpicture lose their *text* content when later placed
with `\usebox` (image content is unaffected), so the header badges would silently
disappear without this patch. The original class is untouched in
`PS-FCN_Poster_LaTex/`.
