# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an mdBook project documenting hardware accelerators and how to program them. The book covers AI accelerators, cryptographic accelerators, data compression accelerators, and the software interfaces for accessing them from user space.

## Build and Development Commands

```bash
# Build the book
mdbook build

# Serve the book locally with live reload (typically on http://localhost:3000)
mdbook serve

# Clean build artifacts
mdbook clean

# Test code samples in the book
mdbook test

# Install mdbook-mermaid preprocessor (required for diagrams)
cargo install mdbook-mermaid --version 0.14.0
```

**Note**: mdbook-mermaid version 0.14.0 is pinned due to parsing issues in v0.17.0.

## Pre-commit Validation

The repository uses pre-commit hooks for quality checks:

```bash
# Install pre-commit hooks
pre-commit install

# Run all pre-commit checks manually
pre-commit run --all-files
```

Pre-commit hooks check:
- YAML syntax
- End-of-file fixers
- Trailing whitespace
- Markdown linting (markdownlint)

## Content Structure

- [book.toml](book.toml) - mdBook configuration (MathJax support, Mermaid diagrams)
- [src/SUMMARY.md](src/SUMMARY.md) - Table of contents defining book structure
- [src/](src/) - Markdown files for each chapter/section
- `book/` - Generated output directory (not in git)

## Content Organization

The book follows a hierarchical structure defined in SUMMARY.md:
1. Introduction → Hardware Accelerators overview
2. Specific accelerator types (AI, Crypto, Data Compression)
3. How accelerators work (async submission, ordering)
4. Software interfaces (Linux user space access, VFIO, UACCE)
5. Performance considerations (estimating, load balancing, optimization)

## Writing Guidelines

- All content is Markdown (.md files)
- MathJax is available for mathematical expressions
- Mermaid diagrams are supported via preprocessor
- Follow existing naming conventions: kebab-case for filenames
- New chapters must be added to SUMMARY.md to appear in the book
- License: Creative Commons Zero v1.0 Universal (CC0)

## Deployment

The book is automatically deployed to GitHub Pages via GitHub Actions on pushes to main branch. See [.github/workflows/mdbook.yml](.github/workflows/mdbook.yml) for CI/CD configuration.
