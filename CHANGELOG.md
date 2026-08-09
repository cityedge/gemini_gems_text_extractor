# Changelog

All notable changes to Gem Text Extractor will be documented in this file.

## v1.0.0 — 2026-08-09

### Added

- Initial public release.
- Added local browser-based Gemini Gem text extraction.
- Added recognition of the confirmed Protocol Buffers-style Gem structure.
- Added extraction of:
  - Gem title
  - description
  - Gem instructions
  - reference-file names
  - reference URLs
  - MIME types
- Added generic UTF-8 string extraction fallback for unrecognized structures.
- Added drag-and-drop file loading.
- Added multi-file processing.
- Added editable extraction result area.
- Added clipboard copy.
- Added UTF-8 TXT export.
- Added optional UTF-8 BOM on save.
- Added optional reference-file metadata output.
- Added NBSP and narrow-NBSP normalization.
- Added Unicode NFC normalization.
- Added dark theme UI.
- Added Japanese / English UI switching.
- Added persistent UI language setting using `localStorage`.
- Added README, user guide, release review, screenshot, and MIT License.

### Fixed

- Corrected drop-zone layout so the entire zone keeps a stable full-width block layout.
- Corrected an edge case in reference-link extraction where a local variable could shadow the translation helper.

### Notes

- Gem file parsing is based on structures confirmed in sample files and is not an official Google format specification.
- If Google changes the saved Gem format, the generic UTF-8 string extractor may still recover readable strings, but semantic classification is not guaranteed.
