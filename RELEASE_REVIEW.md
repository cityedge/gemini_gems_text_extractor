# Release Review: Gem Text Extractor v1.0.0

This document summarizes the review performed before packaging Gem Text Extractor v1.0.0 for GitHub publication.

## Scope

v1.0.0 is the first public release of a browser-only utility that extracts readable UTF-8 text from Gemini Gem files saved in Google Drive.

The release package is designed to work both as a local `index.html` file and as a static GitHub Pages site.

## Reviewed areas

### Application packaging

- Renamed the GitHub Pages entry file to `index.html`.
- Kept all application CSS and JavaScript embedded in the single HTML file.
- No build step is required.
- No external JavaScript library or CDN dependency is required.
- Added the provided application screenshot as `gem_text_extractor.png`.

### User interface

- Confirmed dark theme is the default presentation.
- Confirmed Japanese / English switching is available from the header.
- Confirmed language choice is stored in `localStorage`.
- Confirmed the drop zone is full-width and visually stable.
- Confirmed multiple files can be selected.
- Confirmed each processed file receives its own result card.
- Confirmed output can be edited before saving.
- Confirmed TXT save and clipboard-copy controls are present.

### Extraction behavior

- Confirmed the parser implements Protocol Buffers-style wire parsing for varint, fixed64, length-delimited, and fixed32 fields.
- Confirmed known Gem structure handling extracts title, description, and instructions from core fields.
- Confirmed reference-file metadata extraction is optional.
- Confirmed a generic recursive UTF-8 string extraction mode is available when the known structure is not recognized.
- Confirmed extracted strings are normalized for BOM, NBSP, narrow NBSP, CRLF/CR line endings, and Unicode NFC.

### Privacy and network behavior

- Reviewed `index.html` for network-send APIs used by the app.
- No `fetch`, `XMLHttpRequest`, `WebSocket`, or `sendBeacon` call is used for Gem file content.
- File parsing is performed with browser-local JavaScript.
- UI language is the only application preference stored persistently, via `localStorage`.

### Release fix

During release review, an edge case was found in reference-link extraction: a local variable named `t` could shadow the translation helper function if a URL appeared before a recognized reference filename. The packaged `index.html` renames that local variable and removes the collision.

### Documentation

The package includes:

- `index.html`
- `gem_text_extractor.png`
- `README.md`
- `USER_GUIDE.md`
- `CHANGELOG.md`
- `RELEASE_REVIEW.md`
- `LICENSE`

## Technical checks

The release package is checked for:

- HTML/JavaScript source presence
- embedded JavaScript syntax with `node --check`
- expected repository file set
- absence of accidental absolute local file paths in the documentation
- ZIP integrity after packaging

## Known limitations

- Gemini Gem storage format is not treated as a stable public specification.
- Parsing logic is based on confirmed sample structures.
- Future Google-side format changes may reduce extraction accuracy.
- Generic fallback extraction cannot guarantee semantic field classification.
- Reference-file metadata extraction is best-effort.
- The app does not reconstruct or download referenced file contents.
- Clipboard access can depend on browser security context and permissions.

## Release assessment

The package is suitable for a v1.0.0 GitHub repository release and static GitHub Pages publication, subject to the format-compatibility limitations described above.
