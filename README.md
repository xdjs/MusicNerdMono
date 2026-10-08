# Music Nerd mono

One checkout for all of [Music Nerd](https://musicnerd.net)'s code. This repo holds no code itself, just git
submodules pointing at each repository, plus the guide ([`AGENTS.md`](AGENTS.md)) that tells you and your agent
how they fit together.

| Submodule | What it is |
|---|---|
| [`MusicNerdWeb`](https://github.com/xdjs/MusicNerdWeb) | The website (musicnerd.net), and where issues and trackers live |
| [`MusicNerdAPI`](https://github.com/xdjs/MusicNerdAPI) | The API: research workers, onboarding and the public endpoints |
| [`MusicNerdDocs`](https://github.com/xdjs/MusicNerdDocs) | The API docs site |
| [`MusicNerdSkills`](https://github.com/xdjs/MusicNerdSkills) | Agent skills: `mn-dev` (how we ship) and `mn-marketing` (videos that get artists to claim) |

## Get started

```bash
git clone --recurse-submodules https://github.com/xdjs/MusicNerdMono.git
cd MusicNerdMono
npx skills add xdjs/MusicNerdSkills   # install the skills for your agent
```

Already cloned without submodules? `git submodule update --init`.

Each submodule is its own repository with its own history, branches and PRs. Read [`AGENTS.md`](AGENTS.md) next.
