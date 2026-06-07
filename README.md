# Interchained × Bitcoin — Difficulty, Side by Side

An interactive booth that puts a **Bitcoin-style chain next to Interchained (ITC)** and lets the public *feel* the single biggest engineering difference: how each chain handles a change in mining power. Yank the hashrate and watch Interchained's **DarkGravityWave3-Nova** re-target every block while Bitcoin's 2,016-block epoch sits frozen for weeks. Everything runs 100% client-side — real SHA-256 and a faithful port of the real retarget logic. No node, no backend, no install.

> This is a sibling to the Playground Lab. The Playground is the hands-on "mine it yourself" lab; the DAA Showcase is the guided BTC | ITC journey built on the same real engine. They link to each other but stand alone.

---

## The journey

A self-driven scroll. Each beat renders **Bitcoin (orange) and Interchained (cyan) side by side.**

1. **Hero — "Bitcoin walked. Interchained runs."** Two identity cards lay out the shared DNA and the diverging spec: 10 min vs 60 s blocks, 2,016-block epochs vs every-block retargeting, SHA-256 vs Yespower→SHA-256.
2. **The shared foundation.** A *live, genuine* SHA-256 miner grinds nonces until the digest clears a target — proof that both chains rest on the same hard problem. Interchained only changes the rules *around* it.
3. **The centerpiece — Difficulty.** The DAA station (see below). Yank the hashrate, fire preset shocks, and watch the two responses diverge block-by-block on a shared chart.
4. **Height-based migration.** Scrub the block height across the fork and watch the proof-of-work algorithm hand off from Yespower to SHA-256 in lockstep, with no flag-day coordination.
5. **By the numbers.** The spec sheet, BTC vs ITC, row by row.
6. **The energy question.** The eco-grants framing: proof-of-work without the warehouse.
7. **Why ITC.** The "so what" close — resilience under stress is the point.

---

## The centerpiece: the DAA station

This is the reason the booth exists. One shared pool of mining power drives **two real retarget algorithms at once.**

**What the visitor can do**
- **Yank the hashrate** on a log slider (0.1× → 20×) and watch difficulty respond.
- **Fire preset shocks:** *"Whale plugs in 10×"* and *"Half the miners leave."*
- **Fast-forward to Bitcoin's retarget** — honestly steps the sim until Bitcoin's 2,016-block epoch finally ends, so you can see how long "until it can react" really is.
- **Read both chains live:** difficulty, actual vs target block time (color-coded), and — for Bitcoin — a meter counting down the blocks/days until its next possible adjustment.
- **Follow the lesson:** five guided steps that auto-advance with the action, so the education coincides with the tinkering.
- **Cross the PoW fork** on the migration scrubber.

The takeaway lands on its own: shock the network and Interchained is back on its 60-second target within ~12–24 blocks, while Bitcoin is stuck off-target for the rest of its epoch — roughly two weeks — and can only move 4× when it finally does.

---

## What's real vs. modeled

Being precise about this matters — the booth is teaching the public.

**Genuinely real**
- **`dgwNova()`** in `daa-engine.js` is a faithful, branch-for-branch JavaScript port of Interchained's C++ retarget: the 12/24-block window, the recursive weighted average of past targets, `actualTimespan` / `targetTimespan`, the height-aware clamps, the **emergency trigger evaluated before clamping**, the rolling-median solve time, the **graceful decay** (`multiplier^0.45`, capped at 2.0), the **median smoothing** of the difficulty window, and the asymmetric decay-from-baseline final target with its `powLimit` cap.
- **Bitcoin's side** is the real 2,016-block epoch formula with the 4× per-epoch clamp.
- The **hashrate ↔ difficulty ↔ block-time** relationship is the genuine physics: at equilibrium, difficulty settles at exactly the hashrate multiplier.
- The **Foundation miner** computes real SHA-256 live (and the full Playground mines real proof-of-work blocks).

**Modeled, by necessity**
- **Block solve times are *drawn* from the correct exponential distribution, not *mined*.** You can't reproduce a network-scale "10× hashrate" swing on one laptop, so the arrival of blocks is simulated while the *decisions made about them* are the real algorithm. Variance is gently damped (mean-preserving) so the teaching chart stays legible.
- **Targets are JavaScript floats**, not the 256-bit `nBits` fixed-point encoding — the *logic* is faithful, the bit-packing is dropped because it has no effect on the behavior being taught.
- Heights and parameters (fork at #200, 60 s spacing) are chosen for a legible story, not pulled from mainnet.

Nothing on screen is a fake number: every difficulty value, block time, and countdown is computed by the real algorithms from the live state.

---

## How it's built

Layered on the existing Interchained design system and the Playground's real engine.

### Engine — `showcase/daa-engine.js`
Plain JS on `window.DAA`. No framework.
- `dgwNova(blocks, params, nextHeight)` — the faithful DGW3-Nova port; returns the next target.
- `makeSim({ hashrate, jitter })` — the dual-chain simulator. `step()` mines one ITC block and advances Bitcoin by the same elapsed seconds; `view()` returns the live readout slice; `setHashrate()` applies a shock.
- `powInfoForHeight(h)` / `isPostSha256Fork(h)` — the height-based Yespower→SHA-256 fork.
- Self-tests its equilibrium on load (block time settles near the 60 s target).

### UI (React + Babel, in-browser)
- `showcase/daa-lab.jsx` — the DAA station, the dual difficulty chart (SVG), the guided steps, and the migration scrubber. On `window.DAALab`.
- `showcase/sc-sections.jsx` — every narrative beat: hero, live-SHA-256 Foundation, the DAA wrapper, migration, by-the-numbers, eco, why-ITC, footer. On `window.SC`.
- `showcase/sc-app.jsx` — composes the journey, scroll-spy nav, the top progress rail, and the Tweaks panel.
- `showcase/sc-styles.css` — the booth layer (BTC|ITC dual-accent language, control deck, chart, steps) over `explainer/styles.css` + `playground/pg-styles.css`.
- Reuses `playground/engine.js` (real SHA-256), `playground/pg-shared.jsx`, and `playground/tweaks-panel.jsx`.

## Tweaks

Open the Tweaks panel to adjust, live:
- **Sim & animation speed** (0.5×–3×).
- **Interchained accent** and **Bitcoin accent** colors.

## Running it

Open `ITC Showcase.html`. It loads React, Babel, and Lucide from CDN; everything else is local. Because it transpiles JSX in the browser, the first paint takes a moment, and on a flaky connection a script can occasionally fail to load (a refresh fixes it). For a fully offline, single-file version, the project can be bundled into one self-contained HTML file.

## Notes

- Everything is 100% client-side. There is **no network, no wallet, and no real asset** — it's a teaching tool. Bitcoin is referenced factually for comparison, not cloned.
- The booth is designed for self-driven scrolling and is mobile-ready; the DAA station pauses its loop when scrolled off-screen to save CPU.
- Not investment advice.
