# grafana-playground

[![CI](https://github.com/j6nca/grafana-playground/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/j6nca/grafana-playground/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/github/license/j6nca/grafana-playground)](LICENSE)

A sandbox for trying out Grafana features. Right now it is the source repository for a local
[Git Sync](https://grafana.com/docs/grafana/latest/as-code/observability-as-code/provision-resources/intro-git-sync/) proof of concept:
Grafana reads the dashboards in `dashboards/`, and (with a token) can commit changes made in the UI back here.

## Layout

```
dashboards/                      # Root that Grafana syncs (repository path: dashboards/)
├── Testing/                     # Each sub-directory becomes a Grafana folder
│   ├── _folder.json             # Folder UID + title (uid: testing)
│   ├── git-sync-test.json       # "Git Sync Test" (uid: git-sync-test)
│   └── Subfolder/               # Nested folder: Testing → Subfolder
│       ├── _folder.json         # (uid: gpg-testing-subfolder)
│       └── subfolder-test.json  # "Subfolder Test" (uid: gpg-subfolder-test)
└── Test_Again/
    ├── _folder.json             # (uid: gpg-test-again)
    └── git-sync-test.json       # "Git Sync Test" again, different folder (uid: gpg-git-sync-test)
```

- **One directory = one Grafana folder.** Nest directories to nest folders. Give each directory a `_folder.json`
  with a fixed `metadata.name` (the folder UID) and `spec.title`. Without it, Grafana generates the folder UID and it
  can change between syncs, breaking folder links and permissions.
- **One JSON file = one dashboard.** The file holds the dashboard model, the same JSON you get from
  *Export → Export as JSON* in Grafana. Keep a stable `uid` in each file so the dashboard's URL survives renames.
- **UIDs follow `gpg-<slug>`** (`gpg` = grafana-playground), for both folders and dashboards. UIDs must be unique across
  all of Grafana and are at most 40 characters (letters, digits, `-`, `_`). Set them once and never change them, even
  when renaming or moving. Titles don't need to be unique. `testing` and `git-sync-test` predate the convention.
- A dashboard can be managed by only one Git Sync repository. Pointing two repositories at the same files makes the second one's sync fail with
  `resource ... is managed by repo ...`.

## Connecting Grafana

Point a Git Sync repository at this repo:

| Setting | Value |
| ------- | ----- |
| URL     | `https://github.com/j6nca/grafana-playground` |
| Branch  | `main` |
| Path    | `dashboards/` |

- **Read-only (no token):** the repo is public, so a `git`-type repository with no token and no save
  workflows (`workflows: []`) can pull dashboards in. Grafana checks for new commits on an interval (no webhooks on localhost).
- **Read/write:** add a fine-grained GitHub token scoped to this repo (Contents and Pull requests read/write, Metadata read).
  The `write` workflow commits UI saves straight to `main`. The `branch` workflow pushes to a new branch and opens a PR.

With sync target `folder`, everything lands under one Grafana folder named after the repository, so `Testing`
appears inside it. With target `instance`, `Testing` is a top-level folder.

## Adding a dashboard

1. Build it in Grafana, then *Export → Export as JSON* (leave "Export for sharing externally" off).
2. Save it as `dashboards/<Folder>/<name>.json`, keeping its `uid`.
3. Open a PR. Once it merges, Grafana picks it up on the next sync.

CI checks that every file under `dashboards/` is valid JSON with a unique `uid` and a `title`.
