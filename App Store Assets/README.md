# App Store Assets

This folder contains export-ready assets for macOS App Store submission.

## Screenshots

Location: `Screenshots/`

Generated from app-window-only captures (not full desktop), for:

- `table`, `treemap`, `split`
- `light` and `dark`
- Sizes: `1280x800`, `1440x900`, `2560x1600`, `2880x1800`

Filename pattern:

- `mac-<size>-store-<view>-<mode>.png`

The `raw/` subfolder contains original app-window captures used to produce the resized variants.

## App Icon

Location: `Icon/`

- `app-icon-1024.png` (submission-friendly export)

The app icon set is also wired into the project at:

- `SystemMetrics/SystemMetrics/Assets.xcassets/AppIcon.appiconset`
