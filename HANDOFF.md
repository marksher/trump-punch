# HANDOFF — Aug 09 2026 · main @ be2c45f

## Shipped this session
- Live game: https://marksher.github.io/trump-punch/
- Current source is the static canvas game in `index.html`.
- Current live scene is `assets/rally-crowd-cartoon.png` with the previous compact Mr. Edwards framing.
- Current arm art is `assets/mr-edwards-arm-segments.png`, rendered as separate upper-arm and forearm/fist pieces.
- The right-hand vertical control mirrors the left hand in the opposite direction.
- Shoes target randomized micro-points across each hand’s sweep; three misses lock that side and remove its incoming shoes.
- Last live check: both arm assets loaded, no console errors, and left-side lockout produced `L LOCK` with zero left-entry shoes.

## In flight (exact state, file paths)
- Gameplay is close; the user says the action and rules are working.
- Visual composition is not settled. The user wants the supplied painterly reference used as the visual source, with the body separated from the arms and the arms attached at real shoulders/elbows.
- The user’s latest direction is a full reset for a new model: keep the current arms’ apparent size, restore/replace the rest of the scene deliberately, and avoid further incremental guesses.
- Current checked-in commit: `be2c45f`.

## Next steps (ordered, actionable)
1. Start from the supplied reference image, not an approximated background.
2. Make a clean body/podium layer with the original raised arms removed.
3. Keep the current gameplay contract: two-joint arms, mirrored slider, reachable targets, three-hit side lockout.
4. Calibrate all joints in the same coordinate system as the body image at desktop and mobile sizes.
5. Verify with a live screenshot before reporting completion.

## Open questions
- Confirm whether the canonical body should be the supplied close-up reference or the prior wide rally composition.
- Confirm whether a locked arm should use straightened photo segments or a simpler hanging silhouette.

## Key paths & commands
- Source: `/Users/runners/working/trump-punch/index.html`
- Required assets: `assets/rally-crowd-cartoon.png`, `assets/mr-edwards.png`, `assets/mr-edwards-arm-segments.png`, `assets/ymca.mid`
- Live URL: `https://marksher.github.io/trump-punch/`
- Deploy: push `main`; GitHub Pages serves the repository.
