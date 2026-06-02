# Onboarding Guide Video — Prompt Log & Master Prompt

**Last updated:** 2026-06-02 (post-deploy cleanup)  
**Repo:** https://github.com/m9751/mulesoft-claude-onboarding  
**Live:** https://mulesoft-claude-cursor-onboarding.vercel.app/  
**Registry (generic agent prompt):** PRM-PDLV-006 — https://github.com/m9751/prompt-registry/blob/feat/prm-pdlv-006-overview-video/prompts/product-delivery/PRM-PDLV-006_static-guide-overview-video.md (merges via PR #9)
**Next:** submission / handoff (after redeploy confirms single video on Welcome)

---

## FINAL LIVE STATE (what shipped)

| What | Where |
|------|--------|
| **One video on Welcome** | Claude + Cursor tabs → `/demo-output/output.mp4` (~60s, narration) |
| **Label** | “Overview (~1 min)” — press play, turn sound on |
| **Silent loop** | **REMOVED from page** — file may remain in `demo-output/output-hero-silent.mp4` for archive only |
| **Beacons** | `video_started`, `video_completed`, `video_unmuted` → `smokin-territory.vercel.app/api/beacon` |
| **Hosting** | Push to GitHub `main` → Vercel auto-redeploys |

---

## Tips & tricks (operational)

### Vercel / GitHub (critical)

1. **Two HTML files exist:** `index-v2.html` (source of truth) and `index.html`. Vercel often serves **`index.html` by default**, ignoring your mental model of “v2.”
2. **After every Welcome/embed change:** copy v2 → index, then push:
   ```bash
   cp index-v2.html index.html
   git add index-v2.html index.html
   git commit -m "..."
   git push origin main
   ```
3. **Wait 2–5 min**, then **Shift+Reload** the live URL (cache lies).
4. **Smoke test:**
   ```bash
   ./demo-output/smoke-test.sh
   ```
   (Update script if it still checks for hero markup.)

### Video pipeline

5. **Full rebuild:** `cd demo-output && python3 build_demo.py`
6. **Hero file only (archive):** `python3 build_demo.py hero` — does not affect live page unless embedded.
7. **Never `ffmpeg -c copy`** on final concat → hiccups. Re-encode in `build_demo.py` (already fixed).
8. **Proof screenshots path** from scene HTML: `../../screenshots/…` not `../screenshots/`.
9. **Edition 2 narrated** = 9 scenes in `scenes.json`. Do not ship Edition 3 (150-word cap).

### Beacons (keep simple)

10. **Only three events** on Welcome video — no progress %, no hero events (hero removed).
11. **Beacon accepts custom `event_type`** (tested 200 OK) — still don’t add a dozen variants.
12. **`proposal_id`:** `mulesoft-anypoint-onboarding`

### Working with the stakeholder (Michael)

13. **No jargon:** don’t say main, feat, commit, hero, mux. Say “folder on GitHub,” “save,” “top/bottom video.”
14. **Don’t offer A/B/C lists** when they’re frustrated — pick the obvious fix, ship, confirm once.
15. **Silent loop looked good but added no value** on Welcome — lesson: **polish ≠ value**. Ask “what does this do for someone on the page?” before embedding.
16. **“Expert HTML Designer”** direction = doc-like guide, not marketing demo polish. Don’t over-style embed blocks.
17. **Corporate VPN** may block vercel.app — local copy to Desktop if they can’t open links.

### Files to know

| Path | Purpose |
|------|---------|
| `demo-output/output.mp4` | **Ship this** on Welcome |
| `demo-output/output-hero-silent.mp4` | Archive / optional; **not on page** |
| `demo-output/scenes.json` | Story + hero beat spec |
| `demo-output/build_demo.py` | Build script |
| `demo-output/PROMPT_LOG.md` | This file |
| `index-v2.html` | Edit here first |
| `index.html` | **Must match v2 before push** |
| `vercel.json` | Rewrites (backup routing) |

---

## Lessons learned (full session)

### Creative

- Sell-first **9-beat** narrated arc is the product. (~60s, full VO.)
- Script→Flow content matters for **Script→Flow tab** and for **hero file** — not required as a second video on Welcome.
- In-guide CTA only (no “go to this URL”).
- Notion benchmark = **pacing/style reference only**, not a shorter word count.

