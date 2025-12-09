# Syndemics Lab Notebook Project Style Guide

The motivation for the Syndemics Lab Notebook Project Style Guide is public deployment and open contribution to our code. We intend to distribute and utilize open source software and this style guide is the standard for analysis notebook projects in our repositories. We hope this style guide makes contribution to our projects easy. This style guide in particular focuses less on syntaxes (although it does contain some unqiue details) but instead more on the project setup. This is because both Python and R can be used in our notebook projects. Thus, all R and Python style guide syntax rules apply here too.

## Project Tooling

Our notebooks are built using [Quarto](https://quarto.org/). This is so that they can run Python, R, Julia, etc. and easily be rendered through pandoc into whatever format we desire.

## Repo Structure

Notebook repositories follow a very particular structure. There is a public template [provided](https://github.com/SyndemicsLab/analysis-template-R) on our lab GitHub for R notebook repositories if so desired.

All repositories must have the following directories:

- `notebooks/` - Where the analysis projects take place
- `src/` - can be replaced with R, Python, or a language if only using a specific language with the notebooks
- `data/raw/` - Where to store raw data. Should have a `.gitkeep` file and the rest of the folder listed in the `.gitignore` to keep data from being folded into the repo.
- `data/processed/` - Where to store processed data. Should have a `.gitkeep` file and the rest of the folder listed in the `.gitignore` to keep data from being folded into the repo.
- `output/` - Where to store results printed from the analysis. Should have a `.gitkeep` file and the rest of the folder listed in the `.gitignore` to keep data from being folded into the repo.

The following files are expected to exist in the root of the repository:

- `.gitignore`
- `_quarto.yml`
- `LICENSE.md`
- `README.md`

If this is an R notebook analysis, we also expect:

- `.lintr.R`
- `PROJECT_NAME.Rproj`

## Notebooks

All notebooks should be combinations of a single programming language and Markdown. Every programming cell should be immediately preceeded by a Markdown cell explaining the behavior and intention of the code.

### Setup Cell

The top of all notebooks should be configured to work with Quarto. They should include the notebook title, the author, and the date in YYYY-MM-DD format. The date should reflect the day of the last update.

```yml
---
title: "My Quarto Notebook"
author: "Matthew Carroll"
date: "2025-10-08"
---
```

### Markdown

All Markdown cells should follow the [GitHub Markdown Style Guide](https://google.github.io/styleguide/docguide/style.html).

#### Mathematics

In addition to the GitHub Markdown style requirements, we require that any math
outside of code blocks be contained in inline LaTeX math notation.

### Programming Languages

All programming cells should follow standard syntax rules laid out in their own style guide. The difference inside notebooks is that instead of building for a package, all code should be written as a script with global variables.
