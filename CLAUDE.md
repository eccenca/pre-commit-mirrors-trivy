# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

`trivy-py-ecc` is a **pip-installable wrapper around the [Trivy](https://trivy.dev/) binary**. It contains
no application source code — at install time, `setuptools-download` fetches the correct pre-built Trivy
binary for the host platform and installs it as a console entry point named `trivy` (`trivy.exe` on Windows).
It doubles as a [pre-commit](https://pre-commit.com) hook provider (`trivy-fs`, `trivy-config`).

The package is published under the eccenca org but mirrors upstream `trivy-py` (by asottile). Recent commits
merge from `upstream/main`.

## Architecture

The entire mechanism lives in two declarative files — there is no runtime Python logic to read:

- **`setup.cfg`** — the heart of the project. The `[setuptools_download]` section declares one `[trivy]`
  download entry per platform (Linux x86_64/aarch64/armv7l, macOS arm64/x86_64, Windows). Each entry pins a
  release `url`, a `sha256` checksum, and an `extract_path`. The `marker` lines select which binary installs
  on a given platform. The package version in `[metadata]` (e.g. `0.73.0.1`) encodes the wrapped Trivy
  version `0.73.0` plus a wrapper revision suffix.
- **`setup.py`** — only customizes wheel building: a `bdist_wheel` subclass marks the wheel as non-pure
  (platform-specific) and rewrites `linux` platform tags to `manylinux2014` for PyPI compatibility. The
  `py2.py3` / `none` tag reflects that the wheel carries a binary, not Python code.
- **`.pre-commit-hooks.yaml`** — defines the `trivy-fs` (filesystem vuln scan) and `trivy-config` (IaC
  misconfig scan) hooks consumers reference in their `.pre-commit-config.yaml`.

## The one task that matters: bumping the Trivy version

This repo's lifecycle is almost entirely "update to a new Trivy release" (see `CHANGELOG.md` history). To do it:

1. In `setup.cfg`, update `version` under `[metadata]` to `<trivy-version>.<wrapper-rev>`.
2. In **every** `[trivy]`/`[trivy.exe]` block, update both the `url` (release version) **and** the matching
   `sha256`. All 6 platform entries must point at the same Trivy release. Get checksums from the Trivy
   release `*.tar.gz`/`*.zip` assets — a stale or mismatched `sha256` makes install fail on that platform.
3. Add a dated entry to `CHANGELOG.md` (Keep a Changelog format, semver).

Publishing is automated: pushing a `v*` tag triggers `.github/workflows/publish.yml` to build and `twine
upload` to PyPI.

## Commands

```bash
# Run the full test matrix (installs the binary, runs pre-commit)
tox

# The "tests" — tox's [testenv] just verifies the downloaded binary runs:
trivy --version
trivy --help

# Lint / format (also the pre-commit testenv)
pre-commit run --all-files

# Local install to test the download mechanism end-to-end
pip install .
```

There is no unit-test suite; `tox` validates that the platform binary downloads and executes, then runs
pre-commit (flake8, autopep8, pyupgrade, reorder-python-imports, etc. — config in `.pre-commit-config.yaml`).

## Conventions

- Python ≥ 3.9; code style enforced entirely by pre-commit. New `.py` files must start with
  `from __future__ import annotations` (added automatically by `reorder-python-imports`).
- This is a mirror — prefer keeping `setup.py`/wheel-tagging logic aligned with upstream `trivy-py` unless an
  eccenca-specific change is intended.