### Failed experiments

| What | Why it failed |
|------|----------------|
| Edition 3 (8 beats, ~150 words) | Unusable — gutted story |
| `ffmpeg -c copy` | Playback hiccups |
| Silent loop as default / on Welcome | No value on page; confused users; felt like AI polish for polish’s sake |
| Two-video Welcome (loop + full) | Removed — one video only |
| Serving only `index-v2` without syncing `index.html` | Live site showed **no videos** until synced |

### What worked

- Edition 2 re-encoded `output.mp4`
- Real MCP + Exchange screenshots with badges
- Playwright captures from live guide URL
- Single overview video on Claude + Cursor Welcome
- Minimal beacons (3 events)
- `index.html` synced from `index-v2.html` for Vercel

---

## MASTER PROMPT (next session)

```
Build/maintain MuleSoft AI Editor Onboarding guide video.

PRIMARY DELIVERABLE (only embed unless told otherwise):
- demo-output/output.mp4 — ~60s narrated, Edition 2, nine beats, sell-first
- Claude Welcome + Cursor Welcome: ONE <video controls> — no silent autoplay loop

OPTIONAL ARCHIVE (do NOT embed on Welcome without explicit approval):
- demo-output/output-hero-silent.mp4 — script-first silent ~16s (repo only)

AUDIENCE: integration developer; Script→Flow tab carries script pain; Welcome video = full story only.

BRAND: MuleSoft #032D60 / #EEF4FF, Salesforce Sans.

NARRATED BEATS (do not merge): hook → Salesforce → outcomes → MCP proof → Exchange proof → editor pick → Script→Flow image → SF arch → in-guide CTA.

TECH:
- scenes.json + build_demo.py
- Final mux: RE-ENCODE (never -c copy)
- After HTML edits: cp index-v2.html index.html && git push main
- Beacons: video_started, video_completed, video_unmuted only

DO NOT: word caps, hero loop on Welcome, marketing-polish embed, progress beacon spam, jargon with stakeholder.
```

---

## Edition history

| Ed | Narrated | Hero silent | On Welcome |
|----|----------|-------------|------------|
| v2 | canonical | — | narrated only (final) |
| v3 | rejected | — | — |
| hero v2 script-first | — | built, archived | **removed** |

---

## User feedback log

| Date | Feedback | Action |
|------|----------|--------|
| 2026-06-02 | v3 unusable | Restored v2 |
| 2026-06-02 | Video hiccups | Re-encode mux |
| 2026-06-02 | Silent loop on Welcome confusing | Removed loop |
| 2026-06-02 | Hero looks great but no value | Removed from page |
| 2026-06-02 | Don’t like over-polished AI look | Simpler embed copy; no second video |
| 2026-06-02 | Live site showed nothing | Synced index.html from index-v2 |
| 2026-06-02 | Stop rabbit holes — clean up | Single video; pushed 572c310 |
| 2026-06-02 | Update prompt doc; then submission | This update |

---

## Submission checklist (NEXT — after redeploy)

Use this when Michael is ready to “get it submitted.”

- [ ] **Verify live:** Welcome shows **one** video only (no silent loop). Shift+Reload both:
  - `?editor=claude` Welcome
  - `?editor=cursor` Welcome
- [ ] **Playback:** Play overview — sound on, no stutter mid-video
- [ ] **Customer-safe:** copy sign-off on narrated script
- [ ] **What “submitted” means** (fill in with Michael):
  - Internal link shared? (team/customer URL)
  - Linked from another page / deck / email?
  - Salesforce/MuleSoft publishing process?
  - Git tag / release note?
- [ ] **Record where it lives** in deliverables DB / email / ticket
- [ ] **Optional:** remove or gitignore unused `output-hero-silent.mp4` if repo size matters

---

## Commands cheat sheet

```bash
# Edit page
# 1) edit index-v2.html
# 2) sync + push
cp index-v2.html index.html && git add index-v2.html index.html && git commit -m "..." && git push origin main

# Rebuild narrated video only
cd demo-output && python3 build_demo.py

# Rebuild archived silent file (not on Welcome)
cd demo-output && python3 build_demo.py hero
```
