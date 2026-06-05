# Apple Midnight — Changelog

---

## Concept

A personal Discord theme built on **Vencord** using the **Midnight** community base theme as a compatibility layer, with a deep CSS override layer on top.

### Design goals
- Apple/macOS-inspired dark aesthetic — near-black palette sourced from Apple HIG dark mode system colors
- Heavy glassmorphism — `backdrop-filter: blur()` on all major panels
- Single soft lavender accent (`#C4B5FD` — Tailwind violet-300)
- Animated ambient background — two CSS blobs drifting slowly, GPU-composited
- Custom Paperlogy font (OFL licensed, hosted on GitHub raw CDN)
- 10 UI animations: message entry spring, text reveal, hover actions, typing indicator, channel switch stagger, loading shimmer, channel hover slide, server icon morph, notification badge pulse, modal open spring, voice speaking ring

### Architecture
```
@import Midnight base theme        ← handles Discord class compatibility
   ↓
:root { CSS custom properties }    ← color tokens + Midnight variable overrides
   ↓
Glass panels, typography, animations, accent targets   ← visual layer
```

### Key technical decisions
- **Midnight `@import`** — lets Midnight handle Discord's obfuscated class names; we only override visuals
- **CSS custom properties with `!important`** — required because Vencord loads themes in order; if Midnight is listed after our theme, it wins the cascade without `!important`
- **`html { background }` instead of `body`** — Discord JS dynamically overrides `body` background on channel switch via inline styles; `html` is never touched by Discord's JS
- **`[class*="mentioned_"]` underscore selector** — Discord uses `className_hash` obfuscation patterns; underscore selectors target the hash-suffixed variants directly
- **`#app-mount::before/after` for blobs** — blobs must live inside Discord's compositing stack for `backdrop-filter` on panels to blur them correctly
- **`[class*="themedBackground"]` cleared** — Discord dynamically applies per-server theme gradients to this class on channel switch; clearing it prevents background override
- **Channel switch stagger as `animation-delay` only** — Task 6 (message entry spring) and Task 9 (stagger) target the same elements; setting only `animation-delay` in Task 9 preserves Task 6's spring easing

---

## Update Log

---

### v1.0.0 — Initial Build
**Commits:** `dd67fc1` → `d388ed3`

Full theme built via subagent-driven implementation plan. All 15 tasks completed:

| Task | Feature |
|---|---|
| 1 | Project scaffold — `fonts/` dir, empty CSS, git init |
| 2 | Foundation — metadata, `@import` Midnight, `@font-face`, `:root` tokens |
| 3 | Animated background — GPU-composited CSS blob drift |
| 4 | Glass panels — `backdrop-filter` blur on server sidebar, channel list, chat, modals |
| 5 | Typography — Paperlogy with role-based weight assignments + monospace restore for code |
| 6 | Message entry — spring slide-up + left-to-right `clip-path` text reveal |
| 7 | Message hover actions — staggered `actionFadeIn` with smooth hide transition |
| 8 | Typing indicator — glass pill + lavender bounce animation |
| 9 | Channel switch stagger — `animation-delay` only (preserves spring easing from Task 6) |
| 10 | Loading shimmer — lavender sweep on skeleton placeholders |
| 11 | Channel hover slide + server icon morph + notification badge pulse |
| 12 | Modal open animation — scale + spring for popouts and modals |
| 13 | Voice speaking ring — pulsing lavender rings on active speaker |
| 14 | Accent color targets — lavender on active channel, input focus, send button, selection, links |
| 15 | README with install guide + `.gitignore` |

**Quality fixes applied during review:**
- `font-display: swap` added to all `@font-face` (prevents FOIT on Discord startup)
- `will-change: transform, filter` on background blobs (correct compositor hint)
- `[class*="menu-"][role="menu"]` — narrowed from `[class*="menu-"]` to avoid matching list rows
- `backdrop-filter` fallback `background-color` added to each glass panel rule
- Monospace font restoration for code blocks (prevents Paperlogy overriding `<code>`)
- Typing indicator `span` selectors removed (would have colored "is typing…" text lavender)
- Channel switch stagger changed to `animation-delay`-only to avoid clobbering spring easing
- Link selector scoped to `[class*="chat-"] a` (prevent all `<a>` tags turning lavender)
- `am-` prefix on all `@keyframes` names (prevents collisions with other Vencord themes)

