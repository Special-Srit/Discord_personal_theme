# Apple Midnight — Discord Theme Design Spec

**Date:** 2026-06-04  
**Status:** Approved  
**Vencord version target:** Current (QuickCSS + Theme Loader)

---

## 1. Overview

A dark, Apple/macOS-inspired Discord theme built as a deep CSS override layer on top of the community **Midnight** theme by refact0r. Features heavy glassmorphism panels, a monochrome palette with a single soft lavender accent, a CSS-animated ambient background, the Paperlogy custom font, and a full suite of UI animations.

---

## 2. Architecture

### Approach
Import Midnight core via `@import`, then override with a single custom CSS file. Midnight handles Discord's internal class structure and update compatibility; the override layer owns all visual decisions.

### File Structure
```
apple-midnight.theme.css     ← single file loaded by Vencord
fonts/
  Paperlogy-4Regular.ttf
  Paperlogy-5Medium.ttf
  Paperlogy-6SemiBold.ttf
  Paperlogy-7Bold.ttf
README.md
.gitignore
docs/
  superpowers/specs/
    2026-06-04-apple-midnight-discord-theme-design.md
```

### CSS File Structure
```css
/* 1. Theme metadata header (Vencord/BetterDiscord META block) */
/* 2. @import Midnight core */
/* 3. @font-face declarations (Paperlogy) */
/* 4. CSS custom properties — color tokens */
/* 5. Animated background (body pseudo-elements) */
/* 6. Glass panel overrides */
/* 7. Typography */
/* 8. Animations — keyframes */
/* 9. Animation assignments per element */
/* 10. Accent color touches */
```

---

## 3. Metadata Header

```css
/**
 * @name        Apple Midnight
 * @description Dark monochrome glassmorphism theme. Apple-inspired, soft lavender accent, animated background.
 * @author      <your name>
 * @version     1.0.0
 * @source      https://github.com/<you>/apple-midnight
 */
```

---

## 4. Base Theme

**Midnight by refact0r**
- Import URL: `https://refact0r.github.io/midnight-discord/build/midnight.css`
- Repository: https://github.com/refact0r/midnight-discord
- Used for: Discord class compatibility, structural layout overrides

---

## 5. Color System

All colors defined as CSS custom properties overriding Midnight's defaults.

### Background tokens
| Token | Value | Role |
|---|---|---|
| `--bg-1` | `#08080A` | App background (deepest) |
| `--bg-2` | `#111114` | Panel surfaces |
| `--bg-3` | `#1C1C1F` | Elevated / hover states |
| `--divider` | `#38383A` | Separators |

### Text tokens
| Token | Value | Role |
|---|---|---|
| `--text-1` | `#F2F2F7` | Primary text |
| `--text-2` | `#AEAEB2` | Secondary text, message body |
| `--text-3` | `#636366` | Muted text, timestamps |

### Glass tokens
| Token | Value | Role |
|---|---|---|
| `--border` | `rgba(255,255,255,0.10)` | Panel borders |
| `--glass-srv` | `rgba(255,255,255,0.12)` | Server sidebar surface |
| `--glass-chs` | `rgba(255,255,255,0.10)` | Channel list surface |
| `--glass-chat` | `rgba(255,255,255,0.06)` | Chat area surface |
| `--glass-modal` | `rgba(255,255,255,0.14)` | Modals / popouts |

### Accent tokens (single color — soft lavender)
| Token | Value | Role |
|---|---|---|
| `--accent` | `#C4B5FD` | Active states, buttons, focus |
| `--accent-dim` | `rgba(196,181,253,0.13)` | Active channel bg, mention bg |
| `--accent-border` | `rgba(196,181,253,0.40)` | Active border highlight |
| `--accent-glow` | `rgba(196,181,253,0.08)` | Subtle glow on accent elements |

Source: Apple HIG dark mode system colors (backgrounds/grays) + Tailwind violet-300 (accent).

---

## 6. Glass System

