# Onboarding Guide Video — Prompt Log & Master Prompt

**Canonical build:** Edition 2 (sell-first, 9 scenes, ~60s narrated)  
**Last good output:** `demo-output/output.mp4` (re-encoded 2026-06-02, no concat-copy glitches)  
**Page embed:** `index-v2.html` → Cursor Welcome → `/demo-output/output.mp4` (controls, narration on)

---

## What worked (keep)

| Practice | Why |
|----------|-----|
| **Edition 2 story arc** (9 beats) | Hook pain → Salesforce imagination → outcomes → MCP proof → Exchange proof → editor choice → Script→Flow → SF architecture → in-guide CTA. Full story, ~60s. |
| **`scenes.json` as source of truth** | Drives HTML scenes, TTS, durations, layouts. Rebuild = one command. |
| **MuleSoft brand frames** | Blue 20/95, Salesforce Sans, logo top-right, proof badges. |
| **Real screenshots on proof beats** | `../../screenshots/cursor-tools-mcps-verified.png`, `cursor-agent-exchange-result.png` — never title-only frames. |
| **Live Playwright captures** | Tab bar, welcome, Script→Flow, Salesforce tab from production URL. |
| **`edge-tts` Andrew + scene `duration` ≥ audio length** | VO never cut off mid-sentence. |
| **Final mux: re-encode, never `-c copy`** | Concat copy → non-monotonic DTS → hiccups/dropouts. Use libx264 + aac + `fps=25` + `aresample=async=1` + `faststart`. |
| **In-page: narrated video only** | One `<video controls>` for `output.mp4`. No silent autoplay as default. |
| **Silent hero as optional asset** | `output-hero-silent.mp4` for future embed/loop only — not the primary UX. |

## What failed (do not repeat)

| Mistake | Result |
|---------|--------|
| Edition 3 “Notion” **150-word VO cap** | Gutted Salesforce + depth; felt unusable. |
| **`-c copy` concat** | Playback hiccups at every scene join. |
| **Silent hero autoplay on Welcome** | User heard no story; thought video was broken. |
| **Wrong image path** `../screenshots/` from scenes/ | Blank proof frames (fix: `../../screenshots/`). |

## Retired / backup files

- `scenes.v3-broken.json` — do not use
- `output-v3-broken.mp4`, `output-glitchy-concat.mp4` — bad exports

---

## Hard constraints (all future builds)

1. Viewer is **on the guide** — no external URL CTA.
2. **Sell-first** arc (table above); do not compress below ~9 beats without explicit approval.
3. Proof scenes: screenshots + badges + Ken Burns; min duration ≥ VO length (target 6–10s holds).
4. **VO:** conversational; no arbitrary word cap — fit ~60s naturally (~180–220 words OK for v2).
5. **Mux:** re-encode final MP4 (see `build_demo.py` `build_full()`).
6. **Embed:** narrated `output.mp4` primary; hero silent optional secondary.
7. Beacon `video_*` → Supabase when implementing (not in pipeline yet).

## Benchmark (style only — not structure)

