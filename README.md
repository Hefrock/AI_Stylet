# AI_Stylet

Audio digest about topics at the intersection of Healthcare and AI.

This repo is the public-facing host for the show: it serves the podcast
RSS feed and episode audio over HTTPS via GitHub Pages, so any podcast
app (or a browser) can reach them at a stable URL. The episodes
themselves are produced elsewhere, by the `broadcast` skill in the
[agent-skills](https://github.com/Hefrock/agent-skills) repo — this repo
holds nothing but the files a listener or a podcast app actually fetches.

## Layout

```
docs/
  index.html       landing page
  feed.xml         podcast RSS feed (written by distribute.py, not by hand)
  feed-episodes.json   distribute.py's own index of what's in the feed
  episodes/
    2026-09-08.wav  one file per published episode
  vault_notes/
    2026-09-08.md   Obsidian-formatted note per episode (not meant to be
                     linked from the public site — kept here only because
                     that's where distribute.py writes it; fine to leave
                     public, since it's the same content as the feed
                     description, just reformatted)
```

`docs/` is a GitHub Pages source folder — everything under it is public
and served at `https://hefrock.github.io/AI_Stylet/` once Pages is
enabled (see below). Nothing outside `docs/` is served.

## One-time setup (manual — no API access to do this remotely)

GitHub Pages has to be turned on once in the repo's settings; there's no
way to do this from a script or bot token, it has to be a human clicking
through the UI:

1. On GitHub: **Settings → Pages**
2. Under **Build and deployment → Source**, choose **Deploy from a branch**
3. **Branch:** `main`, folder **`/docs`** → **Save**

GitHub will publish the site within a minute or two at
`https://hefrock.github.io/AI_Stylet/`. Nothing else needs to change on
the code side for this to work — the URL is stable no matter how often
the folder's contents are republished.

## Publishing an episode

From the `agent-skills` repo, after `orchestrate.py` has produced an
episode:

```
python skills/broadcast/scripts/distribute.py \
  --data-dir <same --data-dir orchestrate.py used> \
  --date 2026-09-08 \
  --publish-dir /path/to/this/repo/docs \
  --base-url https://hefrock.github.io/AI_Stylet \
  --feed-link https://hefrock.github.io/AI_Stylet/
```

Then commit and push the changes under `docs/` in this repo. That's the
one deliberate, human-run step that actually makes an episode live —
`distribute.py` only writes the files; it never commits or pushes on its
own (see its own docstring for why: publishing a podcast feed is a
public, hard-to-fully-undo action once a feed URL is syndicated
anywhere).
