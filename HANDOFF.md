# HANDOFF — Aug 10 2026 · portrait-rig pending

## Shipped this session
- Live game: https://marksher.github.io/trump-punch/
- Current source is the static canvas game in `index.html`.
- Current live scene uses the compact game composition in `assets/rally-crowd-cartoon.png`.
- The arm rig is being re-anchored to the backdrop’s natural image coordinates so portrait cover-cropping cannot detach it.
- `archive/dance.jpg` is the current pose/proportion reference; the clean transparent arm cutout remains the rendered upper-arm and forearm/fist art.
- The right-hand vertical control mirrors the left hand in the opposite direction.
- Shoes target randomized micro-points across each hand’s sweep; three misses lock that side and remove its incoming shoes.
- Last live check before this change: the compact scene rendered at 1280×820. Portrait behavior is the regression being fixed now.

## In flight (exact state, file paths)
- Gameplay is close; the user says the action and rules are working.
- The compact framing rollback is live after the full-viewport reference made Mr. Edwards too large.
- In flight: verify the new backdrop-anchored rig at portrait and desktop sizes.

## Next steps (ordered, actionable)
1. Verify portrait and desktop live screenshots after the new rig ships.
2. If needed, adjust only the measured backdrop/dance reference coordinates; keep the shared transform.
3. Keep the gameplay contract: two-joint arms, mirrored slider, reachable targets, three-hit side lockout.
4. Verify desktop and mobile live screenshots before reporting completion.

## Open questions
- Confirm whether the dance-reference proportions need any remaining shoulder/arm alignment polish.

## Key paths & commands
- Source: `/Users/runners/working/trump-punch/index.html`
- Required assets: `assets/rally-crowd-cartoon.png`, `assets/mr-edwards-arm-segments.png`, `assets/mr-edwards.png`, `archive/dance.jpg`, `assets/ymca.mid`
- Live URL: `https://marksher.github.io/trump-punch/`
- Deploy: push `main`; GitHub Pages serves the repository.
