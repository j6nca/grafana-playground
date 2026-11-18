# AGENTS.md

## Purpose

Sandbox repo for trying out Grafana features. Currently the source repository for a local Grafana Git Sync
proof of concept: Grafana syncs the dashboard JSON under `dashboards/` into folders and dashboards.
There is no application code, build or release process.

## Structure

```
.
├── .agents/              # Agent instructions and skills
├── .github/workflows/
│   └── ci.yml            # Secret-file check + dashboard JSON validation
├── dashboards/           # Git Sync root (repository path: dashboards/)
│   └── <Folder>/         # Each directory maps to a Grafana folder
│       └── <name>.json   # One dashboard per file
├── AGENTS.md
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## Conventions

- Dashboard files are plain Grafana dashboard JSON (the *Export as JSON* model), one dashboard per file.
  Every dashboard needs a `title` and a stable, repo-unique `uid`; don't change an existing `uid`.
- Directory names are Grafana folder titles. Keep them human-readable (`Testing`, not `testing-folder`).
- Grafana may commit to this repo itself (UI saves via Git Sync's `write`/`branch` workflows), so expect
  commits and PRs that you didn't author, and pull before editing dashboards.
- Never commit tokens. Git Sync credentials live in Grafana or the local PoC's `.env`, not here.
- Commits follow Conventional Commits; branches follow `feat|bug|hotfix|release|chore/<short-description>`.
  See `CONTRIBUTING.md`.
