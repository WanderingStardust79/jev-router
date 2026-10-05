# Customizations

Personal fork of [gargpratyush/jev-router](https://github.com/gargpratyush/jev-router), tracking upstream, not contributing back. My changes live on the `mattb/main` branch; `master` stays clean for upstream comparison.

Adopted 2026-10-04. Security vetted at upstream commit `38da6b84ea01241bfc41fbddc0928d0f40a703f0`. Later upstream commits are NOT vetted.

## Changes

- 2026-10-04: `CUSTOMIZATIONS.md`, `.github/workflows/sync-upstream.yml`: added by repo-adopt. Sync is fetch only (see below).
- 2026-10-04: `bin/jev-claude.mjs`, `src/codex-cli.mjs`: removed the launch directory `.env` source so a hostile repo cannot redirect prompts or the key via TYPESAFE_BASE_URL (vetting required patch). `package.json`, `package-lock.json`: pinned `@typesafe-ai/sdk` to exactly 0.6.0. Tests: 64 pass, 1 skipped.

## Syncing upstream

The weekly Action only reports new upstream commits and the files they touch. It never merges or pushes. To take an update, review the upstream diff first, then:

```bash
git fetch upstream
git diff 38da6b84ea01241bfc41fbddc0928d0f40a703f0..upstream/master --stat
git checkout master && git merge --ff-only upstream/master && git push origin master
git checkout mattb/main && git merge master
```

## License reminder

Upstream license: **MIT**. Permissive, adapt freely. Attribution required if you redistribute.

## Vetting conditions

Verdict: **CONDITIONAL** (4 stage scan, 2026-10-04: no malicious code or prompt injection found).

- Every fresh prompt you type goes to api.typesafe.ai (plus model id, context size estimate, available model ids). No history or tool results. For UCPM work use plain `claude` or pick a model with /model. Blocked for UCPM data until TypeSafe is named in writing (ucpm-data-privacy).
- REQUIRED PATCH before first run: bin/jev-claude.mjs and src/codex-cli.mjs load a .env from the launch directory, and TYPESAFE_BASE_URL is honored, so a hostile repo could redirect prompts and your key. Remove the process.cwd() .env source, keep ~/.jev-router.env as the only key source.
- Install from this fork with `git clone`, `npm ci`, `npm link`. Never `npm install -g jev-router` (ignores the lockfile). Pin @typesafe-ai/sdk to exactly 0.6.0.
- Tested upstream only on Claude Code 2.1.101. Upstream issues 58, 48, 28 report routing silently disabled on newer versions or with hooks. Run with JEV_DEBUG=1 once and confirm routing works.
- If plain `claude` ever opens with model `jev-router`, delete the model line from ~/.claude/settings.json (a hard kill can leave the sentinel).
- Skip the jev-codex path (writes a global skill). Do not merge the upstream benchmarking branch without review (it reads every transcript under ~/.claude/projects).
- The vetted commit recorded is head at adoption; the vetting report gave no SHA.