Every panel uses three CSS layers to create the frosted glass effect:

```css
background    : var(--glass-*);
backdrop-filter : blur(Xpx) saturate(1.4);
border        : 1px solid var(--border);
box-shadow    : inset 0 1px 0 rgba(255,255,255,0.06),  /* top rim  */
                0 4px 24px rgba(0,0,0,0.40);            /* depth    */
```

### Per-panel blur values
| Panel | `backdrop-filter` blur | Surface opacity |
|---|---|---|
| Server sidebar | `blur(40px)` | 12% |
| Channel list | `blur(48px)` | 10% |
| Chat area | `blur(52px)` | 6% |
| Modals / popouts | `blur(60px)` | 14% |

---

## 7. Animated Background

Two CSS pseudo-elements on `body`. Pure CSS `transform` animation — no JS, no canvas, GPU-composited only.

```css
body { position: relative; }
body::before, body::after {
  content: '';
  position: fixed;
  border-radius: 50%;
  filter: blur(80px);
  pointer-events: none;
  will-change: transform;
  z-index: -1;
}
body::before {
  width: 600px; height: 600px;
  top: -150px; left: -100px;
  background: rgba(196, 181, 253, 0.07);   /* faint lavender */
  animation: blobDrift1 16s ease-in-out infinite alternate;
}
body::after {
  width: 500px; height: 500px;
  bottom: -120px; right: -80px;
  background: rgba(180, 180, 210, 0.06);   /* neutral gray  */
  animation: blobDrift2 20s ease-in-out infinite alternate;
}

@keyframes blobDrift1 {
  from { transform: translate(0, 0) scale(1); }
  to   { transform: translate(60px, 40px) scale(1.12); }
}
@keyframes blobDrift2 {
  from { transform: translate(0, 0) scale(1); }
  to   { transform: translate(-50px, 35px) scale(0.9); }
}
```

**Cycle:** 16–20s (medium drift). GPU-only `transform` — zero repaints.

---

## 8. Typography

### Font: Paperlogy
- Source: https://freesentation.blog/paperlogyfont
- License: SIL Open Font License (OFL) — free for commercial use
- Files: local TTF in `fonts/` directory
- After GitHub push: referenced via raw GitHub URLs

### @font-face declarations
```css
@font-face { font-family:'Paperlogy'; font-weight:400; src:url('./fonts/Paperlogy-4Regular.ttf'); }
@font-face { font-family:'Paperlogy'; font-weight:500; src:url('./fonts/Paperlogy-5Medium.ttf'); }
@font-face { font-family:'Paperlogy'; font-weight:600; src:url('./fonts/Paperlogy-6SemiBold.ttf'); }
@font-face { font-family:'Paperlogy'; font-weight:700; src:url('./fonts/Paperlogy-7Bold.ttf'); }
```

### Usage
| Weight | Used for |
|---|---|
| 400 Regular | Message body text, descriptions |
| 500 Medium | Channel names, UI labels |
| 600 SemiBold | Usernames, section headers |
| 700 Bold | Modal titles, strong emphasis |

> **Note for Vencord:** `@font-face` with relative paths works for local dev only. After pushing to GitHub, replace `./fonts/` with `https://raw.githubusercontent.com/<you>/apple-midnight/main/fonts/`.

---

## 9. Animation System

All animations use CSS `@keyframes` applied via class selectors on Discord's DOM elements. Animations trigger on element insertion — naturally only fires for new messages / state changes.

### 9.1 Message Entry
**Trigger:** New message inserted into chat  
**Effect:** Slide up + spring + text reveal

