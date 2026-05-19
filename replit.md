# App Store Screenshots Editor

A full web app for designing and exporting production-ready App Store and Google Play screenshots. Design slides as advertisements, then export them at every required resolution in a single zip.

## Run & Operate

- `pnpm --filter @workspace/screenshot-editor run dev` — run the screenshot editor (port 3000, served at `/`)
- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Screenshot Editor: Next.js 15 + React 19 + Tailwind CSS 3 + ShadCN UI
- Drag-to-reorder: @dnd-kit
- Export: html-to-image + JSZip
- API: Express 5 (api-server)

## Where things live

- `artifacts/screenshot-editor/` — Next.js screenshot editor app (served at `/`)
- `artifacts/screenshot-editor/src/components/editor/` — all editor components
- `artifacts/screenshot-editor/src/lib/` — types, constants, themes, storage, locale
- `artifacts/screenshot-editor/public/mockup.png` — iPhone frame PNG (do NOT replace)
- `artifacts/screenshot-editor/src/app/api/project/` — GET/POST project state to disk
- `artifacts/screenshot-editor/src/app/api/upload/` — upload screenshot PNGs
- `app-store-screenshots.json` — auto-saved project state (git-trackable)
- `artifacts/api-server/` — Express backend (served at `/api`)

## Architecture decisions

- Screenshot editor is a standalone Next.js app, not a Vite SPA, because it needs server-side API routes for file read/write (project persistence + screenshot upload).
- Project state autosaves to both localStorage (instant) and `app-store-screenshots.json` on disk (git-portable). File takes priority on hydration.
- All images are preloaded as base64 data URIs before export to avoid race conditions with html-to-image.
- Device frames are pure CSS/HTML — no extra PNGs needed except the iPhone bezel (`mockup.png`).
- Export produces a zip with full resolution PNGs organized by platform/device/size/locale.

## Product

- Design App Store (iPhone, iPad) and Google Play (Android Phone, 7"/10" Tablet, Feature Graphic) screenshots
- 7 slide layouts: hero, device-bottom, device-top, two-devices, no-device, split-landscape, feature-graphic
- 4 built-in themes: Clean Light, Dark Bold, Warm Editorial, Ocean Fresh
- Drag-to-reorder slides, inline text editing, layout/theme switcher per slide
- Drop-target screenshot picker (uploaded files saved to `public/screenshots/uploaded/`)
- One-click bulk PNG export at every Apple/Google-required resolution via html-to-image
- Undo/redo (Cmd+Z/Shift+Cmd+Z), keyboard nav (↑↓/j/k), duplicate (Cmd+D), delete (Cmd+Delete)
- Multi-locale support with per-slide localized copy

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Do NOT replace `public/mockup.png` — the PHONE_SCREEN constants in `src/lib/constants.ts` are measured against this exact PNG.
- Export double-call (`toPng` twice) is intentional — do not remove it.
- The Next.js dev script uses `${PORT:-3000}` to pick up the PORT env var.
- `minimumReleaseAge: 1440` in pnpm-workspace.yaml may block installing very new packages — add to `minimumReleaseAgeExclude` if needed.
