# South Africa Slideshow — Implementation Plan for Codex

## Phase 1 — Repository inspection

Before changing anything:

1. Inspect the repository.
2. Determine its current branch and existing files.
3. Do not delete or overwrite unrelated work.
4. Identify whether GitHub Pages is already configured.
5. Report the current state briefly.

## Phase 2 — Build the reusable foundation

Implement:

### `slideshow.html`

A self-contained viewer shell using the manifest in `data/metadata.json`.

Required:
- chapter intro screen
- photo display
- fade transition
- 5-second default duration
- play/pause
- previous/next
- progress
- volume
- fullscreen-friendly layout
- responsive desktop/tablet/mobile UI
- keyboard navigation
- graceful missing-image handling

Autoplay should begin only after a user gesture where browser audio policies require it.

### `curator.html`

Implement a fast photo-review workflow.

Required:
- load a manifest
- large image preview
- Keep / Skip
- previous / next
- progress
- location
- date
- notes
- chapter-start checkbox
- chapter title
- autosave to localStorage
- export JSON

Do not assume that a browser can write directly to `metadata.json` in a GitHub repository. Export/download is the safe browser-only behavior.

### `data/metadata.example.json`

Provide a small working sample using local assets.

### `assets/`

Provide placeholders/sample assets only where necessary. Do not commit copyrighted music or the user's private photos.

## Phase 3 — Documentation

Create:

### `README.md`

Explain:
- what the project does
- how to run locally
- how to curate
- how to prepare metadata
- how to deploy to GitHub Pages
- limitations of Google Photos integration
- where photos/music should be placed

### `docs/ARCHITECTURE.md`

Explain the separation between:
- source/import
- metadata
- curator
- viewer
- deployment

### `docs/SETUP.md`

Explain the real workflow for the user's South Africa photos.

### `CHANGELOG.md`

Start with v1.0 foundation.

## Phase 4 — Google Photos/source integration

Do not invent an API.

Investigate the currently supported Google Photos mechanisms available to a browser/static GitHub Pages application.

If direct shared-album fetching is not reliable/supported:
- keep the viewer independent from Google Photos
- support local/static image paths in the manifest
- document an import workflow as a separate concern

The application must remain usable without Google Photos API access.

## Phase 5 — Quality

Use semantic HTML where practical.

Avoid frameworks unless genuinely necessary.

Keep JavaScript modular and understandable.

Handle:
- invalid/missing metadata
- missing images
- empty chapters
- first/last photo navigation
- audio autoplay restrictions
- unsupported fullscreen
- mobile viewport behavior

Do not add analytics or tracking.

## Definition of done for v1 foundation

A developer can:
1. clone the repository
2. place a few JPG files in the documented assets directory
3. edit/copy the example metadata
4. open the slideshow
5. experience chapters, fades, timing and controls
6. open curator.html
7. review photos
8. export updated metadata
9. deploy the static project to GitHub Pages

The system should work even before real South Africa photos are imported.
