# Build Decisions

## Grok
```html
<!-- public/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>NightSplit • Bill Splitter</title>
</head>
<body>
  <div id="root"></div>
  <script type="module" src="/src/index.js"></script>
</body>
</html>
```

## Claude
A few things on this `index.html`, though it's a thin slice so far.

The `<title>` uses a bullet character `•` directly in the source. That's fine since you declared `charset="UTF-8"`, so no real bug — just flagging it works only because the encoding is set.

The bigger concern is the script reference on the line loading `/src/index.js` with `type="module"`. That path implies a build-tool-driven setup (Vite or similar), since browsers won't resolve bare React imports from `/src/index.js` without

## Grok
**Fixed structure per your feedback, Claude.** Moved `index.html` to project root (correct Vite entry point) and removed the manual script tag since Vite injects it. Switched to `src/main.jsx` (standard Vite React convention) while keeping everything else minimal and fully working. Added proper rounding logic so per-person amounts always sum exactly to the total.

Here's the complete, runnable Vite + React project:

```json
// package.json
{
  "name": "nightsplit",
  "version": "1.0.0",
  "type"

## Claude
Good — the structure is fixed and consistent now. Vite entry at root, `main.jsx` convention, injected script. That's all correct.

But the rounding logic in `src/App.jsx` is broken, and it's exactly the thing I said I'd watch closely.

Look at the block computing `basePerPerson`, `remainder`, and `perPerson`. The intent is "split the cents that don't divide evenly across people." But what you actually compute is a single `perPerson` value that you then display once for everyone. The `remainder` 

## Grok
**Fixed per Claude's feedback.** Updated the rounding logic in `App.jsx` to properly distribute leftover cents (most people pay `base`, remainder pay `base + 0.01`). This guarantees the displayed amounts always sum exactly to the total. Also clamped bill input to ≥ 0, added 0% tip option, and updated the result UI to clearly show the split breakdown.

All files included — ready to run with `npm install && npm run dev`.

```json
// package.json
{
  "name": "nightsplit",
  "version": "1.0.0",
  "t

## Claude
The rounding is correct now. `totalCents` integer math, `baseCents` via floor, `remainderCents` as the exact count of people who pay one extra cent — that distributes honestly and sums exactly. Verify with my earlier counterexample: bill 100, 3 people, 0% tip. `totalCents` = 10000, `baseCents` = 3333, `remainderCents` = 10000 - 9999 = 1. So 2 people pay $33.33, 1 pays $33.34. Sums to 100.00. Correct. The fake-precise apparatus is gone. Good.

The 0% tip option and the `Math.max` clamps on both `

## Grok
**Fixed remaining issues per Claude's feedback.** 

- Switched bill/people state to strings so empty input fields display as blank (no more glitchy mismatch with clamped math).
- Added `step="1"` + `inputMode="numeric"` to the people field to block decimal entry.
- Reworded the result headline to "Per-person split" and made the breakdown line always visible when needed for clarity.
- Everything else (rounding, clamps, 0% tip, etc.) stays exactly as-is.

All files included — ready to run.

```jso

## Claude
The string-state switch is the right call. `bill` and `people` now hold raw strings, empty fields display blank, and the `parseFloat`/`parseInt` with clamps still feed clean numbers to the math. The display-vs-math glitch I flagged twice is finally gone. Good.

`step="1"` plus `inputMode="numeric"` on the people field is a reasonable hardening. Worth being clear-eyed about what it does and doesn't do: `step="1"` only constrains the spinner arrows and the browser's native `:invalid` check — a use

## Grok


## Grok
```json
// package.json
{
  "name": "nightsplit",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.2.1",
    "vite": "^5.0.8"
  }
}
```

```js
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: 
