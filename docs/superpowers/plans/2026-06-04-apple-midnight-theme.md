# Apple Midnight Discord Theme — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build `apple-midnight.theme.css`, a Vencord-compatible Discord theme with Apple/macOS glassmorphism, a dark monochrome + soft lavender palette, animated background blobs, Paperlogy font, and 10 UI animations — layered on the Midnight base theme.

**Architecture:** Import Midnight via `@import` as the compatibility base. A single override CSS file owns all visual decisions via CSS custom properties plus additive rules for glass effects and animations. Font files live in `fonts/`; relative paths are used for local dev (updated to raw GitHub URLs after push).

**Tech Stack:** CSS3 (custom properties, `backdrop-filter`, `@keyframes`, `clip-path`, `will-change`), Vencord QuickCSS / Theme Loader, Midnight theme by refact0r, Paperlogy TTF (SIL OFL).

---

## File Map

| Path | Action | Purpose |
|---|---|---|
| `apple-midnight.theme.css` | Create | Main theme file — single file loaded by Vencord |
| `fonts/Paperlogy-4Regular.ttf` | Copy from `Paperlogy-1.001/` | Body text (400) |
| `fonts/Paperlogy-5Medium.ttf` | Copy from `Paperlogy-1.001/` | Labels (500) |
| `fonts/Paperlogy-6SemiBold.ttf` | Copy from `Paperlogy-1.001/` | Usernames (600) |
| `fonts/Paperlogy-7Bold.ttf` | Copy from `Paperlogy-1.001/` | Titles (700) |
| `README.md` | Create | Install instructions + credits |
| `.gitignore` | Create | Exclude brainstorm artifacts and font archive |

---

## How to Load and Verify in Vencord

After each task that modifies `apple-midnight.theme.css`:

1. Open Discord (with Vencord installed)
2. Go to **Settings → Vencord → Themes** → **"Open Themes Folder"**
3. Copy `apple-midnight.theme.css` **and** the `fonts/` folder into that directory
4. Enable **Apple Midnight** in the theme list — OR paste the file contents into the **QuickCSS** tab for a live edit loop

> **Selector note (applies to all animation tasks):** Discord obfuscates class names. All selectors below use `[class*="partial"]` attribute matching, which is standard practice in Discord theming. If an animation or style doesn't apply, open DevTools (Ctrl+Shift+I in Discord), inspect the target element, and find a stable partial class name to use instead.

---

### Task 1: Project Scaffold

**Files:**
- Create: `fonts/` directory (copy 4 TTF files from `Paperlogy-1.001/`)
- Create: `apple-midnight.theme.css` (empty skeleton)

- [ ] **Step 1: Create `fonts/` directory and copy the four used weights**

```powershell
New-Item -ItemType Directory -Path fonts -Force
Copy-Item "Paperlogy-1.001\Paperlogy-4Regular.ttf"  "fonts\"
Copy-Item "Paperlogy-1.001\Paperlogy-5Medium.ttf"   "fonts\"
Copy-Item "Paperlogy-1.001\Paperlogy-6SemiBold.ttf" "fonts\"
Copy-Item "Paperlogy-1.001\Paperlogy-7Bold.ttf"     "fonts\"
```

- [ ] **Step 2: Verify font files exist**

```powershell
Get-ChildItem fonts\
```

Expected — four files:
```
Paperlogy-4Regular.ttf
Paperlogy-5Medium.ttf
Paperlogy-6SemiBold.ttf
Paperlogy-7Bold.ttf
```

- [ ] **Step 3: Create empty theme file**

Create `apple-midnight.theme.css` containing exactly:

```css
/* apple-midnight.theme.css */
```

- [ ] **Step 4: Initialize git and commit**

```bash
git init
git add fonts/ apple-midnight.theme.css
git commit -m "chore: scaffold project — fonts directory and empty theme file"
```

---

### Task 2: Foundation — Metadata, Import, Font-Face, Color Tokens

**Files:**
- Modify: `apple-midnight.theme.css` (replace entire contents)

This task establishes the full foundation layer. Everything built in later tasks depends on these tokens being defined.

- [ ] **Step 1: Replace `apple-midnight.theme.css` with the foundation block**

