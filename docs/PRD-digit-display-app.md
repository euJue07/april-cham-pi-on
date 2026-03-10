# Product Requirements Document: Sipnayan ChamPIon Digit Display App

**Version:** 0.4  
**Status:** In progress  
**Last updated:** March 11, 2026  

---

## 1. Overview

### 1.1 Purpose

The **Digit Display App** is a facilitator tool for the **Sipnayan ChamPIon 2026 Final Round**. It shows π digits on screen so contestants can recite in sync: one large “current” digit and a small running context (e.g. `3.1415…`) below. The app is a **single, self-contained HTML file** that runs offline (e.g. from a USB drive or local copy) so it can be used in any venue without internet or installation.

### 1.2 Problem Statement

- Final round requires **on-screen digits** for recitation.
- Facilitators need a simple, reliable way to **advance digits** in sync with the contestant.
- Setup must work **offline** and without extra software (browser only).
- Output must be **one HTML file** for easy sharing and forwarding.

### 1.3 Success Criteria

- Facilitator can control the displayed digit with minimal effort (keyboard-first).
- Contestant sees one clear “current” digit and context without distraction.
- App runs from a single HTML file in any modern browser, fully offline.
- Display is readable from audience distance (amphitheatre).

---

## 2. User Roles

| Role | Description | Primary need |
|------|-------------|--------------|
| **Facilitator / Operator** | Runs the app during the Final Round | Reliable, simple controls to show next/previous digit |
| **Contestant** | Recites π in sync with the screen | One clear current digit + optional context |
| **Judge / Audience** | Observe recitation | Readable display, no accidental changes |

---

## 3. Core Features

### 3.1 Display Layout (Required)

- **Large digit (center):** Used only for **feedback** after the judge presses C or X.  
  - **At start and between rounds:** The large digit is **empty** (contestant has not said anything yet; nothing is being compared).  
  - **After C (correct):** The **confirmed** digit is shown in normal color and stays until the next keypress.  
  - **After X (wrong):** The **expected** digit (the one from the corner) is shown in **red** and stays until the next keypress; that same digit appears in red in the small context below.  
  - Dominant size when shown (e.g. 40–60% of viewport height). High contrast, clear font. Single character: `0`–`9`.

- **Small digits (below):** Full sequence of π so far (from the start through the last confirmed position).
  - Shown in a grid of **20 characters per row** (each character in its own cell, including the leading `3` and decimal point).
  - Wrong digits in a distinct “wrong” color; no “current” digit highlight in the grid (the corner shows the expected digit).
  - Smaller font (e.g. 12–20% of large digit). Same font family; can be lighter weight.
  - When the grid exceeds **10 rows**, the context area becomes **scrollable** (vertical scroll); the view scrolls so the last confirmed digit stays visible.

- **Initial state:** On load, the large digit is **empty**. Small digits show **3.** only (no digits after the decimal yet). The corner shows the first digit the contestant should say (e.g. **1**).

- **Facilitator-only expected-digit guide:** A subtle **“Expected: &lt;digit&gt;”** hint in the lower right (muted colour, small font) so the judge can see which digit the contestant should say and compare their answer before pressing C (correct) or X (wrong). Not prominent enough to aid the contestant.

- **Wrong-answer feedback:** When the operator marks the digit as wrong (**X**), the **expected** digit (from the corner) is shown in **red** in the large digit and remains until the next keypress; that same digit appears in the small context grid in a distinct “wrong” colour.

### 3.2 Digit Source

- Digits of π are embedded in the single HTML file (e.g. as a string or in a `<script>`).  
- Sufficient length for the contest (e.g. 500–2000+ digits).  
- No network or external file required.

### 3.3 Single-File, Offline Delivery

