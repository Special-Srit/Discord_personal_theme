# Apple Midnight

A dark, Apple/macOS-inspired Discord theme for Vencord. Features heavy glassmorphism panels, a monochrome palette with a soft lavender accent, CSS-animated ambient background blobs, Paperlogy custom font, and 10 UI animations.

## Preview

<!-- Add a screenshot after first install -->

## Install

### Option A — Vencord Theme Loader (recommended)

1. Open Discord → Settings → Vencord → Themes
2. Click **"Open Themes Folder"**
3. Copy `apple-midnight.theme.css` and the `fonts/` folder into that directory
4. Enable **Apple Midnight** in the theme list

### Option B — QuickCSS

1. Open Discord → Settings → Vencord → QuickCSS
2. Paste the contents of `apple-midnight.theme.css`

> **Font note:** The `fonts/` folder must be present next to the CSS file for Paperlogy to load. Copy `fonts/` into the Vencord themes directory alongside the CSS file.

## After Pushing to GitHub

Replace the local `@font-face` paths in `apple-midnight.theme.css`:

Change:
```
src: url('./fonts/Paperlogy-4Regular.ttf');
```

To:
```
src: url('https://raw.githubusercontent.com/YOUR-USERNAME/apple-midnight/main/fonts/Paperlogy-4Regular.ttf');
```

Do the same for the other three weights (Medium, SemiBold, Bold). This allows anyone to load the theme directly from the GitHub URL without needing local font files.

## Requirements

- [Vencord](https://vencord.dev/) installed in Discord
- The Midnight base theme is bundled via `@import` — no separate install needed

## Credits

- Base theme: [Midnight by refact0r](https://github.com/refact0r/midnight-discord)
- Font: [Paperlogy](https://freesentation.blog/paperlogyfont) — SIL Open Font License
- Palette: Apple HIG dark mode system colors + Tailwind violet-300

## License

MIT