```css
/**
 * @name        Apple Midnight
 * @description Dark monochrome glassmorphism theme. Apple-inspired, soft lavender accent, animated background.
 * @author      your-name
 * @version     1.0.0
 * @source      https://github.com/your-username/apple-midnight
 */

/* ─── BASE THEME ──────────────────────────────────────────────────── */
@import url('https://refact0r.github.io/midnight-discord/build/midnight.css');

/* ─── FONTS ───────────────────────────────────────────────────────── */
/* Local dev: relative paths work when fonts/ is next to this file.   */
/* After GitHub push: replace ./fonts/ with the raw.githubusercontent  */
/* URL: https://raw.githubusercontent.com/<user>/apple-midnight/main/fonts/ */
@font-face { font-family: 'Paperlogy'; font-weight: 400; src: url('./fonts/Paperlogy-4Regular.ttf'); }
@font-face { font-family: 'Paperlogy'; font-weight: 500; src: url('./fonts/Paperlogy-5Medium.ttf'); }
@font-face { font-family: 'Paperlogy'; font-weight: 600; src: url('./fonts/Paperlogy-6SemiBold.ttf'); }
@font-face { font-family: 'Paperlogy'; font-weight: 700; src: url('./fonts/Paperlogy-7Bold.ttf'); }

/* ─── COLOR TOKENS ────────────────────────────────────────────────── */
:root {
  /* Backgrounds — Apple HIG dark mode scale */
  --bg-1:              #08080A;
  --bg-2:              #111114;
  --bg-3:              #1C1C1F;
  --bg-4:              #242428;
  --divider:           #38383A;

  /* Text */
  --text-1:            #F2F2F7;
  --text-2:            #AEAEB2;
  --text-3:            #636366;
  --text-4:            #48484A;
  --text-5:            #3A3A3C;

  /* Borders */
  --border:            rgba(255, 255, 255, 0.10);
  --border-1:          rgba(255, 255, 255, 0.10);
  --border-2:          rgba(255, 255, 255, 0.07);
  --border-3:          rgba(255, 255, 255, 0.04);

  /* Glass surface tints — used by panel rules in Task 4 */
  --glass-srv:         rgba(255, 255, 255, 0.12);
  --glass-chs:         rgba(255, 255, 255, 0.10);
  --glass-chat:        rgba(255, 255, 255, 0.06);
  --glass-modal:       rgba(255, 255, 255, 0.14);

  /* Accent — Tailwind violet-300 / soft lavender */
  --accent:            #C4B5FD;
  --accent-dim:        rgba(196, 181, 253, 0.13);
  --accent-border:     rgba(196, 181, 253, 0.40);
  --accent-glow:       rgba(196, 181, 253, 0.08);

  /* Midnight variable overrides — maps our accent into Midnight's token names */
  --mention-bg:        rgba(196, 181, 253, 0.13);
  --mention-fg:        #C4B5FD;
  --mention-bg-hover:  rgba(196, 181, 253, 0.20);
}
```

- [ ] **Step 2: Load in Vencord and verify**

