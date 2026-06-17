# Packaging pointer

Generic blueprint: `~/repos/prompt-registry/docs/static-guide-video-packaging.md`

| Layer | This repo (pilot) | Generic (package) |
|-------|-------------------|-------------------|
| Run log | `PROMPT_LOG.md` | — |
| Story contract | `scenes.json` | `scenes.schema.json` (target) |
| Build engine | `build_demo.py` | `build_video.py` (target) |
| Agent prompt | — | `PRM-PDLV-006` in prompt-registry |
| Verify | `smoke-test.sh` | templated smoke-test |

**Status:** pilot shipped · package extraction not started.
