# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

LaTeX template for undergraduate theses ("memoria de título") at **Facultad de Ingeniería y Ciencias Aplicadas, Universidad de los Andes (UANDES, Chile)**. Adapted from the [original template (2010)](https://github.com/ingUANDES/PlantillaMemoria) to match the current official `.docx` template from the Faculty.

The thesis text is in **Spanish** (`babel [spanish, activeacute]`). When editing prose, default to Chilean academic Spanish.

## Repository layout

- `docs/Memoria/memoria.tex` — main entry point. Author/title/advisor metadata is set via `\newcommand` at the top (lines 5–25). **Edit these first**; don't put author metadata anywhere else.
- `docs/Memoria/core/preambulo.tex` — all package imports, geometry, theorem environments, color definitions, Spanish renames (`\figurename` → "Ilustración"), `titlesec` chapter/section formatting. Add new packages here.
- `docs/Memoria/core/primeras_paginas.tex` — title page (TikZ-based), agradecimientos, ToC/LoT/LoF, resumen.
- `docs/Memoria/chapters/chapterNN.tex` — one chapter per file, included from `memoria.tex`.
- `docs/Memoria/attachments/anexo_*.tex` — appendices, after `\appendix`.
- `docs/Memoria/referencias.bib` — bibliography, BibTeX. Cite with `\citep{key}` for `(Autor, Año)` or `\citet{key}` for `Autor (Año)` — uses `natbib` + Harvard `agsm` style.
- `docs/Memoria/core/{newapa.sty,newapa.bst,setspace.sty,julialogo.sty}` — bundled style files (newapa kept for reference but no longer loaded).

## Building

**Engine: XeLaTeX**, not pdflatex. The preamble uses `\usepackage{fontspec}` and `\setmainfont{Times New Roman}`, which require XeLaTeX or LuaLaTeX.

The preamble also uses `minted` (which calls `pygmentize` externally), so `--shell-escape` is required on every compile.

Build (run from repo root):

```sh
latexmk -cd -xelatex -shell-escape -synctex=1 docs/Memoria/memoria.tex
```

The `-cd` flag is **important**: `\input{core/...}` paths in `memoria.tex` are resolved relative to the current working directory, so `latexmk` must `cd` into `docs/Memoria/` before compiling.

VS Code: `.vscode/settings.json` defines the build recipe for the LaTeX Workshop extension. **Cmd+Opt+B** runs the same `latexmk` command above.

## Format design (matches the official UANDES `.docx`)

- Paper: A4. Margins: 3 cm L/R, 2.5 cm T/B.
- Font: Times New Roman 12 pt. Line spacing: 1.5. No paragraph indent.
- Title page font sizes: 16 pt (Universidad header/footer), 18 pt (title), 16 pt (author name), 12 pt (rest), 10 pt (city/month/year).
- Chapter headings: `1. INTRODUCCIÓN` — 12 pt, bold, uppercase, inline (no two-line "Capítulo 1 / Título"). Configured via `titlesec`.
- Section headings: `2.1 TÍTULO` — 12 pt, uppercase, no bold.
- Subsection headings: `3.1.1 Título` — 12 pt, natural case, no bold.
- ToC entries: chapter names rendered in MAJUSCULAS via an override of `\l@chapter`. Subsection entries keep natural case.
- Enumerations: `a) b) c)` at level 1, `i) ii) iii)` at level 2 (matches docx).
- No title-page signature page, no copyright page (the docx doesn't have them).

## Conventions worth remembering

- **Figure filenames**: no spaces. Use `mi-figura.pdf`, not `mi figura.pdf`.
- **Figures**: PDF for line drawings, PNG for synthetic pixel images, JPG for photos.
- **Bibliography**: edit `referencias.bib` by hand. Uses `natbib` + `agsm.bst` (Harvard). Cite with `\citep{key}` → `(Autor, Año)` or `\citet{key}` → `Autor (Año)`. Do **not** use `\citeA` (era de `newapa`, ya no carga).
- **Build artifacts** (`*.aux`, `*.bbl`, `*.blg`, `*.log`, `_minted-*`, etc.) are gitignored.
- **Author metadata** lives only in `memoria.tex` lines 5–25. Do not duplicate into `primeras_paginas.tex`.
- **Co-guide block** is conditional: empty `\nombreprofcoguia` → the line is hidden via `\ifdefempty`.
- **Don't reintroduce `\geometry{margin=2cm}`** anywhere; the official 3cm/2.5cm margins are set in `preambulo.tex`.
- **Don't add a `\label` between `\begin{document}` and `\input{...primeras_paginas.tex}`** — it forces a page break and creates a blank page 1.

## Common edit points

| Want to change... | Edit... |
|---|---|
| Author name / title / advisor / mes / año / carrera | `docs/Memoria/memoria.tex:5-25` |
| Chapter heading style (e.g. remove uppercase, remove bold) | `docs/Memoria/core/preambulo.tex` — `\titleformat{\chapter}` |
| Section / subsection style | idem, `\titleformat{\section}` / `\titleformat{\subsection}` |
| ToC entry style (uppercase vs natural) | idem, the `\l@chapter` override at the bottom |
| Title page layout (TikZ coordinates) | `docs/Memoria/core/primeras_paginas.tex` |
| Margins / paper / geometry | `docs/Memoria/core/preambulo.tex:14` |
| Bibliography style | `docs/Memoria/memoria.tex` (`\bibliographystyle{agsm}`) |

## Compared to the original template at github.com/ingUANDES/PlantillaMemoria

If pulling fixes from upstream, expect divergence in:

- `core/preambulo.tex` (titlesec, hidelinks, etoolbox, captionsspanish renames, `\l@chapter` override).
- `core/primeras_paginas.tex` (rewritten title page; signature & copyright pages removed).
- `memoria.tex` (added `\carrera`, `\tituloprofesional`, `\anho`, `\nombreprofcoguia`; removed `\geometry{margin=2cm}`).
- Minted's `outputdir=...` option removed (broken in TeX Live 2024+).
