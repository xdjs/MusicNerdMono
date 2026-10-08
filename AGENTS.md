# AGENTS.md

Guidance for any AI agent (and any new teammate) working across Music Nerd's code. `CLAUDE.md` points here.

This is a **git submodule workspace**: it holds no code, only pointers to each repository at a commit, plus this
guide. Each submodule is an independent repository with its own `AGENTS.md`, history, branches and PRs. Read this
file, then the owning repository's `AGENTS.md`, before changing anything.

## Repositories

| Submodule | What it is | Key tech | Package manager |
|---|---|---|---|
| `MusicNerdWeb` | The website, musicnerd.net: artist pages, claims, onboarding, the admin. **Issues and trackers for every repo live here.** | Next.js 15, React, Drizzle/Postgres (Supabase), Privy + NextAuth, Tailwind/Radix | npm |
| `MusicNerdAPI` | The API: research workers and their cron, onboarding, and the endpoints moving out of the web app | Next.js 16 (API routes only), Zod, Vitest | pnpm |
| `MusicNerdDocs` | The API docs site (Mintlify-format content, OpenAPI reference, Try it, `llms.txt`), live at musicnerd-docs.vercel.app | Next.js, OpenAPI 3.1 | pnpm |
| `MusicNerdSkills` | Agent skills: `mn-dev` (how work is tracked and shipped) and `mn-marketing` (videos that get artists to claim) | Markdown, Python validation scripts | none |

Not included yet: MNTv, the iOS app (`xdjs/MusicNerd`), the Discord bot, the Chrome extension. Add one as a
submodule when it becomes part of day-to-day work.

## Find context

| Task | Read first |
|---|---|
| Any work at all | this file → the owning repo's `AGENTS.md` → MusicNerdWeb `MEMORY.md` (the engineering handoff) |
| Product decisions and why | MusicNerdWeb `docs/rnd/decisions.md`, then the dated R&D/stand-up transcript it cites |
| Planning, tracking or shipping a change | `MusicNerdSkills/skills/mn-dev/SKILL.md` |
| A new or changed endpoint | MusicNerdDocs first (the page), then MusicNerdAPI (the code), then the client |
| UI or brand | MusicNerdWeb `DESIGN.md` (also its *Brand for media* section for videos) |
| Marketing videos | `MusicNerdSkills/skills/mn-marketing/SKILL.md` |
| Releases | MusicNerdWeb `docs/releases.md` |

## How the pieces connect

- **Web → API → database.** MusicNerdWeb serves the site and queues research jobs; MusicNerdAPI runs every
  research job kind, onboarding and its cron, and is moving toward owning all endpoints. Both read the same
  Supabase Postgres. `api.musicnerd.xyz` still serves MusicNerdWeb's legacy endpoints until each is ported.
- **Docs first.** An endpoint gets its page in MusicNerdDocs before it is built in MusicNerdAPI, then the client PR
  lands in whichever app uses it. When behaviour changes, update the page in the same change.
- **Environments.** Vercel previews and `staging.musicnerd.net` use the **staging** database; production uses its own.
  The same artist has different ids on each, so always say which.
- **Skills** are installed into each agent with `npx skills add xdjs/MusicNerdSkills`; edit them in
  `MusicNerdSkills`, never by copying them into another repo.

## Git workflow

1. **Never push directly to `main`** in any repository. Branch from `main` (`<contributor>/<slug>`, Codex uses
   `codex/<slug>`, docs-only may use `docs/<slug>`), conventional commits.
2. **Open every PR as a draft** and mark it ready only when its developer says it is good to merge. A ready PR can
   be merged by anyone once its checks pass; squash merge.
3. **A merge is not a release.** Production is promoted by hand (MusicNerdWeb: the approved `production-release`
   job; see `docs/releases.md`). Record "merged" and "in production" separately.
4. **Every repository is public.** No secrets, tokens, email addresses, database refs or personal paths in code,
   issues, PRs, docs or skills. Env-var names only.
5. **Changes across repos** are separate PRs, one per repository, linked by full ref (`xdjs/MusicNerdWeb#1465`).
   Then bump this mono's submodule pointers in their own PR.

### Updating the submodule pointers

After PRs merge in a submodule, update this repo so a fresh clone gets them:

```bash
git submodule update --remote MusicNerdWeb      # or any submodule; moves it to its origin/main
git checkout -b <contributor>/bump-submodules
git add MusicNerdWeb && git commit -m "chore: bump MusicNerdWeb"
gh pr create --draft
```

### Worktrees

Put new linked worktrees under this workspace's ignored `.worktrees/<repository>/<task>/`, created through the
repository that owns the code:

```bash
git -C MusicNerdWeb worktree add ../.worktrees/MusicNerdWeb/<task> -b <contributor>/<task> origin/main
git -C MusicNerdWeb worktree remove ../.worktrees/MusicNerdWeb/<task>
```

Remove a worktree only after its work is merged or otherwise preserved and its files have been checked. Do not move
existing worktrees or blanket-prune registrations.

## Commands

| Repo | Install | Dev | Gate before a PR |
|---|---|---|---|
| MusicNerdWeb | `npm ci` (see `docs/development.md`) | `npm run dev` (HTTPS, port 3000) | `npm run ci` (type-check, lint, tests, build) |
| MusicNerdAPI | `pnpm install` | `pnpm dev` | `pnpm test && pnpm type-check && pnpm lint:check && pnpm format:check && pnpm build` |
| MusicNerdDocs | `pnpm install` | `pnpm dev` | `pnpm test && pnpm type-check && pnpm lint:check && pnpm build` |
| MusicNerdSkills | none | none | `python3 scripts/portability_lint.py && python3 scripts/validate_manifests.py && python3 scripts/check_resolvable.py && python3 scripts/run_resolver_eval.py` |

Each repo's own `AGENTS.md` is the source of truth for its commands; if this table disagrees, that file wins and this
table gets fixed.

## People

Pete (product and design), Carl (releases), Sweetman (engineering; call them Sweetman in writing), and CY, who
executive-produces Music Nerd with Carl and leads the R&D retro and AMA. Attribute
decisions to whoever made them and where (stand-up, R&D sync).
