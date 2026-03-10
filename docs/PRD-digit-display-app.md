# Product Requirements Document: Sipnayan ChamPIon Digit Display App

**Version:** 0.1  
**Status:** Draft  
**Last updated:** March 10, 2026  

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

- **Large digit (center):** The digit the contestant should recite *right now*.  
  - Dominant size (e.g. 40–60% of viewport height).  
  - High contrast, clear font (e.g. sans-serif, bold).  
  - Single character: `0`–`9` or the decimal point when at position 0.

- **Small digits (below):** Running context of π so far.  
  - Example: `3.1415926535…` with the current position aligned or highlighted.  
  - Smaller font (e.g. 12–20% of large digit).  
  - Same font family; can be lighter weight.  
  - Wraps or scrolls if needed when the sequence gets long.

- **Initial state:** On load, show large digit `3` and small digits `3.14159…` (or configurable length), with the “current” position at the first digit after the decimal (e.g. `1` in `3.14159`).

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
| **Space** or **Enter** | Next digit | Primary “advance” action; most used. |
| **Backspace** or **←** (Left arrow) | Previous digit | For corrections or if contestant repeats. |
| **Home** | Jump to start | Show `3` and `3.14159…` from the beginning. |
| **End** | Jump to end of loaded digits | Optional; for testing or long runs. |
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
- **Visual feedback (optional):** Brief, subtle highlight or animation on “next” (e.g. 100 ms) so the operator sees that the key was registered, without distracting the contestant.

### 5.3 Alignment with Contest Rules

- **10-second rule:** Optional **pause timer** (e.g. 10 s countdown) that appears when the operator hasn’t advanced for 5+ seconds; warning at 8 s, “Time’s up” at 10 s. Can be toggled (e.g. “T” for timer on/off) so it’s available but not mandatory.  
- **No external aid:** App does not show answers or hints; it only displays the official sequence.  
- **Neutral branding:** Optional small “Sipnayan ChamPIon 2026” or logo in a corner; not distracting from the digits.

### 5.4 Practice & Rehearsal

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