- **Output artifact:** One `.html` file.  
- No external CSS/JS/images; everything inlined (or data URIs if needed).  
- No server; open file in browser (file:// or after transfer).  
- Usable after forwarding (email, USB, shared drive) without install steps.

---

## 4. Keyboard Controls (Suggested)

All controls are **keyboard-only** so the operator never has to grab the mouse during recitation.

| Key | Action | Notes |
|-----|--------|--------|
| **Space** or **Enter** | Next digit (correct) | Advance without marking wrong; primary “next” action. |
| **X** | Wrong | Expected digit is shown in red (large + context) and stays until next keypress; then advance to next. |
| **C** | Correct — next digit | Same as Space/Enter; advance with no wrong styling. |
| **Backspace** | Previous digit | For corrections or if contestant repeats. |
| **Home** | Jump to start | Large digit empty, context “3.”, corner shows first expected digit; clears wrong markers. |
| **Double-tap E** | Jump to end | Only way to end the game / jump to last digit; avoids accidental end from single keypress. |
| **F** or **F11** | Toggle fullscreen | Maximize visibility in amphitheatre. |
| **R** | Reset to start | Same as Home; mnemonic “Reset”. |
| **?** or **H** | Show/hide help overlay | Short list of controls; hide after a few seconds or on next key. |

**Reserved / avoid:**  
- Avoid binding keys that browsers use (e.g. Ctrl+W, Ctrl+N).  
- Optional: **Esc** closes fullscreen or help overlay only, does not change digit.

**Accessibility:**  
- Ensure focus stays in the page (e.g. no focusable inputs) or add a “Click to focus” hint so keypresses are always captured.

---

## 5. User Experience Improvements

### 5.1 Readability & Visibility

- **High contrast:** Dark digits on light background (or light on dark) with a contrast ratio that meets basic accessibility (e.g. 4.5:1+).  
- **Large tap targets:** If touch is ever used, make the “next” area big (e.g. full center or bottom area).  
- **Fullscreen mode:** One-key fullscreen (e.g. F or F11) for projector/screen.  
- **No layout shift:** When advancing, only the digit and the trailing context change; no jumping or resizing.

### 5.2 Facilitator Experience

- **Help overlay:** “?” or “H” shows a short cheat sheet (e.g. “Space = next, Backspace = previous, F = fullscreen”); disappears on next key or after 3–5 seconds.  
- **Click-to-focus:** Big “Click anywhere to enable keyboard” on first load so keypresses are not lost.  
- **Pre-game countdown:** After clicking to enable, a 3-second countdown (3 → 2 → 1) is shown before the game starts; the 10 s bar and keyboard controls become active when the countdown ends. Restart (R / Home) also shows the countdown before resetting to the start.
- **Mobile/tablet controls:** On viewports ≤1024px, a fixed bottom “keyboard” region shows touch-friendly buttons (Next, Wrong, Prev, Restart, Help, Fullscreen, End) with the meta block (Digit #N, Expected) just above the keys, like a phone keyboard; desktop keeps keyboard-only plus the floating R button.
- **Visual feedback (optional):** Brief, subtle highlight or animation on “next” (e.g. 100 ms) so the operator sees that the key was registered, without distracting the contestant.

### 5.3 Alignment with Contest Rules

- **10-second bar (judge guide):** A **per-digit** 10 s bar appears below the large digit and depletes over 10 seconds, changing color (e.g. accent to yellow to red) as time runs down. It is a **guide for the judge only** and does **not** end the game or advance the digit when it reaches zero; the judge still presses C/X or next as usual. The bar restarts whenever the displayed digit changes (advance, wrong, previous, jump to start/end).  
- **No external aid:** App does not show answers or hints; it only displays the official sequence.  
- **Neutral branding:** Optional small “Sipnayan ChamPIon 2026” or logo in a corner; not distracting from the digits.
- **Only double-tap E** ends the game (jump to last digit); the **End** key is not bound.
- **End-game summary:** When the game is over (position at last digit), an accuracy summary is shown with correct and wrong counts and percentages; the small context grid below remains visible as history.

### 5.4 Practice & Rehearsal & Rehearsal

- **Practice mode (optional):** Same layout and controls, with optional “hide large digit” so a contestant can practice reciting and then reveal to check.  
- **Configurable start index:** Optional URL hash or simple form (e.g. `?start=100`) to start from digit 100 for practice from a specific position.  
- **Digit count:** Optional small “Digit #N” (e.g. “Digit #42”) for facilitators/judges to record scores.

### 5.5 Robustness

- **No accidental advance:** Only explicit keypresses advance; no auto-advance unless explicitly added as an optional feature.  
- **State in URL (optional):** Encode current index in hash (e.g. `#50`) so refreshing or sharing the link preserves position (useful for rehearsal).  
- **Graceful degradation:** If JavaScript fails, show at least “3.14159…” as static text so the contest can still proceed with a printed sheet.

---

## 6. Non-Functional Requirements

| Area | Requirement |
|------|-------------|
| **Compatibility** | Works in recent Chrome, Firefox, Safari, Edge (last 2 versions). |
| **Performance** | Instant response to keypress; no noticeable delay when advancing. |
| **Offline** | No external requests; all assets in the single HTML file. |
| **Portability** | Single file; easy to duplicate and forward. |
| **Privacy** | No analytics, no external calls; no data leaves the device. |

---

## 7. Out of Scope (V0)

- Server or backend; multi-device sync; user accounts.  
- Calculator, notes, or any “aid” beyond displaying the official π sequence.  
- Mobile app or native install; browser-only.  
- Storing or reporting scores (handled separately by judges).  
- Multiple constants (e.g. e, φ); only π for ChamPIon 2026.

---

## 8. Deliverables

| Item | Description |
|------|-------------|
| **Single HTML file** | Self-contained `digit-display.html` (or agreed name) with inlined π digits, styles, and script. |
| **Short operator guide** | One-page instructions: how to open, focus, and use keyboard (can be in repo README or comments in HTML). |
| **Optional:** | Separate one-pager “PRD” or “Design notes” (this document) for future iterations. |

---

## 9. References

- [Sipnayan ChamPIon 2026](sipnayan-champion-2026.md) — Official contest rules, Final Round mechanics, scoring, and 10-second rule.
- **Final round:** Recitation with on-screen digits; contestant recites in sync; one digit at a time; 10 s hesitation rule; penalties for incorrect digits.

---

*This PRD is the single source of truth for the Digit Display App scope and behavior. Implementation should follow these requirements so the app is ready for Sipnayan ChamPIon 2026 Final Round.*
