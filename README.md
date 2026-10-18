# cinematic-music-video-director

An **open-source, autonomous production system for code-drawn, music-led short films** — built as a
single Agent Skill that works in both **Claude Code** (`.claude/skills/`) and **Codex** (`.agents/skills/`),
plus a reusable, dependency-light **render engine** that draws every frame as a pure function of song time.

Everything here is **original code released under MIT**. This repository contains **no copyrighted song
audio and no song lyrics** — those are intentionally excluded (see *Copyright & scope* below).

---

## What's in this repo

```
cinematic-music-video-director/
├── SKILL.md                      # the autonomous director Skill (single source of truth)
├── references/                   # directing, pipeline, audio/lyrics, visual-language, QC, recovery
├── scripts/                      # analyze_music, align_lyrics, validate_timeline, qc_video, render
├── templates/                    # director_score, storyboard, timeline, shot schema, style guide
├── tests/                        # 3 validators + skill-layout test (all pass)
├── engine/                       # a runnable demo: "The Last Orchard"
│   ├── studio.html               #   Canvas2D stage; exposes window.renderAt(t)
│   ├── render.mjs                #   headless-Chrome frame renderer + ffmpeg encoder
│   └── src/                      #   shots, cast, sets, props, custom shots (all original)
├── LESSONS.md                    # post-mortem notes from real productions
├── install.sh                    # symlink the Skill into ~/.claude and ~/.agents
└── THIRD_PARTY_*.md              # attributions and notices
```

**Two deliverables, one idea:**

1. **The Skill** (`SKILL.md` + `references/`, `scripts/`, `templates/`, `tests/`) — a reusable
   methodology any agent can follow to turn a *rights-cleared* song + lyric text into a finished,
   code-drawn MV. It is generic; nothing is hardcoded to one track.
2. **The Engine demo** (`engine/`) — a concrete, working example ("The Last Orchard") you can
   render today to learn the pipeline. Its shot data carries **no lyrics**; it renders an original
   visual narrative only.

---

## How it works (in one paragraph)

The engine treats the whole film as `frame(t) = f(t)`: a `shotAt(t)` selector picks the active shot
from a data-driven `SHOT_SPECS` list, and `drawSpec()` paints it with Canvas2D (textures, light,
silhouettes, particles — no external image or video models). A headless Chrome instance walks
`t = 0 … duration` at the target fps, dumping one JPEG per frame; `ffmpeg` packs them into an
H.264/AAC MP4. Renders are **resumable** (existing frames >12 KB are skipped), and `qc_video.py`
runs black-frame / freeze / A-V drift checks and reports evidence instead of guessing.

---

## Quick start — render the demo

```bash
cd engine
npm install            # pulls puppeteer-core (you also need Google Chrome on PATH)
node render.mjs --all  # renders frames into renders/frames (skips any already present)
node render.mjs --encode --out output/last-orchard.mp4
```

Open `studio.html` in a browser and call `window.renderAt(t)` from the devtools console to preview
a single frame at song-time `t` (seconds).

## Quick start — use the Skill

```bash
bash install.sh        # symlinks into ~/.claude/skills and ~/.agents/skills
```

Then, in Claude Code or Codex:

> Use cinematic-music-video-director to make a 9:16 animated music video for
> `/path/song.mp3`; lyrics are in `/path/song.lrc`; no paid APIs.

The Skill asks only for genuine blockers (missing audio, missing rights, a paid service) and
otherwise proceeds through its gates: **M**easure → **D**evelop (2 concepts) → **E**dit →
**P**ilot → **R**ender → **A**udio/visual QC, reporting evidence at each step.

## Tests

```bash
bash tests/run_tests.sh      # py_compile + node --check + 3 unittest cases
```

---

## Copyright & scope (read this)

- **Code, skill text, references, templates, the demo engine and its original visuals:**
  © the authors, **MIT License** — free to use, modify, and redistribute.
- **NOT included, and never were:** any commercial song's audio file, and any song's lyrics.
  This repo's `engine/` demo ships with **empty lyric fields** on purpose; it visualizes an
  original story, not someone else's words.
- If you build an MV of a real, copyrighted song, **you** are responsible for licensing the audio
  and lyrics. The Skill is designed to require explicit rights clearance before it will render
  third-party lyrics.
- `musical-mv-storyboard` (a referenced prior art) has **no licence**; it was used for ideas only.
  No code or text from it is copied here.

---

## License

MIT — see [`LICENSE`](./LICENSE). Third-party attributions: [`THIRD_PARTY_LICENSES.md`](./THIRD_PARTY_LICENSES.md)
and [`THIRD_PARTY_NOTICES.md`](./THIRD_PARTY_NOTICES.md).
