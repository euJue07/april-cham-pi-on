---
name: Judge timing / next-digit fix
overview: "Align with judge workflow: corner = expected digit; on X show that digit in red on the big number and below; no big number at start (nothing to compare yet)."
todos:
  - id: red-formatting
    content: "On X, show expected (corner) digit in red on big number and keep it until next keypress; same digit red in context."
  - id: corner-as-expected
    content: "Corner shows the digit the contestant should say (expected); label e.g. expected or current."
  - id: no-big-at-start
    content: "Initial and after each C/X: big number empty; only corner shows next digit to say."
  - id: docs
    content: "Update PRD and help text for new flow and red feedback."
isProject: false
---

# Judge workflow: red feedback and "no big number" at start

## Desired behavior

- **Judge** looks at the **corner** for the digit the contestant **should** say (e.g. 1).
- **Contestant** says something (e.g. 3).
- **Judge presses X (wrong):** The **big number** shows **1** (the expected digit from the corner) in **red**. That **1** also appears in **red** in the small context below. So the red formatting is: "you were supposed to say 1."
- **At the beginning:** There is **no big number** — the contestant hasn't said anything yet, so nothing is being compared. Only the corner shows the first digit to say (e.g. 1).

So:

1. **Corner** = expected digit (what the contestant should say). Judge compares contestant's answer to this.
2. **On X:** Big number = expected digit in **red**; that same digit appears **red** below. Do **not** advance the big number to the next digit until the next keypress.
3. **Initial / waiting:** Big number is **empty**; only the corner shows the next digit to say.

## Current behavior (what to change)

- **advanceWrong()** in [digit-display.html](digit-display.html): marks current index wrong, advances `index`, then **render()** sets the big number to **getCurrentChar()** (the *next* digit) and removes the `.wrong` class. So the judge never sees the expected digit (e.g. 1) in red on the big number — they see the next digit (e.g. 4) and the 1 only in the small grid.
- **Initial state:** Big number shows "3" (index 0). So there is always a big number.

## Implementation plan

### 1. Red formatting on X (keep expected digit in red on big)

- In **advanceWrong()**: before advancing, store **expected = getCurrentChar()**. Mark **wrongIndices[index] = true**, then **index++**.
- Introduce a **wrong-feedback state**: e.g. `showingWrongDigit = expected` (or a boolean + the digit). In **render()**: if `showingWrongDigit` is set, set the big number to that digit and add the `.wrong` class; do **not** set big number to `getCurrentChar()` or remove `.wrong`.
- Clear **showingWrongDigit** at the start of **advance()** and **advanceWrong()** (so the next C or X starts a new round and render can show the next state). After clearing, normal render will show the next digit or empty (see below).
- Result: after X, the big number stays on the expected digit in red; that digit is already in the context in red; on next C/X the big number updates (or goes empty in "waiting" mode).

### 2. Corner = expected digit

- **Corner** should show the digit the contestant should say. With the new "waiting" model (see below), that is **fullSeq[nextToSay]** where `nextToSay` is the next position to be judged. So corner label can stay "Next:" (next to say) or become "Expected:" / "Say:"; value = that digit. No change to *which* digit is shown if we keep "next digit to say" semantics; only ensure the big number on X shows this same expected digit in red.

### 3. No big number at start and after each C/X ("waiting" state)

- **State meaning:** Treat **index** as "number of digits after the decimal that have been confirmed (correct or wrong)." So:
  - **index 0:** No digits after decimal yet. Context = "3." only. **Next to say** = fullSeq[1] (e.g. "1"). **Corner** = "1". **Big** = **empty**.
  - After **C** or **X**: we add the digit we just judged to the sequence and set index = 1, 2, … Then we go back to "waiting": **big = empty**, **corner = fullSeq[index + 1]** (next digit to say).
- **Initial load:** index = 0, big = empty (or a placeholder like "—" or space), corner = fullSeq[1], context = "3." (no digits after decimal in the grid yet, or only "3.").
- **Render()** when in waiting (no wrong feedback): if "waiting" (e.g. index 0, or a dedicated flag "just advanced and waiting"), set big number to empty (or hide it). When we have wrong feedback, big = expected digit in red as above.
- **Backspace / previous:** Must be consistent: going back should restore "waiting" for that position (corner = digit at that position, big = empty) and adjust context accordingly.

This may require a small refactor: e.g. **nextToSayIndex = index + 1** (position in fullSeq of the digit to say), and "waiting" means we are not currently showing a feedback digit on the big number. So:
- **Waiting:** big empty, corner = fullSeq[nextToSayIndex], context = all digits from fullSeq[1] through fullSeq[index].
- **After C:** advance index (add digit to context), then we're waiting again: big empty, corner = fullSeq[index + 1].
- **After X:** add digit to context in red, advance index, set showingWrongDigit = fullSeq[index] (the one we just marked wrong), render shows that in red on big; on next keypress we clear showingWrongDigit and we're waiting (big empty).

So the "no big number at start" implies we need a clear notion of "waiting" and that the big number is only filled when we are showing feedback (correct digit briefly or wrong digit in red until next keypress). The PRD currently says the large digit is "the digit the contestant should recite right now" — we're changing to "big number is only used for feedback after judge presses C/X; at start and between rounds it's empty."

### 4. Files to touch

| File | Change |
|------|--------|
| [digit-display.html](digit-display.html) | Add `showingWrongDigit` (or equivalent); in **advanceWrong()** set it to expected digit and do not let render overwrite big with next digit; in **render()** branch on showingWrongDigit to show that digit in red on big. Implement "waiting" state: big empty when no feedback; corner = next digit to say; initial index 0 with big empty and context "3."; advance/previous and goStart/goEnd consistent with new index semantics. Update corner label/copy if desired. Update help overlay text. |
| [docs/PRD-digit-display-app.md](docs/PRD-digit-display-app.md) | Update §3.1: corner = expected digit; big number only for feedback (empty at start and between rounds); on X, big shows expected digit in red and it appears red below. Bump "Last updated." |

### 5. Optional: brief "correct" feedback

- After **C**, you could show the correct digit on the big number (normal color) for a short moment, then clear to empty for the next round. Not required by your message; can be added later if desired.

## Summary

- **Red formatting:** On X, big number shows the **expected** (corner) digit in red and stays that way until next keypress; that digit is red in the context below.
- **Corner:** Shows the digit the contestant should say (expected).
- **No big number at start:** Initial and after each C/X, big number is empty; only the corner shows the next digit to say.
