# Contributing

## Workflow

1. Create a branch: `git checkout -b feat/your-change`
2. Add or edit dashboards under `dashboards/` (see the README for the layout)
3. Commit using [Conventional Commits](#commit-style)
4. Push and open a pull request. CI must pass before merging.

Grafana can also commit here through Git Sync. Pull before editing a dashboard that may have been saved from the UI.

## Branch Naming

`feat|bug|hotfix|release|chore/brief-3-5-word-description`, e.g. `feat/add-latency-dashboard`.

## Commit Style

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>
```

Types: `feat`, `fix`, `chore`, `docs`, `test`, `refactor`, `perf`, `ci`

Examples:
- `feat(dashboards): add Git Sync Test dashboard`
- `fix(dashboards): correct panel query in Testing/latency`
