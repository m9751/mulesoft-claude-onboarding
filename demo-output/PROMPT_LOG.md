# Onboarding Guide Video — Prompt Log & Master Prompt

**Last updated:** 2026-06-02 (pre-deployment review)  
**Repo:** `mulesoft-claude-onboarding`  
**Live site:** `index-v2.html` via `vercel.json` → https://mulesoft-claude-cursor-onboarding.vercel.app/

---

## Current production state

| Asset | File | On live guide? |
|-------|------|----------------|
| **Primary** | `demo-output/output.mp4` (~60s, narrated, Edition 2) | **Yes** — Claude + Cursor Welcome |
| **Optional teaser** | `demo-output/output-hero-silent.mp4` (~16s, silent, script-first) | **No** — preview only until deploy decision |
| **Spec** | `demo-output/scenes.json` (edition 2 + hero_silent block) | — |
| **Pipeline** | `demo-output/build_demo.py` | — |
| **Preview** | `demo-output/preview-hero.html` | Optional local/Vercel |

**Embed (shipped):** One `<video controls>` per Welcome tab → `/demo-output/output.mp4`. No muted autoplay.

**Tracking (shipped in HTML, not validated in Supabase):** `video_started`, `video_progress`, `video_completed`, `video_unmuted` per `data-video-context` (`claude-welcome` / `cursor-welcome`).

---

## Lessons learned (read before any rebuild)

### Creative / story

1. **Edition 2 is canonical.** Nine beats, sell-first, ~180–220 words VO. Do not replace with an 8-beat “Notion” cut.
2. **No arbitrary VO word caps.** A 150-word cap gutted Salesforce, depth, and proof — unusable.
3. **Developer hook = Script→Flow pane.** Lead with legacy script pain (`Before — Legacy Script`, 847 lines, FIXME). Captures: `capture-script-before-after.png`, `capture-script-legacy-panel.png`, then `capture-scriptflow-arch.png`.
4. **Outcomes before mechanics.** Exchange/IDE wins, then MCP proof — not an MCP lecture.
5. **In-guide CTA only.** Viewer is already on the guide; never “visit this URL.”

### Technical / pipeline

6. **Never `ffmpeg -c copy` on final concat.** Causes non-monotonic DTS → hiccups/dropouts. Always re-encode: `libx264`, `aac`, `fps=25`, `aresample=async=1:first_pts=0`, `-movflags +faststart`.
7. **Screenshot paths from `demo-output/scenes/`:** repo screenshots = `../../screenshots/…`; captures = `../captures/…`.
8. **Scene `duration` ≥ TTS length + 0.35s.** Proof beats need real holds (6–10s target).
9. **Ken Burns on proof + hero only.** Static layouts for tab/outcomes; avoid over-animation.
10. **Hero-only rebuild:** `cd demo-output && python3 build_demo.py hero` (captures + silent mux, does not rebuild full narrated cut).

### UX / embed mistakes

11. **Do not autoplay silent video on Welcome.** Users thought the product was broken (no story, no sound).
12. **“Hero” ≠ tiny file** — it’s a **layout slot** (banner above content). Opening the raw MP4 full-screen looks huge; on-page it should be a constrained strip (~300–400px tall) if we enable it.
13. **Corporate networks may block `*.vercel.app`.** For review, copy to Desktop or open files locally (`Desktop/mulesoft-video-preview/`).

### Process

14. **Log prompt changes in this file** before deploy conversations.
15. **Keep bad exports** renamed (`output-v3-broken.mp4`, `output-glitchy-concat.mp4`) so they are not shipped again.

---

## What worked (keep)

| Practice | Detail |
|----------|--------|
| Edition 2 arc | Hook → Salesforce → outcomes → MCP proof → Exchange proof → editor pick → Script→Flow → SF arch → in-guide CTA |
| `scenes.json` | Single source of truth for layouts, VO, durations, hero beats |
| MuleSoft brand | `#032D60`, `#EEF4FF`, Salesforce Sans, logo top-right |
| Proof screenshots | Green MCP + Exchange agent with badges; always on screen |
| Playwright captures | Live Vercel + element shot for legacy script panel |
| Re-encoded mux | Stable playback (~60s narrated, ~16s hero) |
| Dual Welcome embed | Same `output.mp4` on Claude + Cursor Welcome |

## What failed (do not repeat)

| Mistake | Result |
|---------|--------|
| Edition 3 / 150-word cap | No audio story, collapsed arc |
| `ffmpeg -c copy` concat | Video hiccups at joins |
| Silent hero as default on Welcome | “Broken” silent experience |
| Hero v1 order (outcomes → Exchange → arch) | Missed developer script hook |
| Wrong `../screenshots/` in scene HTML | Blank proof frames |

---

## Two outputs explained

### 1. `output.mp4` (primary — deploy this on Welcome)

- ~60 seconds, **full narration** (`en-US-AndrewNeural`)
- Edition 2 story (9 scenes)
- **This is what Claude + Cursor Welcome use today**

### 2. `output-hero-silent.mp4` (optional teaser — deploy only if intentional)

- ~16 seconds, **no audio**
- **Script-first** order (2026-06-02):
  1. **6s** — Before/After code (`capture-script-before-after.png`)
  2. **5s** — Legacy script pane zoom (`capture-script-legacy-panel.png`)
  3. **5s** — Script→Flow architecture diagram
- **Purpose:** Muted loop in a **small banner** above the narrated player to stop scrollers — “that’s my script → there’s an escape → here’s the flow”
- **Not** a replacement for `output.mp4`
- **Not** on Welcome until we explicitly design layout (muted loop + “Watch with sound” below)

---

