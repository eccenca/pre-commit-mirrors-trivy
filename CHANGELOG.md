# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.68.2.1] - 2026-01-19

### Changed
- Updated Trivy binary from v0.68.1 to v0.68.2
- Updated all platform binary checksums

## [0.68.1.1] - 2025-12-04

### Changed
- Updated Trivy binary from v0.67.2 to v0.68.1
- Updated all platform binary checksums

## [0.67.2.4] - 2024-11-24

### Changed
- Package name convention: `trivy_py_ecc` → `trivy-py-ecc` (using hyphens per PEP 423)

## [0.67.2.3] - 2024-11-24

### Fixed
- Updated README.md with correct package name and repository URLs

## [0.67.2.2] - 2024-11-24

### Fixed
- Fixed PyPI upload by converting Linux wheel tags to manylinux2014 tags

## [0.67.2.1] - 2024-11-24

### Added
- Initial release of `trivy_py_ecc` (rebranded from `trivy-py`)
- GitHub Actions workflow for automated PyPI publishing
- Python wrapper for Trivy v0.67.2 binary
- Support for multiple platforms:
  - Linux: x86_64, ARM64, ARM
  - macOS: ARM64 (Apple Silicon), x86_64 (Intel)
  - Windows: x64
- Pre-commit hooks: `trivy-fs` and `trivy-config`
- Automatic binary download and installation

### Changed
- Package name: `trivy-py` → `trivy_py_ecc`
- Maintainer: eccenca GmbH
- Repository: https://github.com/eccenca/pre-commit-mirrors-trivy