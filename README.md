# MiniPdf for Python

MiniPdf converts basic DOCX, XLSX, and PPTX content to PDF 1.4 without
requiring Microsoft Office or LibreOffice. Conversion runs through the Rust
MiniPdf engine in `minipdf-rs` via the PyO3 extension `minipdf._native`, which
the package build backend compiles from [`native/`](native/). A pure Python
renderer with reduced feature coverage remains as a fallback for source-tree
runs where the extension has not been built.

The package requires Python 3.10 or later, and building it requires a Rust
toolchain.

## Native Rust Engine

`minipdf._native` is a PyO3 extension that delegates conversion to the Rust
engine in `minipdf-rs`. When the module is importable, the Python API uses it
automatically and the pure Python renderers are only used as a fallback.

Installing the package builds the extension:

```bash
pip install .
```

To rebuild only the extension into the package source tree, use PowerShell:

```powershell
pip install -e ".[dev]"
```

Run ``pytest tests``, ``ruff check src tests``, ``ruff format --check src tests``, and ``mypy src tests`` after building.

## Install

```bash
pip install minipdf
```

## Python API

```python
import minipdf

minipdf.convert_to_pdf("report.docx", "report.pdf")
pdf_bytes = minipdf.convert_to_pdf_bytes("report.docx")
```

## Command Line

```bash
minipdf report.docx
minipdf workbook.xlsx -o workbook.pdf
minipdf slides.pptx -o slides.pdf
minipdf convert report.docx -o report.pdf
minipdf report.docx --paper-size a4
minipdf report.docx --page-width 400 --page-height 500
```

## Initial Scope

Version 0.1 supports document page geometry, margins, paragraphs, explicit page
breaks, and basic bold, italic, and font-size run formatting. Text currently
uses PDF built-in Latin fonts. Tables, images, embedded fonts, CJK/RTL shaping,
headers, footers, and lists are not yet supported. XLSX rendering currently
extracts common cell value types into text rows, while PPTX rendering extracts
paragraph text and preserves slide page dimensions and boundaries.

## Repository migration

This implementation now lives in [https://github.com/PdfLibrarys/minipdf-python](https://github.com/PdfLibrarys/minipdf-python). Its language-specific
Git history was preserved from [MiniPdf](https://github.com/mini-software/MiniPdf)
(source commit `862ab485fdf8c5b3d3ec76afb6145f4ff68bf987`).
The original repository maintains the .NET implementation. Open issues and
pull requests for this implementation in this repository.

The native engine is fetched from [minipdf-rs](https://github.com/PdfLibrarys/minipdf-rs)
using an immutable Git revision in the Cargo manifest. No sibling checkout is
required. Updating the engine requires changing that revision and regenerating
the Cargo lockfile.