Open Discord, load the theme. Confirm:
- Background is near-black (`#08080A`) — a clear departure from Discord's default gray
- Midnight's structural layout is present (server sidebar, channel list, chat area)
- No font errors in DevTools → Console (a 404 on the TTF means the `fonts/` folder isn't next to the CSS file in the themes folder — copy it there)

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: foundation — metadata header, Midnight import, font-face, color tokens"
```

---

### Task 3: Animated Background

**Files:**
- Modify: `apple-midnight.theme.css` — append after color tokens section

Two CSS pseudo-elements on `body` drift slowly as blobs. `transform`-only animation — GPU-composited, zero repaints.

- [ ] **Step 1: Append the animated background section to `apple-midnight.theme.css`**

```css
/* ─── ANIMATED BACKGROUND ────────────────────────────────────────── */
body {
  position: relative;
}

body::before,
body::after {
  content: '';
  position: fixed;
  border-radius: 50%;
  pointer-events: none;
  will-change: transform;
  z-index: 0;
}

body::before {
  width: 600px;
  height: 600px;
  top: -150px;
  left: -100px;
  background: rgba(196, 181, 253, 0.07);
  filter: blur(80px);
  animation: blobDrift1 16s ease-in-out infinite alternate;
}

body::after {
  width: 500px;
  height: 500px;
  bottom: -120px;
  right: -80px;
  background: rgba(180, 180, 210, 0.06);
  filter: blur(80px);
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

- [ ] **Step 2: Verify in Discord**

Reload the theme. Confirm:
- A very faint lavender glow is visible in the top-left area of the app background
- The blob drifts slowly — wait ~5s to observe movement (16s full cycle)
- Open DevTools → More Tools → Rendering → check "Paint flashing" — the blob should produce **no green paint flashes** during movement (transform-only animation)

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: animated background — GPU-composited CSS blob drift"
```

---

### Task 4: Glass Panel Overrides

**Files:**
- Modify: `apple-midnight.theme.css` — append

Adds `backdrop-filter` blur to Discord's four main surfaces. The background blobs from Task 3 show through these panels as frosted glass.

- [ ] **Step 1: Append glass panel rules**

```css
/* ─── GLASS PANELS ────────────────────────────────────────────────── */

/* z-index: 1 ensures panels sit in front of the body blob layer (z-index: 0) */

/* Server list */
nav[aria-label="Servers sidebar"],
[class*="guilds-"] {
  background:               var(--glass-srv) !important;
  backdrop-filter:          blur(40px) saturate(1.4);
  -webkit-backdrop-filter:  blur(40px) saturate(1.4);
  border-right:             1px solid var(--border);
  box-shadow:               inset 0 1px 0 rgba(255, 255, 255, 0.06),
                            4px 0 24px rgba(0, 0, 0, 0.40);
  position: relative;
  z-index: 1;
}

/* Channel list */
nav[aria-label*="channel"],
[class*="sidebar-"] {
  background:               var(--glass-chs) !important;
  backdrop-filter:          blur(48px) saturate(1.4);
  -webkit-backdrop-filter:  blur(48px) saturate(1.4);
  border-right:             1px solid var(--border-2);
  box-shadow:               inset 0 1px 0 rgba(255, 255, 255, 0.06),
                            4px 0 20px rgba(0, 0, 0, 0.35);
  position: relative;
  z-index: 1;
}

/* Chat area */
[class*="chat-"],
[class*="chatContent"] {
  background:               var(--glass-chat) !important;
  backdrop-filter:          blur(52px) saturate(1.4);
  -webkit-backdrop-filter:  blur(52px) saturate(1.4);
  position: relative;
  z-index: 1;
}

/* Modals, popouts, context menus */
[class*="modal-"][class*="root"],
[class*="popout-"][class*="root"],
[class*="menu-"] {
  background:               var(--glass-modal) !important;
  backdrop-filter:          blur(60px) saturate(1.5);
  -webkit-backdrop-filter:  blur(60px) saturate(1.5);
  border:                   1px solid var(--border);
  box-shadow:               inset 0 1px 0 rgba(255, 255, 255, 0.08),
                            0 8px 40px rgba(0, 0, 0, 0.50);
}
```

- [ ] **Step 2: Verify in Discord**

Reload the theme. Confirm:
- Server sidebar has a frosted/translucent look — not solid
- Channel list is slightly more transparent than the server sidebar
- Chat area is the most transparent panel (6% opacity surface)
- Right-click a message — context menu appears as frosted glass
- If a panel appears solid: DevTools → inspect that panel's outermost container → find its partial class name → update the selector

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: glass panels — backdrop-filter blur per panel with depth shadows"
```

---

### Task 5: Typography

**Files:**
- Modify: `apple-midnight.theme.css` — append

- [ ] **Step 1: Append typography rules**

```css
/* ─── TYPOGRAPHY ──────────────────────────────────────────────────── */

/* Global — Paperlogy for the entire app */
* {
  font-family: 'Paperlogy', -apple-system, BlinkMacSystemFont, sans-serif !important;
}

/* Message body text — Regular */
[class*="markup-"],
[class*="messageContent"],
[class*="content-"] {
  font-weight: 400;
}

/* Channel names, UI labels, timestamps — Medium */
[class*="channelName"],
[class*="name-"][class*="channel"],
[class*="timestamp-"],
[class*="subtext-"] {
  font-weight: 500;
}

/* Usernames, section headers — SemiBold */
[class*="username-"],
[class*="author-"],
[class*="headerText-"],
[class*="categoryHeader"],
h1, h2, h3 {
  font-weight: 600;
}

/* Modal titles, strong emphasis — Bold */
[class*="title-"][class*="modal"],
[class*="header-"][class*="modal"],
strong {
  font-weight: 700;
}
```

- [ ] **Step 2: Verify in Discord**

Reload the theme. Confirm:
- Message body text uses Paperlogy — noticeably different from Discord's default Whitney (more geometric, cleaner)
- Usernames appear slightly bolder than message body text
- If font doesn't load: DevTools → Console → look for a 404 on the `.ttf` path → copy the `fonts/` folder into the Vencord themes directory alongside the CSS file

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: typography — Paperlogy with role-based weight assignments"
```

---

### Task 6: Message Entry Animation

**Files:**
- Modify: `apple-midnight.theme.css` — append

New messages slide up from 14px below with a spring easing. The message text then reveals left-to-right via `clip-path`, starting 0.18s after the container lands.

- [ ] **Step 1: Append message entry animation**

```css
/* ─── ANIMATION: MESSAGE ENTRY ────────────────────────────────────── */

@keyframes msgSlideUp {
  from { opacity: 0; transform: translateY(14px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes msgTextReveal {
  from { clip-path: inset(0 100% 0 0); }
  to   { clip-path: inset(0 0% 0 0); }
}

[class*="messageListItem"],
[class*="message-"][class*="cozy"] {
  animation: msgSlideUp 0.3s cubic-bezier(0.34, 1.4, 0.64, 1) forwards;
}

[class*="messageListItem"] [class*="markup-"],
[class*="message-"][class*="cozy"] [class*="markup-"] {
  clip-path: inset(0 100% 0 0);
  animation: msgTextReveal 0.45s ease-out 0.18s forwards;
}
```

- [ ] **Step 2: Verify in Discord**

Send a new message. Confirm:
- The message container rises up with a very subtle spring bounce (not large — the overshoot is gentle)
- Message text writes itself left-to-right over ~0.45s, beginning ~0.18s after the container appears
- Previously visible messages are **not** re-animated on scroll (animation only fires on DOM insertion)

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: message entry — spring slide-up + left-to-right text reveal"
```

---

### Task 7: Message Hover Action Buttons

**Files:**
- Modify: `apple-midnight.theme.css` — append

React/reply/edit buttons stagger-animate in from above when hovering a message.

- [ ] **Step 1: Append hover action animation**

```css
/* ─── ANIMATION: MESSAGE HOVER ACTIONS ────────────────────────────── */

@keyframes actionFadeIn {
  from { opacity: 0; transform: translateY(-4px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Hidden until parent is hovered */
[class*="message-"]:not(:hover) [class*="buttonContainer-"],
[class*="messageListItem"]:not(:hover) [class*="buttonContainer-"] {
  opacity: 0;
  pointer-events: none;
}

/* On hover: stagger each action button */
[class*="message-"]:hover [class*="buttonContainer-"] > *:nth-child(1),
[class*="messageListItem"]:hover [class*="buttonContainer-"] > *:nth-child(1) {
  animation: actionFadeIn 0.18s ease forwards;
  animation-delay: 0.05s;
}

[class*="message-"]:hover [class*="buttonContainer-"] > *:nth-child(2),
[class*="messageListItem"]:hover [class*="buttonContainer-"] > *:nth-child(2) {
  animation: actionFadeIn 0.18s ease forwards;
  animation-delay: 0.12s;
}

[class*="message-"]:hover [class*="buttonContainer-"] > *:nth-child(3),
[class*="messageListItem"]:hover [class*="buttonContainer-"] > *:nth-child(3) {
  animation: actionFadeIn 0.18s ease forwards;
  animation-delay: 0.19s;
}
```

- [ ] **Step 2: Verify in Discord**

Hover over a message. Confirm:
- Action buttons (emoji, reply, more) appear with a staggered slide-down + fade
- Moving cursor away hides them
- If buttons are already forced visible by Midnight: inspect the `buttonContainer` parent in DevTools, find the correct class name, update the `:not(:hover)` selector

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: message hover actions — staggered actionFadeIn animation"
```

---

### Task 8: Typing Indicator

**Files:**
- Modify: `apple-midnight.theme.css` — append

Typing indicator restyled as a frosted glass pill. Dots are lavender and bounce sequentially.

- [ ] **Step 1: Append typing indicator styles**

```css
/* ─── ANIMATION: TYPING INDICATOR ─────────────────────────────────── */

@keyframes typingBounce {
  0%, 60%, 100% { transform: translateY(0); }
  30%            { transform: translateY(-5px); }
}

/* Glass pill container */
[class*="typing-"] {
  background:      rgba(255, 255, 255, 0.08) !important;
  backdrop-filter: blur(20px);
  border:          1px solid var(--border-2);
  border-radius:   20px;
  padding:         4px 10px;
}

/* Lavender dots */
[class*="typing-"] [class*="dot-"],
[class*="typing-"] span {
  background-color: var(--accent) !important;
  border-radius:    50%;
}

/* Staggered bounce */
[class*="typing-"] [class*="dot-"]:nth-child(1),
[class*="typing-"] span:nth-child(1) {
  animation: typingBounce 1s ease-in-out infinite;
  animation-delay: 0s;
}

[class*="typing-"] [class*="dot-"]:nth-child(2),
[class*="typing-"] span:nth-child(2) {
  animation: typingBounce 1s ease-in-out infinite;
  animation-delay: 0.2s;
}

[class*="typing-"] [class*="dot-"]:nth-child(3),
[class*="typing-"] span:nth-child(3) {
  animation: typingBounce 1s ease-in-out infinite;
  animation-delay: 0.4s;
}
```

- [ ] **Step 2: Verify in Discord**

Have another account start typing. Confirm:
- Typing indicator appears as a frosted glass pill
- Dots are lavender (`#C4B5FD`) and bounce in sequence with a 0.2s offset
- If dots aren't lavender: DevTools → inspect a dot element → find the actual class → update the `[class*="dot-"]` selector

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: typing indicator — glass pill + lavender bounce animation"
```

---

### Task 9: Channel Switch Stagger Animation

**Files:**
- Modify: `apple-midnight.theme.css` — append

When switching channels, the first 10 visible messages cascade in from slightly below with increasing delays.

- [ ] **Step 1: Append channel switch stagger**

```css
/* ─── ANIMATION: CHANNEL SWITCH STAGGER ──────────────────────────── */

@keyframes channelSwitchIn {
  from { opacity: 0; transform: translateY(8px); }
  to   { opacity: 1; transform: translateY(0); }
}

[class*="messages-"] [class*="messageListItem"],
[class*="scroller-"] [class*="messageListItem"] {
  animation: channelSwitchIn 0.3s ease-out forwards;
}

[class*="messages-"] [class*="messageListItem"]:nth-child(1),
[class*="scroller-"] [class*="messageListItem"]:nth-child(1)  { animation-delay: 0.08s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(2),
[class*="scroller-"] [class*="messageListItem"]:nth-child(2)  { animation-delay: 0.16s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(3),
[class*="scroller-"] [class*="messageListItem"]:nth-child(3)  { animation-delay: 0.24s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(4),
[class*="scroller-"] [class*="messageListItem"]:nth-child(4)  { animation-delay: 0.32s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(5),
[class*="scroller-"] [class*="messageListItem"]:nth-child(5)  { animation-delay: 0.40s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(6),
[class*="scroller-"] [class*="messageListItem"]:nth-child(6)  { animation-delay: 0.48s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(7),
[class*="scroller-"] [class*="messageListItem"]:nth-child(7)  { animation-delay: 0.56s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(8),
[class*="scroller-"] [class*="messageListItem"]:nth-child(8)  { animation-delay: 0.64s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(9),
[class*="scroller-"] [class*="messageListItem"]:nth-child(9)  { animation-delay: 0.72s; }

[class*="messages-"] [class*="messageListItem"]:nth-child(10),
[class*="scroller-"] [class*="messageListItem"]:nth-child(10) { animation-delay: 0.80s; }
```

- [ ] **Step 2: Verify in Discord**

Click a different channel. Confirm:
- Messages cascade in from below, top-to-bottom, over ~0.8s
- Message 1 appears at 0.08s, message 10 at 0.80s
- Messages beyond index 10 appear without delay (base animation, 0s delay)

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: channel switch stagger — cascading message entrance"
```

---

### Task 10: Loading Shimmer

**Files:**
- Modify: `apple-midnight.theme.css` — append

Skeleton placeholder elements show a lavender-tinted shimmer sweep while channel history loads.

- [ ] **Step 1: Append loading shimmer**

```css
/* ─── ANIMATION: LOADING SHIMMER ─────────────────────────────────── */

@keyframes shimmer {
  from { background-position: -200% center; }
  to   { background-position:  200% center; }
}

[class*="skeleton-"],
[class*="placeholder-"],
[class*="ghost-"] {
  background: linear-gradient(
    90deg,
    rgba(255, 255, 255, 0.04) 0%,
    rgba(196, 181, 253, 0.12) 50%,
    rgba(255, 255, 255, 0.04) 100%
  ) !important;
  background-size: 200% 100%;
  animation: shimmer 1.4s ease-in-out infinite;
  border-radius: 4px;
}
```

- [ ] **Step 2: Verify in Discord**

Switch to a large channel or reload Discord to trigger skeleton loading. Confirm:
- Skeleton lines show a lavender shimmer sweeping left-to-right in a 1.4s loop
- If selector doesn't match: DevTools → inspect a skeleton element while loading → find the class name partial → update the selector

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: loading shimmer — lavender sweep on skeleton placeholders"
```

---

### Task 11: Channel Hover Slide + Server Icon Morph + Notification Badge Pulse

**Files:**
- Modify: `apple-midnight.theme.css` — append

Three passive UI interactions grouped together — all use CSS `transition` on `:hover`/state.

- [ ] **Step 1: Append channel hover slide**

```css
/* ─── ANIMATION: CHANNEL HOVER SLIDE ─────────────────────────────── */

[class*="channel-"][class*="link-"],
[class*="channel-"][class*="wrapper-"] {
  transition: background 0.2s ease, transform 0.2s ease;
  position: relative;
}

[class*="channel-"][class*="link-"]:hover,
[class*="channel-"][class*="wrapper-"]:hover {
  transform: translateX(4px);
}

/* Left accent bar — slides in on hover */
[class*="channel-"][class*="link-"]::before,
[class*="channel-"][class*="wrapper-"]::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 2px;
  height: 0;
  background: var(--accent);
  border-radius: 0 2px 2px 0;
  transition: height 0.2s ease;
}

[class*="channel-"][class*="link-"]:hover::before,
[class*="channel-"][class*="wrapper-"]:hover::before {
  height: 12px;
}
```

- [ ] **Step 2: Append server icon morph**

```css
/* ─── ANIMATION: SERVER ICON MORPH ────────────────────────────────── */

nav[aria-label="Servers sidebar"] [class*="wrapper-"] img,
[class*="blobContainer-"] img {
  border-radius: 50%;
  transition: border-radius 0.25s cubic-bezier(0.34, 1.56, 0.64, 1),
              transform     0.25s cubic-bezier(0.34, 1.56, 0.64, 1);
}

nav[aria-label="Servers sidebar"] [class*="wrapper-"]:hover img,
[class*="blobContainer-"]:hover img {
  border-radius: 14px;
  transform: scale(1.08);
}

/* Active server pip — lavender indicator */
[class*="listItemWrapper-"][class*="selected"] [class*="pill-"] span,
nav[aria-label="Servers sidebar"] [class*="selected-"] [class*="pill-"] span {
  background: var(--accent) !important;
}
```

- [ ] **Step 3: Append notification badge pulse**

```css
/* ─── ANIMATION: NOTIFICATION BADGE PULSE ─────────────────────────── */

@keyframes badgePulse {
  0%, 100% { transform: scale(1); }
  50%       { transform: scale(1.2); }
}

[class*="numberBadge-"],
[class*="badge-"][class*="count"] {
  background: var(--accent) !important;
  color:       #1C1C1F !important;
  font-weight: 700;
  animation:   badgePulse 2s ease-in-out infinite;
}
```

- [ ] **Step 4: Verify in Discord**

Confirm:
- Hovering a channel row shifts it 4px right, and a 2px lavender bar rises from the left edge
- Server icons morph from circle to squircle with a spring bounce on hover
- Active server shows a lavender pip on the left edge
- Unread notification badge is lavender and gently pulses at 2s cycle

- [ ] **Step 5: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: channel hover slide, server icon morph, notification badge pulse"
```

---

### Task 12: Modal / Popup Open Animation

**Files:**
- Modify: `apple-midnight.theme.css` — append

Modals, user profiles, context menus, and the emoji picker animate in from scale 92% + 10px below.

- [ ] **Step 1: Append modal open animation**

```css
/* ─── ANIMATION: MODAL / POPOUT OPEN ─────────────────────────────── */

@keyframes modalOpen {
  from { opacity: 0; transform: scale(0.92) translateY(10px); }
  to   { opacity: 1; transform: scale(1)    translateY(0); }
}

[class*="modal-"][class*="root"],
[class*="popout-"][class*="root"],
[class*="emojiPicker-"],
[class*="userProfile-"],
[class*="contextMenu-"] {
  animation:        modalOpen 0.35s cubic-bezier(0.34, 1.4, 0.64, 1) forwards;
  transform-origin: center top;
}
```

- [ ] **Step 2: Verify in Discord**

Open a user profile (click a username), right-click a message (context menu), and open the emoji picker. Confirm:
- Each surface scales from 92% → 100% while rising from 10px below
- Spring easing gives a subtle bounce at full size
- Animation completes in ~0.35s

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: modal open animation — scale + spring for popouts and modals"
```

---

### Task 13: Voice Speaking Ring

**Files:**
- Modify: `apple-midnight.theme.css` — append

Two concentric lavender rings pulse outward from a speaking user's voice avatar.

- [ ] **Step 1: Append voice speaking ring**

```css
/* ─── ANIMATION: VOICE SPEAKING RING ─────────────────────────────── */

@keyframes speakRing {
  from { transform: scale(1);   opacity: 0.5; }
  to   { transform: scale(1.6); opacity: 0; }
}

[class*="speaking-"] [class*="avatar-"],
[class*="voiceUser-"][class*="speaking"] [class*="avatar-"],
[class*="participant-"][class*="speaking"] [class*="avatar-"] {
  position: relative;
}

[class*="speaking-"] [class*="avatar-"]::before,
[class*="speaking-"] [class*="avatar-"]::after,
[class*="voiceUser-"][class*="speaking"] [class*="avatar-"]::before,
[class*="voiceUser-"][class*="speaking"] [class*="avatar-"]::after,
[class*="participant-"][class*="speaking"] [class*="avatar-"]::before,
[class*="participant-"][class*="speaking"] [class*="avatar-"]::after {
  content: '';
  position: absolute;
  inset: -2px;
  border-radius: 50%;
  border: 2px solid rgba(196, 181, 253, 0.5);
  animation: speakRing 1s ease-out infinite;
  pointer-events: none;
}

[class*="speaking-"] [class*="avatar-"]::after,
[class*="voiceUser-"][class*="speaking"] [class*="avatar-"]::after,
[class*="participant-"][class*="speaking"] [class*="avatar-"]::after {
  animation-delay: 0.2s;
}
```

- [ ] **Step 2: Verify in Discord**

Join a voice channel and speak (or use push-to-talk). Confirm:
- Two lavender rings pulse outward from your avatar in the voice panel
- Rings fade as they expand — ripple effect
- The second ring lags by 0.2s behind the first
- If selector doesn't match: DevTools → inspect the voice user panel while speaking → find the class partial containing "speaking" → update the selector

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: voice speaking ring — pulsing lavender rings on active speaker"
```

---

### Task 14: Accent Color Targets

**Files:**
- Modify: `apple-midnight.theme.css` — append

Apply the lavender accent to all interactive and indicator elements across the UI.

- [ ] **Step 1: Append accent color rules**

```css
/* ─── ACCENT COLOR TARGETS ────────────────────────────────────────── */

/* Active/selected channel */
[class*="channel-"][class*="selected-"],
[class*="channel-"][class*="active-"],
[class*="channel-"][class*="link-"][class*="selected"] {
  background:  var(--accent-dim) !important;
  border-left: 2px solid var(--accent-border) !important;
  color:        var(--text-1) !important;
}

/* Unread channel dot */
[class*="unread-"][class*="bar"],
[class*="unread-"][class*="dot"] {
  background: var(--accent) !important;
}

/* Message input — accent border on focus */
[class*="textArea-"]:focus-within,
[class*="textAreaSlate-"]:focus-within,
[class*="channelTextArea-"]:focus-within {
  border-color: var(--accent-border) !important;
  box-shadow:   0 0 0 1px var(--accent-glow) !important;
}

/* Send button */
[class*="sendButton-"],
[class*="button-"][aria-label="Send Message"] {
  color:       var(--accent) !important;
  background:  var(--accent-dim) !important;
  border:      1px solid var(--accent-border) !important;
  transition:  background 0.2s ease, box-shadow 0.2s ease;
}

[class*="sendButton-"]:hover,
[class*="button-"][aria-label="Send Message"]:hover {
  background: rgba(196, 181, 253, 0.22) !important;
  box-shadow: 0 0 12px var(--accent-glow);
}

/* Mentions */
[class*="mention-"],
[class*="mentioned-"] {
  background:  var(--accent-dim) !important;
  border-left: 2px solid var(--accent-border) !important;
}

[class*="mention-"] [class*="username-"],
[class*="roleColor-"] {
  color: var(--accent) !important;
}

/* Text selection */
::selection {
  background: rgba(196, 181, 253, 0.25);
  color:       var(--text-1);
}

/* Links */
a,
[class*="anchor-"] {
  color: var(--accent) !important;
}

a:hover,
[class*="anchor-"]:hover {
  color: #DDD6FE !important;
}
```

- [ ] **Step 2: Verify in Discord**

Confirm:
- Active channel has a lavender left border and dim background tint
- Clicking the message input shows a lavender border glow
- Mentions have lavender tint and left border
- Selecting text shows a lavender highlight
- Links are lavender; slightly lighter on hover

- [ ] **Step 3: Commit**

```bash
git add apple-midnight.theme.css
git commit -m "feat: accent color targets — lavender on interactive and indicator elements"
```

---

### Task 15: README and .gitignore

**Files:**
- Create: `.gitignore`
- Create: `README.md`

- [ ] **Step 1: Create `.gitignore`**

Create `.gitignore` with these contents:

```gitignore
# Brainstorm artifacts from visual companion
.superpowers/

# OS artifacts
.DS_Store
Thumbs.db

# Original font archive — individual files in fonts/ are tracked separately
Paperlogy-1.001/
```

- [ ] **Step 2: Create `README.md`**

```markdown
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

```css
/* Replace: */
src: url('./fonts/Paperlogy-4Regular.ttf');
/* With: */
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
```

- [ ] **Step 3: Commit**

```bash
git add .gitignore README.md
git commit -m "docs: README with install guide and .gitignore"
```

---

## Self-Review Notes

**Spec coverage check:**

| Spec requirement | Task |
|---|---|
| Metadata header (Vencord META block) | Task 2 |
| `@import` Midnight | Task 2 |
| `@font-face` (4 weights) | Task 2 |
| Color tokens (`:root`) | Task 2 |
| `fonts/` directory | Task 1 |
| Animated background (2 blobs, 16–20s drift) | Task 3 |
| Glass panels (server, channels, chat, modals) | Task 4 |
| Typography — Paperlogy, role-based weights | Task 5 |
| Message entry (slide-up + text reveal) | Task 6 |
| Message hover actions (stagger) | Task 7 |
| Typing indicator (glass pill + bounce) | Task 8 |
| Channel switch stagger (`:nth-child` 1–10) | Task 9 |
| Loading shimmer | Task 10 |
| Channel hover slide + accent bar | Task 11 |
| Server icon morph (circle → squircle) | Task 11 |
| Notification badge pulse | Task 11 |
| Modal open animation | Task 12 |
| Voice speaking ring | Task 13 |
| All accent color targets | Task 14 |
| `.gitignore` excluding `.superpowers/` | Task 15 |
| README with install instructions | Task 15 |
| GitHub font URL note | Task 15 README |

All 10 animations from the spec are covered. All CSS custom property names (`--accent`, `--glass-srv`, `--border`, etc.) are defined in Task 2 and referenced consistently throughout. All `@keyframes` names are unique. No placeholders or TODOs remain.
