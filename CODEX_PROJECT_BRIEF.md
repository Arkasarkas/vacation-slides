# South Africa Slideshow — Codex Project Brief

## Goal

Build a reusable web-based interactive slideshow system for the user's South Africa vacation photos.

The project has two eventual outputs from the same curated photo library:

1. A polished interactive slideshow for TV, iPad, desktop and mobile.
2. A future printed photo album.

For the current phase, focus on the slideshow and curator. Do not build the printed album yet, but preserve enough selection metadata to support it later.

## Product concept

The important design principle is:

Google Drive / photo source
→ import/analyze
→ one curated photo library
→ KEEP/SKIP decisions + metadata
→ slideshow viewer now
→ album builder later

The user wants to avoid manually sorting the same photos twice.

## Current v1 priorities

### Curator

A simple interface for reviewing photos one at a time.

Core actions:
- Keep
- Skip
- Previous / Next
- Progress indicator
- Optional location
- Optional date
- Optional notes/description
- Chapter-start flag
- Chapter title
- Autosave decisions
- Export metadata JSON

Never delete the original photos.

The metadata should make it possible to later identify:
- photos kept for the slideshow
- photos kept for the future album
- photos skipped

For v1 it is acceptable to have one selection state, but design the schema so an additional album-selection field can be added later without redesigning everything.

### Slideshow

Core experience:
- Start slideshow
- 5 seconds per photo by default
- Smooth ~500 ms fade
- Chapter title screens
- White text on dark background
- Background MP3
- Music loops
- Play/pause
- Previous/next
- Progress indicator
- Volume control
- Responsive layout
- Fullscreen-friendly
- Graceful handling of missing images
- Keyboard navigation on desktop
- Touch-friendly controls on iPad

Photos and videos should eventually be representable in the same manifest, although video playback is explicitly out of scope for v1 according to the source PRD.

## Important architecture principle

Do NOT make the core viewer depend on undocumented Google Photos scraping or fragile Google Photos URLs.

The source PRD proposes public Google Photos album URLs and storing Google Photos URLs. Treat that as the original requirement, but verify feasibility before implementing it.

Prefer a clean separation:

1. Import/source layer
2. Metadata/manifest
3. Curator
4. Slideshow viewer

The viewer should consume a local/static metadata manifest and static assets wherever practical. This makes GitHub Pages deployment and TV playback much more reliable.

If Google Photos integration cannot be implemented reliably without unsupported APIs/authentication, do not fake it. Build the curator around an importable manifest/static asset directory and document the required import step.

## Suggested project structure

south-africa-slideshow/
├── README.md
├── CHANGELOG.md
├── slideshow.html
├── curator.html
├── assets/
│   ├── css/
│   ├── js/
│   ├── images/
│   ├── videos/
│   └── music/
├── data/
│   ├── metadata.example.json
│   └── metadata.json
├── versions/
│   └── v1.0/
│       ├── metadata.json
│       └── README.md
└── docs/
    ├── ARCHITECTURE.md
    └── SETUP.md

Keep the implementation dependency-light. Vanilla HTML/CSS/JavaScript is preferred unless a dependency has a clear benefit.

## Metadata concept

The source PRD uses:
- slideshow title
- source album URL
- creation date
- version
- music
- chapters
- photo IDs
- photo URLs
- location
- date
- notes
- display duration
- transition duration
- autoplay
- volume

Keep this general structure compatible where sensible.

Example:

{
  "slideshow": {
    "title": "South Africa",
    "version": "1.0"
  },
  "music": {
    "file": "assets/music/slideshow-music.mp3",
    "loopEnabled": true
  },
  "chapters": [
    {
      "id": "ch-001",
      "title": "Day 1 — Safari",
      "order": 1,
      "photos": [
        {
          "photoId": "image-0001",
          "src": "assets/images/image-0001.jpg",
          "location": "Kruger National Park",
          "date": "2026-08-15",
          "notes": "Giraffe at sunset",
          "selection": "keep"
        }
      ]
    }
  ],
  "settings": {
    "photoDisplayDurationSeconds": 5,
    "transitionDurationMs": 500,
    "autoplayEnabled": true,
    "audioVolume": 0.7
  }
}

## UX direction

The curator should be fast rather than feature-heavy.

A reviewer should be able to:
- open photo
- press Keep or Skip
- immediately move to next photo
- use keyboard shortcuts
- see progress
- optionally add metadata

Do not make metadata entry mandatory.

The slideshow should feel elegant and minimal rather than like a photo-management application.

## Future-proofing

Leave room for:
- per-chapter music
- video clips
- captions
- map/location view
- thumbnail navigation
- MP4 export
- album generation
- richer AI metadata
- multiple selection categories
- future vacation projects

Do not implement these unless needed for v1.

## Testing expectations

Verify:
- valid JSON
- curator navigation
- autosave/local persistence
- export
- slideshow loading
- fade transitions
- timing
- music controls
- previous/next
- chapter screens
- responsive layout
- keyboard controls
- graceful image failures
- no console errors
- GitHub Pages compatibility

## User context

The user is comfortable with Premiere Pro and coding/developer tooling. The aim is to reduce manual work, not to teach basic HTML.

The user has already discussed using Claude Code/Codex and wants the project implemented in a GitHub repository.

## Source document

The original PRD is included alongside this brief as `PRD_SOURCE.md`.
