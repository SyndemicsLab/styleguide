# styleguide

An official style guide for code written and maintained by the Syndemics Lab at Boston Medical Center.

## Purpose

These style guides provide a common resource for interacting with the lab's code bases. By conforming to them, we make reading code, setting up development environments, and documentation common across projects and allow users to quickly adopt new projects or languages. Currently, we support the languages and tools listed below:

- C++
- Python
- R
- Quarto Notebooks
- SAS

Our primary development tool is VS Code (or Positron). We are in a transition period away from RStudio in alignment with Posit's development focuses and will occassionally use SAS Studio when working with SAS databases. While we do not require specific development tools (e.g. some lab members use Emacs, others Jupyter Lab, etc.) VS Code based tools are the most widely used.

Additionally, we make heavy use of git and GitHub. All code is stored on our lab's GitHub page and (if applied) run through GitHub Actions.

## General Rules

Our language style guides are intentionally very flexible. That being said, we designed our guide on the idea that we shouldn't re-invent the wheel. That is, most of our style guides are actually based on other style guides with small adaptations that connect them together for consistency. For example, our C++ and Python guides are based on Google's style guides and our R style guide is based off of Tidyverse. The differences all center around an attempt to join together our languages. Several core commonalities are:

- Variables should always be `lower_snake_case`
- All code files should have the same header template (with the language specific comment symbol)
- All functions should explicitly return values
