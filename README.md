# TerraQuest frontend - Rise of Microbes

Static front end for **TerraQuest / Rise of Microbes (RoM)** - the 46-file bundle served at the reference site https://sites.hellominds.ai/b1/biotic-model/ and deployed to the MVP at https://microbes.biotexturas.org/. No build step: plain HTML/CSS/JS. Companion backend repo: https://github.com/jekeymer/terraquest-mint

The TerraQuest landing page lives separately at https://terraquest.biotexturas.org/ (not in this repo).

## How this repo stays in sync

- **Authority = the reference site.** Bundle files are fetched from `https://sites.hellominds.ai/b1/biotic-model` by `.github/workflows/fetch-live-bundle.yml` (runs on a push to `main` touching that file, or manually via workflow_dispatch).
- The workflow pulls the full 46-file manifest with `curl -fsSL` - any single failure aborts the job, no partial syncs - and asserts the count is exactly 46 before committing as `b1-sync`.
- `README.md` and the workflow file are repo-owned, **outside** the manifest: a sync never touches them.
- **Do not hand-edit bundle files here** - the next sync overwrites them from the reference. Edits land on the reference site first, then the workflow pulls them in.
- Oversized files (vault 172 KB, battle 155 KB, Seek 145 KB, dj 144 KB, sim 72 KB, PNGs up to 733 KB) exceed GitHub's ~60 KB direct-push cap, so the workflow is their transport by design.

## The map: RoM chapters -> pages

The entry page (`index.html`, heading "Choose your research quest") carries the four chapters:

| Chapter | Line on the page | State | Served by |
|---|---|---|---|
| 1. Remnant AI | The Calculi of intelligence. | Enter -> `sim.html` | `sim.html` - ALife Ecology |
| 2. The hunt for intelligence | Find your smart microbe. | LOCKED | observation lane: `collections.html` -> `Seek.html` |
| 3. The expansion of intelligence | Conquer the arena. | LOCKED | arena lane: `battle.html` + `dj.html` |
| 4. Drone activate | Seek and track. | Enter -> `Seek.html` | `Seek.html` - Seek & Track; record: `kEngram9.html` (Ancient Datagram) |

Enter targets for chapters 1 and 4 are quoted straight from the page. Chapters 2 and 3 render LOCKED with no href yet - their pages ship in this bundle today, unlock wiring arrives when the chapters open.

## Pages

- `index.html` - **entry / quest page**: council triad (Art / Science / Technology / Datagram), four-chapter quest picker, wallet connect stub (display-only), footer. Judges-facing page at microbes.biotexturas.org.
- `entry.html` - legacy entry page (council v2), kept in the bundle.
- `collections.html` - **TerraScope Observatory**: 14 collections / 593 frames as thumbnails; pick mode stores a 3-frame window for Seek; in-page AVI (MJPEG) reader.
- `Seek.html` - **Seek & Track** (chapter 4): two pipeline tabs (Frame diff - blob / Optical flow - clustering) with live knobs, camera + collections loading, 8 hash rows, KEEP / MINT / run log (`tq_seek_runs_v1`), annotations CV ToolBar, nav doors to Collections, My workbench and the Swarm Arena (CV arena, separate site). `cv_demo.html` redirects here.
- `sim.html` - **ALife Ecology** (chapter 1's Enter target): 48x48 lattice terrain sim; selection tools (SELECT / RADIATE / RE-PICK / TRACKER) unlock once a kEngram is collected; writes standings (`tq_standings_v1`).
- `dj.html` - **Terrain Console**: registered-constant terrain knobs (regrowth / diffusion / consumption + Boids radii).
- `battle.html` - **Battle Arena**: three attested matches + LIVE TERRAIN RUN, Genome bank contestant load, STANDINGS. Upload with `battle_data.json` as a pair.
- `deck.html` - **kEngram deck**: the nine Knowledge Engrams as cards; Collect writes `biotic_kengrams_v1`; References (P.1 / P.2).
- `kEngram1.html` ... `kEngram9.html` - expanded card pages; deck cards link here, each page carries its eight neighbors.
- `vault.html` - **My workbench**: browser-local inspector - GENOME BANK (battle genomes, `biotic_vault_v1`), CV VAULT (`tq_cv_vault`), AVATARS placeholder.
- `glyph.html` - genome glyph viewer (TCS filaments / TF branching / TR swirl / DR barbs; TF=146 baseline).
- `construct.html` - **Construct your own TerraScope**, generated from the manual markdown; the `.md` and `.pdf` ship beside it (one source, both outputs).
- `config.json` - alifesim-config-v1 manifest (programId placeholder; no secrets). `battle_data.json` - battle's data sibling.
- `*.png` / `perceptron_source.svg` - card faces and figure assets.

## The kEngram <-> ToolBar loop

1. **Collect**: Collect on the deck (or a kEngram page) bumps `biotic_kengrams_v1` in browser localStorage.
2. **Unlock**: that count gates two surfaces - the sim's selection tools, and Seek's annotations CV ToolBar (hidden with a hint line until at least one kEngram is collected).
3. **Run and keep**: a Seek run can be KEEP'd and MINT'd - the run log feeds the CV VAULT in My workbench. MINT is the bundle's only on-chain action (Solana devnet, signed when the player presses it, never automatic).
4. **Persist**: everything is browser-local (`biotic_kengrams_v1`, `biotic_vault_v1`, `tq_cv_vault`, `tq_seek_runs_v1`, `tq_standings_v1`, `tq_resetlog_v1`) - it travels with the browser, not the server.

One line: collect a kEngram -> the ToolBars unlock -> run the CV pipeline in Seek -> KEEP / MINT -> the run lands in My workbench.

## Deploy

Static-only: upload the whole 46-file set to the microbes folder root (replace the set as a whole). HTTPS required (crypto.subtle for wallet connect). Hard refresh after upload if a page looks stale.

Display-only guardrail: nothing in this bundle signs or mints on its own; mints fire only on a player press with a connected wallet on devnet.
