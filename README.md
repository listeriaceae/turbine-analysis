# [Turbine: Facebook’s Service Management Platform for Stream Processing][turbine]

This repository contains the slides for a presentation on the system presented
in the paper written by Yuan Mei, Luwei Cheng, Vanish Talwar, Michael Y. Levin,
Gabriela Jacques-Silva, Nikhil Simha, Anirban Banerjee, Brian Smith, Tim
Williamson, Serhat Yilmaz, Weitao Chen and Guoqiang Jerry Chen.

## Requirements

 - `pandoc(1)`.
 - a PDF engine (e.g., `tectonic`).
 - a BibLaTeX processor (for bibliography, e.g., `biber(1)`).

## Build

```sh
pandoc turbine.md --bibliography=references.bib --biblatex --pdf-engine=tectonic -t beamer -o presentation.pdf
```

[turbine]: https://drive.google.com/file/d/18aQRHCTjS65RG_5s4bXm-tJ1l5LW6e4-/view?usp=sharing
