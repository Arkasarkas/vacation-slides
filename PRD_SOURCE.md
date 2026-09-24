# Product Requirements Document: Interactive Photo Slideshow System

**Project Name:** South Africa Slideshow  
**Version:** 1.0  
**Date:** September 23, 2026  
**Owner:** Arkas  

---

## 1. Overview

Build a reusable web-based interactive photo slideshow system that pulls images from Google Photos, displays them with chapter breaks and narration, and plays looping background music. The slideshow will be shareable and viewable on any device (iPad, TV, desktop) without requiring authentication from viewers.

**Primary Use Case:** Create a polished slideshow of South Africa vacation photos that can be easily shared and displayed to family and friends.

**Bonus:** Template should be reusable for future vacations with minimal setup.

---

## 2. Key Requirements

### 2.1 Viewer Experience (End User — e.g., Arkas's Mum)
- Opens a simple link to the slideshow (no login required)
- Clicks "Start Slideshow"
- Photos auto-advance every **5 seconds** with smooth fade transitions
- Chapter screens appear before photo groups (e.g., "Day 1 — Safari") with elegant white text on dark background, fade transition
- Background music plays continuously and loops throughout the entire slideshow
- Can pause/play at any time
- Can manually navigate back/forward through photos
- Easy stop control on iPad (implementation TBD: long press or persistent button)
- Works seamlessly on iPad, TV, and desktop browsers
- No ads, no interruptions, no lag

### 2.2 Creator Experience (Arkas)
- **Curator Tool:** Browse all Google Photos from shared album, one photo at a time
  - Mark each photo: **Keep** / **Skip**
  - Optionally add metadata: location, date, notes (grabbed from Google Photos if available)
  - Flag photos as **Chapter Start** and enter chapter title (e.g., "Day 1 — Safari")
  - See real-time progress (e.g., "Photo 47 of 200")
  - Decisions auto-save to metadata JSON
- **Music Assignment:** Upload one MP3 file for the entire slideshow; will loop throughout
  - Per-chapter music capability reserved for future iterations (v1.1+)
- **Versioning:** Create v1.0, v1.1, etc. with changelogs; easily rollback or iterate
- **Note:** Curator tool is for curation only; preview the final result in the slideshow viewer itself

---

## 3. Technical Architecture

### 3.1 File Structure
```
slideshows/
├── south-africa-2026/
│   ├── slideshow.html          # Main slideshow viewer (generic, reusable)
│   ├── curator.html            # Curation tool (generic, reusable)
│   ├── CHANGELOG.md            # Version history
│   └── versions/
│       ├── v1.0/
│       │   ├── metadata.json   # Curated photo list, chapters, music assignments
│       │   ├── music/
│       │   │   ├── day-1-safari.mp3
│       │   │   ├── day-2-landscape.mp3
│       │   │   └── ...
│       │   └── README.md       # Release notes for v1.0
│       └── v1.1/
│           ├── metadata.json
│           ├── music/
│           └── README.md
```

### 3.2 Google Photos Integration
- Use public Google Photos shared album link or album key
- Fetch image URLs from shared album (no OAuth required for viewers)
- Curator tool: Load album in iframe or fetch via public API
- Store only image IDs/URLs in metadata.json; actual images live in Google Photos

### 3.3 Hosting
- **GitHub Pages:** Host all HTML, CSS, JS, metadata.json, and MP3 files
- **URL:** `https://<username>.github.io/south-africa-2026/slideshow.html?version=v1.0`
- Easy to push updates via `git push`

---

## 4. Metadata JSON Specification

### 4.1 Structure
```json
{
  "slideshow": {
    "title": "South Africa 2026",
    "googlePhotosAlbumUrl": "https://photos.google.com/share/...",
    "createdDate": "2026-09-23",
    "version": "1.0"
  },
  "music": {
    "file": "slideshow-music.mp3",
    "loopEnabled": true
  },
  "chapters": [
    {
      "id": "ch-001",
      "title": "Day 1 — Safari Adventure",
      "order": 1,
      "photos": [
        {
          "photoId": "image-0001",
          "googlePhotosUrl": "https://photos.google.com/.../...",
          "location": "Kruger National Park",
          "date": "2026-08-15",
          "notes": "Giraffe at sunset"
        },
        {
          "photoId": "image-0002",
          "googlePhotosUrl": "https://photos.google.com/.../...",
          "location": "Kruger National Park",
          "date": "2026-08-15",
          "notes": "Lion pride in grass"
        }
      ]
    },
    {
      "id": "ch-002",
      "title": "Day 2 — Mountain Peaks",
      "order": 2,
      "photos": [
        // ...
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
```