## Hard constraints (all future builds)

1. Viewer on guide — in-guide CTA only  
2. Edition 2 arc unless Michael approves a new edition  
3. Script pane visible for dev audience (captures or Script→Flow tab)  
4. Proof = screenshots + badges, not empty frames  
5. Final mux = re-encode (see `build_demo.py`)  
6. Primary embed = narrated `output.mp4` with controls  
7. Hero silent = optional, muted, constrained layout, never autoplay-only UX  

---

## Benchmark (style only)

[Notion “What is Notion?”](https://www.youtube.com/watch?v=Vicx5Kz6hs4) — borrow mute test, kinetic type, UI choreography. **Do not** borrow 150-word limit or beat collapse.

---

## MASTER PROMPT (copy for next session)

```
You are a Video Director + Motion Designer for the MuleSoft AI Editor Onboarding Guide.

DELIVERABLES:
A) output.mp4 — ~60s narrated sell-first (PRIMARY, goes on Claude + Cursor Welcome)
B) output-hero-silent.mp4 — ~15–16s silent script-first teaser (OPTIONAL banner only)

VIEWER is already on the guide. CTA = pick tab → setup → prove Exchange → build. NO external URL.

AUDIENCE: Integration developer first. Hook = legacy script pain (847 lines, FIXME, schema drift).
Then escape (Script→Flow, DataWeave), then proof (MCP + Exchange), then editor choice.

BRAND: MuleSoft #032D60 / #EEF4FF, Salesforce Sans, logo top-right.

NARRATED STORYBOARD (9 beats — do not merge):
1. kinetic_pain — hook
2. split_imagine — Salesforce + EHRs/claims/legacy
3. kinetic_outcomes — Exchange, scaffold, deploy from IDE
4. proof_highlight — MCP green dot screenshot + badges
5. proof_highlight — Exchange agent screenshot + badges
6. editor_tabs — Claude vs Cursor
7. image — Script→Flow architecture capture
8. image — Salesforce strategy tab
9. cta_inguide — in-guide CTA

HERO SILENT (3 beats — script-first, no VO):
1. capture-script-before-after.png (6s)
2. capture-script-legacy-panel.png (5s)
3. capture-scriptflow-arch.png (5s)

RULES: Mute test; show don’t tell; conversational VO; NO word cap; proof screenshots mandatory.

TECH:
- scenes.json drives build_demo.py
- Playwright: capture script pane element + Script→Flow tab from live URL
- edge-tts per scene; duration >= audio + 0.35s
- Per-scene clips → concat → RE-ENCODE (never -c copy): libx264, aac, fps=25, aresample=async=1, faststart
- Full build: python3 build_demo.py
- Hero only: python3 build_demo.py hero

OUTPUT: Two-column storyboard + updated scenes.json
```

---

## Edition history

| Ed | Status | Notes |
|----|--------|-------|
| v0–v1 | archive | Early cuts |
| **v2** | **canonical narrated** | 9 scenes, ~60s |
| v2-reencode | **current output.mp4** | Glitch-free mux |
| v3 | **rejected** | 150-word Notion cut |
| hero v1 | retired | outcomes → Exchange → arch (wrong hook) |
| **hero v2 script-first** | **current output-hero-silent.mp4** | before/after → legacy pane → arch |

---

## Commands

```bash
cd demo-output
python3 build_demo.py        # full: captures + frames + output.mp4 + hero
python3 build_demo.py hero   # hero silent only (after capture script)
```

---

## Files to commit for deploy (not build artifacts)

- `demo-output/output.mp4`
- `demo-output/output-hero-silent.mp4` (if serving teaser)
- `demo-output/scenes.json`, `build_demo.py`, `PROMPT_LOG.md`
- `demo-output/captures/*.png` (source frames for rebuilds)
- `screenshots/*.png` (proof)
- `index-v2.html`

**Gitignore in demo-output:** `clips/`, `frames/`, `audio/`, `narration/`, `concat*.txt`, broken exports

---

## Deployment checklist (next conversation)

### Done
- [x] Edition 2 narrated `output.mp4` re-encoded (no hiccups)
- [x] Embed on Claude + Cursor Welcome (`output.mp4`, controls)
- [x] Pushed to GitHub / Vercel (`demo-output/output.mp4` returns 200)
- [x] Script-first hero rebuilt + `preview-hero.html`
- [x] Prompt log + master prompt updated

### Decide before / during deploy
- [ ] Put **hero silent** on Welcome? If yes: small muted loop + narrated player below (never silent-only)
- [ ] Poster image / lazy-load for mobile
- [ ] Validate `video_*` beacon events in Supabase / smokin-territory API
- [ ] Confirm `*.vercel.app` reachable for customer networks (or alternate host)
- [ ] File size budget (~8MB narrated + ~4MB hero) — acceptable for Vercel static?

### After deploy
- [ ] Smoke-test Claude + Cursor Welcome on desktop + mobile
- [ ] Customer-safe copy sign-off
- [ ] Optional: link from Welcome callout “See Script → Flow” to tab + scroll

---

## User feedback log

| Date | Feedback | Action taken |
|------|----------|--------------|
| 2026-06-02 | Notion v3 unusable — no story | Reverted to Edition 2 |
| 2026-06-02 | Video hiccups | Re-encode mux; ban `-c copy` |
| 2026-06-02 | Silent default confusing | Narrated-only embed on Welcome |
| 2026-06-02 | Hero must show script pane | Script-first hero v2 + captures |
| 2026-06-02 | Can’t open Vercel links | Desktop copy `mulesoft-video-preview/` |
| 2026-06-02 | Ready for deployment discussion | This doc updated |
