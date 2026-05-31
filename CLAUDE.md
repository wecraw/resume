# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repo holds Will Crawford's resume in two formats:
- `WillCrawford_Resume_2024.tex` — the canonical source (LaTeX)
- `WillCrawford_Resume_2024.pdf` — the compiled output, checked in alongside the source
- `README.md` — a Markdown mirror of the resume content

When editing resume content, update **both** the `.tex` file and `README.md` to keep them in sync.

## Building

Compile the LaTeX source to PDF with:

```bash
pdflatex WillCrawford_Resume_2024.tex
```

This requires a LaTeX distribution (e.g. MacTeX or TeX Live). After compiling, the updated `.pdf` should be committed alongside any `.tex` changes.

## LaTeX Template Structure

The `.tex` file is based on [Jake Gutierrez's resume template](https://github.com/sb2nov/resume). Key custom commands defined in the preamble:

- `\resumeSubheading{title}{date}{role}{date}` — job/education entry with two-line header
- `\resumeSubSubheading{role}{date}` — continuation role under same employer (no repeated company name)
- `\resumeProjectHeading{title+tech}{date}` — project entry
- `\resumeItem{text}` — single bullet point
- `\resumeSubHeadingListStart` / `\resumeSubHeadingListEnd` — wraps groups of subheadings
- `\resumeItemListStart` / `\resumeItemListEnd` — wraps bullet lists under a subheading

Sections follow the order: Experience → Projects → Education → Technical Skills.
