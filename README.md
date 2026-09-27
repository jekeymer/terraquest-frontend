# TerraQuest frontend

Static front end for TerraQuest / Rise of Microbes — the exact 8-file set deployed at https://microbes.biotexturas.org/ (upload verified live 2026-09-27). Companion backend repo: jekeymer/terraquest-mint.

The TerraQuest landing page lives separately at https://terraquest.biotexturas.org/ (not in this repo).

## Files (repo root = microbes folder root)

- index.html — council front door: triad (Art / Science / Technology / Datagram), chapter picker ("Chose your research quest", chapter 1 active), wallet connect via injected provider (connect only, nothing signed).
- sim.html — Alife Sim: 48x48 lattice terrain, selection tools (SELECT / RADIATE / RE-PICK / TRACKER, unlocked by collecting kEngrams), vault views (SAVED / OWNED / REGISTRY), zoom 1x-12x + FOLLOW, ENTRY / DJ / BATTLE mode links.
- dj.html — DJ console: terrain knobs as a Neutral sandbox layer (regrowth / diffusion / consumption + Boids radii).
- glyph.html — glyph viewer: deterministic seeded-walk rendering (TCS filaments / TF branching / TR swirl / DR barbs), TF=146 baseline, origin-suppression fix included.
- battle.html — battle console: three attested matches + LIVE TERRAIN RUN (banner: "LIVE RUN - not the shipped record").
- battle_data.json — battle.html data sibling; always upload as a pair with battle.html.
- config.json — alifesim-config-v1 (programId placeholder until devnet wiring; no secrets).
- deck.html — kEngram deck: nine kEngrams; collecting any card unlocks the sim's selection tools (browser localStorage).

## Deploy

Static-only, no build step. Upload all files to the microbes folder root (replace the whole set; delete any stray entry.html). HTTPS required (crypto.subtle for wallet connect). Hard refresh after upload if a page looks stale — the CDN edge caches a few minutes.

Display-only guardrail: nothing in this bundle signs or mints on-chain; the mint flow ships when the program ID wiring is given the go.
