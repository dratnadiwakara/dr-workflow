---
description: Compiles a .tex file with pdflatex and bibtex, with 15s pause between each step.
globs: ["*.tex"]
---

# Agent: LaTeX Compiler

## Role

You compile a single LaTeX (.tex) file by running the standard sequence: pdflatex → bibtex → pdflatex → pdflatex, with **15 seconds sleep between each command**. All paths are relative to the project root.

## Input

- The .tex file to compile is the file the user attached via @ (e.g. `@other-context/bricks_in_the_age_of_clicks_v2_20260307.tex`). Use that file’s path.
- If `$ARGUMENTS` is provided, treat it as the path to the .tex file and use it instead.

## Path resolution

From the chosen .tex path (relative to project root), derive:

1. **Directory** containing the .tex file (e.g. `other-context`). If the file is at the project root, use `.` or the project root path.
2. **Filename** including `.tex` (e.g. `bricks_in_the_age_of_clicks_v2_20260307.tex`).
3. **Basename** without `.tex` (e.g. `bricks_in_the_age_of_clicks_v2_20260307`) for the `bibtex` command.

## Working directory

All commands must run from the directory that contains the .tex file so that `\input`, `\bibliography`, and `\includegraphics` resolve correctly.

## Command sequence

Run **one** Shell invocation that chains all four steps with 15-second pauses between them.

- **Windows (PowerShell)**  
  Use `;` between commands and `Start-Sleep -Seconds 15` for the pause:
  ```
  cd <tex_directory>; pdflatex <filename>.tex; Start-Sleep -Seconds 15; bibtex <basename>; Start-Sleep -Seconds 15; pdflatex <filename>.tex; Start-Sleep -Seconds 15; pdflatex <filename>.tex
  ```

- **Unix / macOS**  
  Use `&&` between commands and `sleep 15` for the pause:
  ```
  cd <tex_directory> && pdflatex <filename>.tex && sleep 15 && bibtex <basename> && sleep 15 && pdflatex <filename>.tex && sleep 15 && pdflatex <filename>.tex
  ```

Detect the environment from user/context (e.g. Windows → PowerShell). Use the project root as the starting working directory when resolving paths; pass the correct absolute or relative path to `cd` so that it points to the .tex file’s directory.

## After running

- Report success or failure.
- If successful, state the path to the generated PDF (same directory as the .tex file, basename with `.pdf` extension).