---

## 5. Curator Tool (curator.html)

### 5.1 Interface
- **Left side:** Large photo preview (takes up ~70% of screen)
- **Right side:** Control panel (~30%)
  - Photo counter: "47 / 200"
  - Two buttons: **Keep** | **Skip**
  - Text input: "Location (optional)" — pre-filled from Google Photos if available
  - Text input: "Date (optional)" — pre-filled from Google Photos if available
  - Text area: "Notes / Description (optional)"
  - Checkbox: **"Start new chapter here?"**
  - If checked: Text input: "Chapter title (e.g., 'Day 1 — Safari')"
  - "Next Photo" button
  - Progress bar showing completion

### 5.2 Functionality
- Load all photos from Google Photos shared album
- Show one photo at a time
- Keep/Skip decisions saved to `metadata.json` in real-time (localStorage or download at end)
- Attempt to pre-fill location and date from Google Photos metadata if available
- Generate chapter structure automatically based on "Start new chapter" flags
- Validate: ensure each chapter has at least one photo
- Export final metadata as downloadable JSON file

### 5.3 Workflow
1. User opens curator.html
2. Enters Google Photos album URL
3. Photos load (location/date pre-filled from Google Photos metadata if available)
4. User flips through each one, marking Keep or Skip
5. Optionally refines location, date, or adds notes
6. Flags chapter starts and enters chapter titles
7. At the end, downloads metadata.json
8. User manually moves metadata.json to `versions/v1.0/metadata.json`
9. User uploads one MP3 music file to `versions/v1.0/music/slideshow-music.mp3`

---

## 6. Slideshow Viewer (slideshow.html)

### 6.1 Features
- **Auto-advance:** Photos display for 5 seconds each
- **Smooth transitions:** Fade between photos (0.5 sec transition)
- **Chapter screens:** Full-screen elegant white text on dark background showing chapter title before first photo of chapter, then fade to first photo
- **Background music:** One MP3 file plays continuously and loops throughout the entire slideshow
- **Playback controls:**
  - Play / Pause button
  - Previous / Next photo buttons
  - Progress bar (current photo / total photos)
  - Volume slider
  - Easy stop/exit (implementation TBD: refined for iPad)
- **Optional metadata display:** Location and date subtly displayed on photos if available from Google Photos
- **Responsive design:** Works on desktop, tablet, mobile, and when mirrored to TV
- **No authentication needed:** Viewers just open the link

### 6.2 Technical Specs
- Fetch metadata.json from repo
- Load all photo URLs from Google Photos
- Preload images in background for smooth display
- Handle audio looping and crossfading with Web Audio API
- Use CSS for smooth transitions (Intersection Observer for performance)
- Fallback: if a photo fails to load, skip gracefully

### 6.3 URL Parameters
- `?version=v1.0` — Load specific version
- `?autoplay=false` — Start paused
- `?duration=3` — Override photo display duration

---

## 7. Music Handling

### 7.1 Music Files
- One MP3 file for the entire slideshow
- User uploads to `versions/v1.0/music/slideshow-music.mp3`
- Music is referenced in metadata.json

### 7.2 Playback Rules
- Music starts when slideshow starts
- Loops seamlessly throughout the entire slideshow
- Viewer can adjust volume with slider
- Viewer can pause audio with the main play/pause button

### 7.3 Future Enhancement (v1.1+)
- Per-chapter music capability reserved for future iteration
- Structure already designed in metadata.json to support this when needed

---

## 8. Versioning & Deployment

### 8.1 Version Workflow
- Create each version under `versions/vX.Y/`
- Update `CHANGELOG.md` with: version number, date, changes, new features
- Keep only active version metadata at root (for simplicity)
- When deploying v1.1, copy v1.0 as backup, update root metadata.json

### 8.2 GitHub Push
```bash
git add .
git commit -m "v1.0: Initial release — South Africa slideshow curated"
git push origin main
```
- GitHub Pages auto-deploys
- Live at `https://<username>.github.io/south-africa-2026/slideshow.html`