---

### v1.0.1 — GitHub CDN + Author
**Commits:** `dd4a13f`

- Switched `@font-face` src URLs from local `./fonts/` to raw GitHub CDN
- Updated `@author` and `@source` in metadata header

---

### v1.0.2 — Live Testing Fixes (Round 1)
**Commits:** `77e20a5`

First load in Discord revealed multiple issues:

**Font not loading**
- Cause: font URLs used `/main/` but repo branch is `master`
- Fix: changed all four `@font-face` URLs to `/master/`

**Background blobs invisible / glass effect not showing**
- Cause: `body::before/after` blobs were covered by Discord's `#app-mount` solid background
- Fix: cleared transparency chain — `#app-mount`, `[class*="layers-"]`, `[class*="layer-"]`, `[class*="base-"]` set to `transparent`
- Blob opacity increased from 0.07 → 0.15 (lavender) and 0.06 → 0.10 (gray)

**Notification badge not lavender**
- Added broader badge selectors: `[class*="lowerBadge-"]`, `[class*="upperBadge-"]`
- Added `transform-origin: center center` to badge pulse animation

---

### v1.0.3 — Cascade Fix + Blob Migration
**Commits:** `a8115e4`

Diagnostic run revealed `:root` variable overrides being beaten by Midnight loading after us:
- `--bg-1: hsla(220, 15%, 20%, 1)` (Midnight's value, not our `#08080A`)
- `--accent-1: oklch(75% 0.1 215)` (Midnight v2 teal, not our `#C4B5FD`)

**Midnight native variables added with `!important`:**
```
--font, --accent-1 → --accent-5, --hover, --active, --selected, --mention, --mention-bg, --link
```

**Blob layer migrated to `#app-mount::before/after`:**
- `body::before/after` replaced with `#app-mount::before/after`
- Blob size increased (700px / 600px), opacity boosted (0.20 / 0.14)

---

### v1.0.4 — Background Survival Fix
**Commits:** `3aba97b`

Diagnostic showed body and `#app-mount` were transparent despite `!important` rules.
- Cause: Discord's JS dynamically sets background on channel switch, overriding even `!important` in stylesheets if Discord uses `!important` on inline styles
- Fix: moved dark base to `html { background: #08080A !important }` — Discord JS never touches `<html>`
- Added `[class*="themedBackground"] { background: transparent !important }` — clears Discord's per-server dynamic gradient on channel switch

---

### v1.0.5 — Mention Highlight Fix
**Commits:** `37982f1`, `014d8eb`

Ping/mentioned message rows showing as solid lavender bars (too opaque).

**Root cause:** Double application — Midnight already applies `--mention-bg` to mention rows via its own selectors, AND our `[class*="mention-"]` CSS rule added another layer on top.

**Fix 1:** Removed the explicit `[class*="mention-"]` CSS block entirely — let Midnight handle it via `--mention-bg` variable.

**Fix 2:** Still too solid — Midnight v2 uses `--accent-1` (solid `#C4B5FD`) for mention backgrounds in some versions.
- Reduced `--mention-bg` from `rgba(196,181,253,0.13)` → `rgba(196,181,253,0.07)`
- Added direct override: `[class*="mentioned_"] { background-color: rgba(196,181,253,0.07) !important }` — targets Discord's `_hash` class pattern directly

---

### v1.0.6 — Palette Fix + New Glass Panels
**Commits:** `b00deef`

`--bg-*` and `--text-*` variables still showing Midnight's values; added `!important` to all:

```css
--bg-1: #08080A !important   (was: hsla(220, 15%, 20%, 1))
--bg-2: #111114 !important
--bg-3: #1C1C1F !important
--bg-4: #242428 !important
--text-1 → --text-5: all !important
```

**New glass panels added:**
- Member list sidebar (`[class*="membersWrap-"]`) — `blur(48px)`
- Chatbox / message input (`[class*="channelTextArea_"]`) — `rgba(255,255,255,0.05)` + `blur(20px)` + rounded border
- User panel / bottom-left (`[class*="panels-"]`) — `blur(48px)`

---

### v1.0.7 — Text Contrast + Date Separator Fix
**Commits:** `47fb9f2`, `82a2bdb`

**Timestamps and pronouns too faint on near-black background:**
- `--text-3` was calibrated for Midnight's lighter `~#2c2e38` base, not our `#08080A`
- Shifted entire text scale up:
  - `--text-3`: `#636366` → `#AEAEB2`
  - `--text-4`: `#48484A` → `#8E8E93`
  - `--text-5`: `#3A3A3C` → `#636366`

**Date separator lines invisible:**
- `--divider: #38383A` is nearly invisible on `#08080A`
- Updated to `#3A3A3E` + added explicit CSS for divider lines (`rgba(255,255,255,0.12)`)

**Date pills and timestamps given explicit styling:**
- Date pill (e.g. "2026년 6월 5일"): glass background + border + rounded pill
- Inline message timestamps: `var(--text-4)`
- System messages (calls, joins): `var(--text-3)`

---

### v1.0.8 — Typing Indicator Fix
**Commits:** `faca71f`, `afec36c`

**Issue:** Typing indicator rendering as a full-width broken bar.
- Cause: `[class*="typing-"]` was applying `border-radius: 20px` + glass background to the full-width container, not the inner pill

**First attempt** (`faca71f`): Added `width: fit-content` + targeted inner `[class*="typingIndicator-"]` — partially worked but still misidentifying elements.

**Root cause found** (`afec36c`): User inspected the typing indicator element — class is `announcer_b88801`. Discord moved the typing indicator into the aria-live announcer element in recent versions. Our selectors were hitting completely unrelated elements.

**Final fix:**
- Target `[class*="announcer_"]` directly
- Set `color: var(--text-3)`, `background: transparent`, `border: none`
- Lavender dot animation retargeted to `[class*="announcer_"] [class*="dot-"]`
- Removed all broken `[class*="typing-"]` glass/pill rules that were misapplying

---

## Known Issues / Future Improvements

- **Glass effect subtlety** — Blobs are there but subtle on near-black; could increase blob opacity further or add a static gradient layer
- **Voice speaking ring** — Needs live voice channel testing to confirm `overflow: visible` isn't needed on parent containers
- **Server icon active pip** — May need DevTools check to confirm lavender indicator is applying correctly
- **Chatbox border radius** — `channelTextArea_` glass rule has `border-radius: 10px` which might conflict with Discord's input layout on some resolutions
- **Loading shimmer GPU** — Uses `background-position` animation (non-GPU); future improvement is `::before` + `transform: translateX()` for full compositing
- **Font URLs** — After any repo rename, update 4 `@font-face` URLs from `master` branch path

---

## File Structure

```
apple-midnight.theme.css    ← single file loaded by Vencord
fonts/
  Paperlogy-4Regular.ttf
  Paperlogy-5Medium.ttf
  Paperlogy-6SemiBold.ttf
  Paperlogy-7Bold.ttf
README.md
CHANGELOG.md
.gitignore
docs/
  superpowers/
    specs/2026-06-04-apple-midnight-discord-theme-design.md
    plans/2026-06-04-apple-midnight-theme.md
```

---

## CSS File Sections (in order)

```
1.  Theme metadata (Vencord @name/@description/@author/@version/@source)
2.  @import Midnight base theme
3.  @font-face — Paperlogy 400/500/600/700
4.  :root — color tokens + Midnight variable overrides (all !important)
5.  App base + animated background (html dark base, #app-mount blobs, transparency chain)
6.  Glass panels (server sidebar, channel list, chat, modals, member list, chatbox, user panel)
7.  Date separators + timestamps
8.  Typography (Paperlogy global, monospace restore, weight assignments)
9.  Animations — message entry (spring + text reveal)
10. Animations — message hover actions
11. Animations — typing indicator (announcer_ target)
12. Animations — channel switch stagger (delay-only)
13. Animations — loading shimmer
14. Animations — channel hover slide
15. Animations — server icon morph
16. Animations — notification badge pulse
17. Animations — modal open spring
18. Animations — voice speaking ring
19. Accent color targets (active channel, unread dot, input focus, send button, mentions, selection, links)
```