```css
/* Phase 1: Container slide up */
@keyframes msgSlideUp {
  from { opacity: 0; transform: translateY(14px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Phase 2: Text reveal left→right */
@keyframes msgTextReveal {
  from { clip-path: inset(0 100% 0 0); }
  to   { clip-path: inset(0 0% 0 0); }
}

/* NOTE: Actual Discord class names must be inspected at implementation time.
   Midnight exposes some via its own overrides; others need DevTools inspection.
   Placeholders here represent the intent — not final selectors. */
.message-container {
  animation: msgSlideUp 0.3s cubic-bezier(0.34, 1.4, 0.64, 1) forwards;
}
.message-container .markup {
  clip-path: inset(0 100% 0 0);
  animation: msgTextReveal 0.45s ease-out 0.18s forwards;
}
```

### 9.2 Message Hover Actions
**Trigger:** `:hover` on message  
**Effect:** React/reply/edit buttons fade + slide down, staggered

```css
@keyframes actionFadeIn {
  from { opacity: 0; transform: translateY(-4px); }
  to   { opacity: 1; transform: translateY(0); }
}
/* Applied to each action button with nth-child delay: 0.05s, 0.12s, 0.19s */
```

### 9.3 Typing Indicator
**Effect:** Lavender bouncing dots in glass pill background  
Three dots with `translateY(-5px)` bounce, staggered 0.2s each. Pill restyled with glass background + lavender dots.

### 9.4 Channel Switch
**Trigger:** Channel change  
**Effect:** Messages stagger fade + slide in from top  
`opacity: 0 → 1` + `translateY(8px → 0)`. Stagger implemented via `:nth-child(1..10)` selectors, each with `animation-delay: 0.08s × n`. Messages beyond the 10th appear without animation (already visible in viewport anyway).

### 9.5 Loading Shimmer
**Effect:** Skeleton messages with lavender-tinted shimmer sweep  
`background: linear-gradient(90deg, transparent, rgba(196,181,253,0.12), transparent)` animated across placeholder elements. 1.4s loop.

### 9.6 Notification Badge Pulse
**Effect:** Unread badges gently scale 1 → 1.2 → 1, lavender color  
2s `ease-in-out infinite` pulse. Subtle — draws attention without flashing.

### 9.7 Channel Hover Slide
**Effect:** Channel rows `translateX(4px)` + lavender accent bar slides in on left  
`transition: all 0.2s ease`. Accent bar: `width: 2px`, `height: 0 → 12px`.

### 9.8 Server Icon Morph
**Effect:** `border-radius: 50% → 14px` (circle → squircle) + `scale(1.08)` on hover  
Active server shows lavender pip indicator on left edge.  
`transition: all 0.25s cubic-bezier(0.34, 1.56, 0.64, 1)`

### 9.9 Modal / Popup Open
**Effect:** Scale from 92% + translateY(10px) + opacity 0 → full  
`animation: 0.35s cubic-bezier(0.34, 1.4, 0.64, 1)`. Applied to: user profiles, context menus, emoji picker, settings modals.

### 9.10 Voice Speaking Ring
**Effect:** Double lavender pulsing rings around speaking avatar  
Two concentric rings (`::before`, `::after`) with `border: 2px solid rgba(196,181,253,0.5)` scaling outward. 1s loop, offset by 0.2s.

---

## 10. Accent Color Targets

Elements that receive the lavender accent (`#C4B5FD` / `--accent`):
- Active channel indicator + left border
- Unread channel dot
- Mention highlight background + text
- Message notification badge
- Send button + hover
- Active/focused input border
- User role color (where applicable)
- Voice speaking rings
- Typing indicator dots
- Server icon (active state)
- Selection highlight

---

## 11. GitHub / Distribution

- Fonts committed to `fonts/` in repo
- After push: `@font-face` src URLs updated to `https://raw.githubusercontent.com/<user>/apple-midnight/main/fonts/Paperlogy-*.ttf`
- `.gitignore` excludes `.superpowers/` brainstorm files
- `README.md` includes: preview screenshot, install instructions, Vencord QuickCSS import

---

## 12. Out of Scope (Phase 1)

- Plugins (separate phase, decided after theme is complete)
- Light mode variant
- Custom emoji / sticker styling
- Mobile Discord support
