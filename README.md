# Plantilla LaTeX — Memoria UANDES

Plantilla en LaTeX para memorias de título de la **Facultad de Ingeniería y Ciencias Aplicadas, Universidad de los Andes (Chile)**, con el formato (márgenes, tipografía, portada, índices, capítulos) ajustado para coincidir con el template oficial `.docx` de la facultad.

Basada en el [template original de Jaime Cisternas](https://github.com/ingUANDES/PlantillaMemoria) (2010), con las siguientes mejoras:

- Compatible con TeX Live moderno (2024+) — `minted v3+`, fontspec.
- Portada calzada al `.docx` oficial (16pt header, 18pt título, 16pt nombre, 12pt resto).
- Sin página en blanco inicial, sin firma del profesor ni copyright (no están en el `.docx` oficial).
- Capítulos del índice en MAYÚSCULAS (`1. INTRODUCCIÓN`, `BIBLIOGRAFÍA`, `A. ANEXOS`).
- Todos los títulos a 12pt.
- Índices renombrados: "Índice de Ilustraciones" / "Índice de Tablas".
- Co-guía condicional (la línea desaparece si no aplica).
- Hyperlinks sin recuadros rojos.
- `.vscode/settings.json` con la receta de compilación lista — **Cmd+Opt+B** simplemente funciona.

---

## 1. Requisitos

| Herramienta | Por qué | Cómo se instala (macOS) |
|---|---|---|
| **MacTeX** (TeX Live 2024+) | XeLaTeX, latexmk, BibTeX | `brew install --cask mactex` |
| **Pygments** | Resaltado de código en `minted` | `pip install Pygments` |
| **VS Code** | Editor recomendado | https://code.visualstudio.com |
| Extensión **LaTeX Workshop** | Compila desde el editor | Marketplace de VSCode |

Linux: usar `texlive-full` desde el package manager. Windows: instalar [MiKTeX](https://miktex.org) o TeX Live.

Verifica que el toolchain está en tu `$PATH`:

```sh
which xelatex latexmk bibtex pygmentize
```

Las cuatro rutas deben devolver algo.

---

## 2. Compilar la memoria

### Desde VS Code

1. Abre la carpeta raíz del repo en VSCode.
2. Instala **LaTeX Workshop** (autor: James Yu) si no la tienes.
3. Abre `docs/Memoria/memoria.tex`.
4. **Cmd + Opt + B** (macOS) / **Ctrl + Alt + B** (Linux/Windows) → compila.
5. El PDF se abre como pestaña al lado. Para refrescarlo manualmente: clic en el ícono "Refresh PDF".

### Desde la línea de comandos

```sh
latexmk -cd -xelatex -shell-escape -synctex=1 docs/Memoria/memoria.tex
```

El PDF queda en `docs/Memoria/memoria.pdf`.

Limpieza de artefactos auxiliares (`*.aux`, `*.log`, etc., pero conserva el PDF):

```sh
latexmk -cd -c docs/Memoria/memoria.tex
```

Limpieza total (borra también el PDF):

```sh
latexmk -cd -C docs/Memoria/memoria.tex
```

### ¿Por qué `-cd` y `-shell-escape`?

- **`-cd`**: los `\input{core/...}` son relativos al directorio de trabajo. `-cd` hace que `latexmk` entre primero a `docs/Memoria/`.
- **`-shell-escape`**: el paquete `minted` (resaltado de código) llama internamente a `pygmentize`. LaTeX no puede ejecutar binarios externos sin este flag.
- **`xelatex`** (no pdflatex): el preámbulo usa `\usepackage{fontspec}` para Times New Roman, lo que requiere XeLaTeX o LuaLaTeX.

---

## 3. Estructura del repo

```
.
├── README.md                         ← este archivo
├── CLAUDE.md                         ← contexto para Claude Code (asistente de IA)
├── Plantilla Memoria.docx            ← template oficial de UANDES (referencia visual)
├── .vscode/settings.json             ← receta de compilación para LaTeX Workshop
├── .gitignore                        ← ignora *.aux, *.log, _minted-*, etc.
└── docs/
    └── Memoria/
        ├── memoria.tex               ← ARCHIVO RAÍZ. Edita la metadata acá (líneas 5–25).
        ├── core/
        │   ├── preambulo.tex         ← paquetes, márgenes, formato de títulos
        │   ├── primeras_paginas.tex  ← portada, agradecimientos, índices, resumen
        │   ├── newapa.sty            ← estilo APA-like para citas
        │   ├── newapa.bst            ← estilo APA-like para .bbl
        │   ├── setspace.sty          ← interlineado
        │   └── julialogo.sty         ← logo de Julia (opcional)
        ├── chapters/
        │   ├── chapter01.tex         ← Introducción
        │   ├── chapter02.tex         ← Marco Teórico
        │   ├── chapter03.tex         ← Desarrollo Metodológico
        │   ├── chapter04.tex         ← Análisis de Resultados
        │   └── chapter05.tex         ← Conclusiones y Recomendaciones
        ├── attachments/
        │   └── anexo_a.tex           ← Anexos
        ├── referencias.bib           ← Bibliografía (BibTeX)
        ├── fonts/                    ← Fuente TamilMN (usada por la portada)
        └── logos/                    ← Logos UANDES
```

---

## 4. Editar la metadata de tu memoria

Toda la información personal del documento (nombre, título, carrera, profesor guía, mes, año, resumen, agradecimientos) vive en un **único bloque** al inicio de `docs/Memoria/memoria.tex`, líneas 5–25:

```latex
\newcommand{\nombreautor}{NOMBRE COMPLETO DEL ALUMNO}
\newcommand{\mes}{Mes}                                  % p. ej. Agosto
\newcommand{\anho}{Año}                                 % p. ej. 2026
\newcommand{\titulo}{Título de la Memoria}
\newcommand{\tituloprofesional}{TÍTULO PROFESIONAL EN MAYÚSCULAS}
\newcommand{\nombreprofuno}{NOMBRE DEL PROFESOR GUÍA}
\newcommand{\nombreprofcoguia}{}                        % vacío si no aplica

\newcommand{\resumen}{...}
\newcommand{\agradecimientos}{...}
\newcommand{\dedicatoria}{...}
```

**No** dupliques estos datos en `primeras_paginas.tex` — la portada los lee de aquí.

Si tu carrera tiene co-guía, ponle el nombre a `\nombreprofcoguia`. Si no, déjalo vacío `{}` y la línea desaparece automáticamente de la portada.

Ejemplos de `\tituloprofesional` por carrera:

| Carrera | `\tituloprofesional` |
|---|---|
| Ingeniería Civil en Computación | `INGENIERO CIVIL EN COMPUTACIÓN` |
| Ingeniería Civil Industrial | `INGENIERO CIVIL INDUSTRIAL` |
| Ingeniería Civil Mecánica | `INGENIERO CIVIL MECÁNICO` |
| Ingeniería Comercial | `INGENIERO COMERCIAL` |

---

## 5. Cómo escribir contenido

### Capítulos

Cada capítulo es un archivo en `docs/Memoria/chapters/chapterNN.tex`. Para agregar uno nuevo:

1. Crea `chapters/chapter06.tex` con `\chapter{Mi título}` y un `\label{ch:mititulo}`.
2. Agrega `\input{chapters/chapter06.tex}` en `memoria.tex`, en la zona donde están los demás capítulos.

### Citas y bibliografía

- Edita `docs/Memoria/referencias.bib` manualmente (formato BibTeX).
- En el texto cita con `\citeA{clave}` (no `\cite{}` — el estilo `newapa` usa `\citeA`).
- La bibliografía se genera automáticamente al compilar.

### Figuras

- Formatos: **PDF** para líneas/vectoriales, **PNG** para imágenes sintéticas, **JPG** para fotos.
- **Nombres de archivo sin espacios** (`mi-figura.pdf`, no `mi figura.pdf`).
- Inserción:

```latex
\begin{figure}[h]
  \centering
  \includegraphics[width=0.8\textwidth]{ruta/a/figura.pdf}
  \caption{Descripción de la figura.}
  \label{fig:micaption}
\end{figure}
```

El paquete `\figurename` está renombrado a "Ilustración", así que la figura saldrá rotulada `Ilustración 1: Descripción de la figura.`

### Tablas

```latex
\begin{table}[h]
  \centering
  \begin{tabular}{lcc}
    \toprule
    Columna 1 & Columna 2 & Columna 3 \\
    \midrule
    A & 1 & 2 \\
    B & 3 & 4 \\
    \bottomrule
  \end{tabular}
  \caption{Descripción.}
  \label{tab:mitabla}
\end{table}
```

### Enumeraciones

El template configura automáticamente las enumeraciones según el `.docx` oficial:

```latex
\begin{enumerate}
  \item Primer punto.       % se rotula a)
  \item Segundo punto.      % se rotula b)
  \begin{enumerate}
    \item Sub-punto.        % se rotula i)
    \item Otro sub-punto.   % se rotula ii)
  \end{enumerate}
\end{enumerate}
```

---

## 6. Formato que cumple el template UANDES

Todo lo siguiente ya está configurado en `core/preambulo.tex`. **No es necesario modificarlo** salvo que la facultad cambie los requisitos.

| Elemento | Especificación oficial | Dónde se controla |
|---|---|---|
| Tipografía | Times New Roman 12 pt | `preambulo.tex` (`\setmainfont{Times New Roman}`) |
| Interlineado | 1.5 | `memoria.tex` (`\baselinestretch{1.5}`) |
| Márgenes | 3 cm izq/der, 2.5 cm arr/abj | `preambulo.tex` (`geometry{...}`) |
| Sangría | sin sangría | `preambulo.tex` (`\parindent=0cm`) |
| Papel | A4 | `preambulo.tex` (`a4paper`) |
| Capítulos | `1. INTRODUCCIÓN` (12pt, bold, MAYÚS.) | `preambulo.tex` (titlesec) |
| Secciones | `2.1 TÍTULO` (12pt, MAYÚS., sin bold) | idem |
| Subsecciones | `3.1.1 Título` (12pt, caso natural) | idem |
| Enumeraciones | `a) b) c)` → `i) ii) iii)` | `preambulo.tex` |

---

## 7. Comparación contra el `.docx` oficial

El archivo `Plantilla Memoria.docx` (raíz del repo) es el template oficial de la Facultad. Sirve como referencia visual: ábrelo en Word para ver el formato esperado y compara con tu PDF compilado.

---

## 8. Solución de problemas

| Síntoma | Causa probable | Fix |
|---|---|---|
| `Cmd+Opt+B` no compila | Falta la extensión LaTeX Workshop | Instalarla desde el Marketplace |
| `Package minted Error: Package option "outputdir"...` | Versión vieja del template | Asegurarse de que el preámbulo dice `\usepackage{minted}` sin opciones |
| `\citeA` undefined | Falta el paquete `newapa` | Verificar que `core/newapa.sty` exista |
| Citas aparecen como `[?]` | No se corrió bibtex | Recompilar (latexmk hace las pasadas necesarias automáticamente) |
| `command not found: pygmentize` | Falta Pygments | `pip install Pygments` |
| Página en blanco antes de la portada | `\label` o contenido entre `\begin{document}` y `\input{primeras_paginas}` | No agregar nada ahí |
| Overflow horizontal en la portada | Nombre o título muy largo | Ensanchar minipage en `primeras_paginas.tex` |

---

## 9. Cómo aportar mejoras

Este template es para todos los compañeros de la carrera. Si descubres bugs o mejoras de formato:

1. Crea un branch.
2. Haz el cambio.
3. Asegúrate de que `memoria.tex` siga compilando.
4. Abre un Pull Request explicando qué cambiaste y por qué.

---

## 10. Créditos

- **Template original**: [Jaime Cisternas](mailto:jcisternas@uandes.cl), Facultad de Ingeniería UANDES, 2010.
- **Adaptación moderna** (formato `.docx`-aligned, TeX Live 2024+, VSCode setup): Manuel Tagle, 2026.
