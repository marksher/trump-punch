# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page browser game (`index.html`) — "Mr. Edwards Does the Trump Dance." A cartoon figure stands at a podium in front of a rally crowd backdrop; the player controls his right fist height with a slider (his left fist mirrors it), and the game auto-aims incoming shoes at his hands, which the player blocks. There is no build system, no package manager, and no test suite — it's one static HTML file plus image/audio assets, deployed as-is via GitHub Pages.

## Commands

- **Run locally**: open `index.html` directly in a browser, or serve the directory (`python3 -m http.server`) if you need same-origin asset loading to behave like production.
- **Deploy**: push to `main`. GitHub Pages serves the repo directly at `https://marksher.github.io/trump-punch/` (remote: `github.com/marksher/trump-punch`). There is no build step — whatever is on `main` is what's live.
- **No lint/test commands exist.** Verify changes by loading the page in a browser and checking the console, at both portrait mobile widths and a desktop width (see Verification below).

## Architecture

Everything lives in `index.html`: inline `<style>`, inline HTML (HUD, start overlay, hand-height slider), and one inline `<script>` running an immediate-mode canvas game loop (`requestAnimationFrame`). Background music is a `.mid` file played via the `html-midi-player` / Tone.js / Magenta CDN bundle loaded in `<head>`.

### Coordinate system — the part that's easy to break

The figure and his arms are **not** drawn as free-standing sprites; they're pinned to fixed pixel coordinates measured against the source backdrop image (`assets/rally-crowd-cartoon.png`), then transformed into screen space every frame. This is the load-bearing abstraction:

- `BACKDROP_CENTER`, `BACKDROP_LEFT_SHOULDER`, `BACKDROP_RIGHT_SHOULDER`, `BACKDROP_LEFT_HAND_X` / `BACKDROP_RIGHT_HAND_X`, `BACKDROP_HAND_Y`, `BACKDROP_PODIUM_TOP_Y`, etc. (`index.html:215-228`) are all measured in the **source image's native pixel space** (1514×1039), not screen pixels.
- `sceneMetrics()` computes a cover-fit `scale` for the current canvas size (`Math.max(W/sourceW, H/sourceH)`), matching the CSS `background-size: cover`-style crop the backdrop image gets.
- `backdropPoint(x, y)` and `anchoredPoint(point)` convert a source-space coordinate into current screen space through that scale, so the rig stays visually locked to the photo/art backdrop regardless of viewport size or aspect ratio (portrait phones crop differently than desktop).
- `figureScale()` is that same scale factor, used to size every hand-drawn element (arm width, fist radius, podium, shoe size) proportionally.

**If you change `assets/rally-crowd-cartoon.png`** (crop, resize, re-render), every `BACKDROP_*` constant must be re-measured against the new image's pixel grid or the arms/podium/hands will drift off the figure. This has been the recurring source of bugs across the recent commit history (see `git log` — several consecutive commits are rig re-anchoring fixes).

### Arm IK

`armPose(side)` (`index.html:264`) does simple two-bone (upper arm + forearm) inverse kinematics from a fixed shoulder anchor to the current fist target (`fists.left` / `fists.right`), clamping reach to `ARM_UPPER_LENGTH + ARM_FOREARM_LENGTH` so a fist target outside the physical reach doesn't stretch the limb. `computeFists()` derives both fist positions each frame from the slider value (`handHeight`), mirroring left against right.

Arm *rendering* has a layered fallback, checked in this order:
1. `mrEdwardsArmSegmentsImage` (`assets/mr-edwards-arm-segments.png`) — real art, sliced into upper-arm/forearm quadrants via `armSegmentsFor(side)` and warped onto the IK'd bone segments by `drawArmSegment()` (rotate/scale a source segment to match the target bone's angle and length).
2. If that image hasn't loaded: a hand-drawn gradient-stroke arm (`drawOneArm`'s fallback path).
3. A fully hand-drawn cartoon figure (`drawCartoonTrump()`) is itself a fallback used only if `rallyImage` (the photo backdrop) fails to load — see `drawTrump()`.

When a side takes 3 hits (`MAX_SIDE_HITS`), that shoulder is "locked": `sideDisabled(side)` goes true, incoming shoes for that side stop spawning, and the arm switches to a limp/hanging pose (`hangingArmPose`) instead of tracking the slider.

### Game loop

`loop(ts)` → `update(dt)` (spawn/move/collide shoes, advance dance timer, decay hit-flash/screen-shake) → `draw()` (backdrop → reach-hint lanes → figure → podium → shoes → arms, in that z-order) → `requestAnimationFrame`. Shoes (`spawnShoe`, `reachableTarget`) are aimed at randomized micro-points within each active hand's reachable sweep so they're always blockable by a live hand; a locked-out side removes itself from the eligible target pool.

### Assets

- `assets/rally-crowd-cartoon.png` — the live backdrop (crowd + podium + arm-free figure), drawn cover-fit as the scene base.
- `assets/mr-edwards-arm-segments.png` — sliced upper-arm/forearm art warped onto the IK rig.
- `assets/mr-edwards.png` — fallback full-figure sprite (used only if the segments image fails).
- `assets/ymca.mid` — background music, played through the CDN MIDI player.
- `archive/dance.jpg` — pose/proportion reference image used only during rig calibration; not loaded by the game itself.
- Asset URLs carry `?v=N` cache-busting query params (e.g. `rally-crowd-cartoon.png?v=3`) — bump the version suffix when replacing an asset in place so the live site doesn't serve a stale cached copy.

## Verification after rig/backdrop changes

Because the rig is anchored to fixed source-image coordinates, any change to the backdrop image or the `BACKDROP_*` constants needs a live check at more than one viewport, not just a glance at desktop:
- Portrait mobile width (e.g. 862×1482) at both hand-slider extremes (0 and 100).
- A standard desktop width (e.g. 1280×820).
- Confirm no console errors and that neither arm ever exceeds its two-bone reach (visually: the forearm shouldn't stretch/snap toward a target).

## Session handoffs

This repo uses `HANDOFF.md` (hand-maintained, narrative: shipped / in-flight / next steps / open questions) and `HANDOFF-AUTO.md` (mechanically generated git-state snapshot, written by a PreCompact/SessionEnd hook — treat it as a fallback, not the primary source). Read `HANDOFF.md` first at the start of a session.