---

## 9. Reusability for Future Projects

### 9.1 Template Approach
- Core files (slideshow.html, curator.html) are generic and never change
- Only metadata.json and music/ files differ per slideshow
- Folder structure is consistent: `slideshows/ → project-name/ → versions/`

### 9.2 For a New Vacation (e.g., Japan 2027)
1. Create folder: `slideshows/japan-trip-2027/`
2. Copy `slideshow.html` and `curator.html` from south-africa-2026
3. Copy `CHANGELOG.md` template
4. Run curator tool with new Google Photos album
5. Upload new metadata.json and music files to `versions/v1.0/`
6. Push to GitHub
7. Done — Japan slideshow is live

---

## 10. Testing & Verification Checklist

### 10.1 Curator Tool
- [ ] Loads Google Photos album correctly
- [ ] Photos display one at a time
- [ ] Keep/Skip buttons work
- [ ] Metadata fields (location, date, notes) pre-fill from Google Photos when available
- [ ] Metadata can be edited and saves correctly
- [ ] Chapter flags work; chapter title text saves
- [ ] Progress counter updates
- [ ] Final metadata.json downloads correctly
- [ ] JSON is valid and parseable
- [ ] Skipped photos do not appear in final metadata

### 10.2 Slideshow Viewer
- [ ] Photos load from Google Photos
- [ ] Auto-advance works (5 sec per photo)
- [ ] Transitions are smooth (0.5 sec fade)
- [ ] Chapter screens display correctly (white text on dark background, fade to first photo)
- [ ] Music plays and loops seamlessly throughout slideshow
- [ ] Pause/Play toggles work
- [ ] Previous/Next buttons navigate correctly
- [ ] Volume slider adjusts audio
- [ ] Stop/exit button is easy to access (especially on iPad)
- [ ] Optional metadata (location, date) displays subtly if available
- [ ] Responsive on mobile, tablet, desktop
- [ ] Works when mirrored to TV
- [ ] No console errors
- [ ] Works on iPad (Safari browser)

### 10.3 Deployment
- [ ] GitHub repo is public
- [ ] GitHub Pages is enabled
- [ ] URL is live and accessible
- [ ] No CORS errors when loading images
- [ ] Metadata.json loads correctly
- [ ] Music files play without ads or interruptions

---

## 11. Future Enhancements (Out of Scope for v1.0)

- [ ] Per-chapter music support (infrastructure ready; currently one track for whole slideshow)
- [ ] Advanced metadata display on photos (currently subtle, optional)
- [ ] Long-press or refined touch controls for stop on iPad
- [ ] Add location markers on a map view
- [ ] Export slideshow as downloadable MP4 video
- [ ] Add photo annotations/captions overlay
- [ ] Shuffle mode for photos
- [ ] Thumbnail strip for quick navigation
- [ ] Dark mode / light mode toggle
- [ ] Share URL with access controls
- [ ] Print photo album integration (separate project)

---

## 12. Out of Scope (For This Phase)

- Photo editing (crops, filters, color correction)
- Printed photo album (will be tackled separately)
- Video clips embedded in slideshow (photos only for v1.0)
- Advanced analytics or tracking

---

## 13. Dependencies

- **Frontend:** HTML5, CSS3, vanilla JavaScript (no framework required)
- **Hosting:** GitHub Pages (free)
- **Music:** User-sourced MP3 files (copyright-free or licensed)
- **Photos:** Google Photos shared album

---

## 14. Timeline & Next Steps

1. **Immediate:** Finalize metadata JSON structure and curator tool UI
2. **Week 1:** Build curator.html and slideshow.html
3. **Week 2:** Test with real South Africa photos
4. **Week 3:** Deploy to GitHub Pages
5. **Week 4:** Share with family; gather feedback; iterate to v1.1 if needed

---

## 15. Handoff Notes for Claude Code

- Curator tool should be simple and intuitive; UX is critical
- Slideshow should be bulletproof; no loading delays or lag
- Music crossfading should be smooth; no audio pops
- Mobile responsiveness is essential (iPad use case)
- Code should include comments for future maintainability
- Consider accessibility (alt text, keyboard navigation)

---

**End of Document**