[Notion “What is Notion?”](https://www.youtube.com/watch?v=Vicx5Kz6hs4) — mute test, kinetic type, UI choreography. **Borrow:** pacing feel, show-don’t-tell. **Do not borrow:** 150-word limit or collapsing beats to 8.

---

## MASTER PROMPT (copy for next session)

```
You are a Video Director + Motion Designer for a MuleSoft onboarding guide.

GOAL: ~60s sell-first narrated MP4 + optional 15s silent hero. Viewer is ALREADY on the guide.

AUDIENCE: Integration dev, Salesforce admin/architect, IT/platform. Outcomes first; editor choice second; not an MCP lecture.

BRAND: MuleSoft — #032D60, #EEF4FF, Salesforce Sans, logo top-right every frame.

STORYBOARD (9 beats — do not merge):
1. Kinetic hook — 847-line script / five portals → one editor
2. Salesforce imagination — org + EHRs/claims/legacy APIs from one editor
3. Outcomes — Exchange, scaffold, deploy from IDE
4. Proof MCP — screenshot + Connected + 18 tools badges
5. Proof Exchange — screenshot + live org + approve once
6. Pick editor — Claude vs Cursor tab bar
7. Script→Flow — architecture capture
8. Salesforce tab — Headless 360 / governance
9. In-guide CTA — pick tab, setup, prove Exchange, build (no external URL)

RULES:
- Mute test: visuals carry story without VO
- Show don’t tell: UI + screenshots, not bullet walls
- Conversational VO; length driven by ~60s audio, NOT a word cap
- Proof screenshots mandatory (paths from demo-output/scenes/: ../../screenshots/...)
- Motion: minimal — CSS stagger, light Ken Burns on proof only
- CTA: IN_GUIDE only

TECH (non-negotiable):
- scenes.json → Playwright frames → edge-tts → per-scene clips → ffmpeg concat WITH RE-ENCODE (libx264, aac, fps=25, faststart). NEVER ffmpeg -c copy on final mux.

OUTPUT:
1. Two-column storyboard (Visuals | VO)
2. scenes.json matching Edition 2 layout names: kinetic_pain, split_imagine, kinetic_outcomes, proof_highlight, editor_tabs, image, cta_inguide
3. Rebuild command: cd demo-output && python3 build_demo.py

INPUTS TO PROVIDE:
- WEBPAGE_URL (default: mulesoft-claude-cursor-onboarding.vercel.app)
- Screenshot paths for MCP + Exchange proof
```

---

## Edition history

| Ed | Status | Notes |
|----|--------|-------|
| v0 | archive | 36s generic |
| v1 | archive | tab map |
| **v2** | **canonical** | 9 scenes, sell-first, ~60s |
| v2.1 | merged | screenshot path fix |
| v3 | **rejected** | 150-word cut, 8 beats |
| v2-reencode | **current** | same story, glitch-free mux |

## Rebuild

```bash
cd demo-output && python3 build_demo.py
```

Ship artifacts: `output.mp4`, `output-hero-silent.mp4` (optional), `scenes.json`, `captures/`, `screenshots/` (repo root).

---


## “Hero silent” explained (optional second file)

We produce **two** MP4s from the pipeline:

| File | Length | Audio | Purpose |
|------|--------|-------|---------|
| **`output.mp4`** | ~60s | **Yes** — full narration | **Primary.** The real story on Claude + Cursor Welcome. User presses play, turns sound on. |
| **`output-hero-silent.mp4`** | 15s | **No** | **Optional marketing loop.** Three quick visuals (outcomes → Exchange proof → Script→Flow) with **no voiceover**, meant to autoplay **muted** on a landing page like a GIF/banner — similar to a muted preview on LinkedIn or a homepage hero. |

**We are NOT using hero silent on the guide today.** It confused people (looked “broken” with no story). It stays in the repo only if you later want e.g. a muted looping banner above the fold *in addition to* the narrated player — not instead of it.

**Embed decision (current):** Claude Welcome + Cursor Welcome → **`output.mp4` only**, with controls.


## Implementation checklist (next)

See conversation / Michael for deploy sign-off.

- [ ] Commit `demo-output/output.mp4` + `output-hero-silent.mp4` + pipeline sources (not clips/frames/audio)
- [ ] Commit `index-v2.html` embed (Cursor Welcome; mirror Claude Welcome if desired)
- [ ] `git push` → Vercel production
- [ ] Verify https://…/demo-output/output.mp4 returns 200
- [ ] Smoke-test playback on Cursor Welcome (no hiccups, sound on)
- [ ] Wire `video_started` / `video_progress` / `video_completed` → smokin-territory beacon (already stubbed in index-v2)
- [ ] Optional: Claude Welcome same block; poster image; lazy-load for mobile
