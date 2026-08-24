# allsaintsfermentery — safe-settings configuration

This repository stores the organization-level [`safe-settings`](https://github.com/github-community-projects/safe-settings) configuration for the `allsaintsfermentery` GitHub organization.

## How it works

[`safe-settings`](https://github.com/github-community-projects/safe-settings) is a community-maintained GitHub App that enforces repository settings as code. When installed on the org, it reads this repository and applies the defined settings to every repository in the organization.

## Repository layout

```
.github/
  settings.yml        # Shared org-wide defaults (applied to all repos)
  repos/              # Per-repository overrides (empty for now)
    .gitkeep
```

### `.github/settings.yml` — shared defaults

Contains the baseline configuration applied to every repository in the org unless overridden. Current baseline:

- **Visibility**: private by default
- **Merge strategy**: rebase-only (`allow_rebase_merge: true`; squash and merge commits disabled)
- **Branch cleanup**: `delete_branch_on_merge: true`
- **Repo features**: issues enabled; projects, wiki, and discussions disabled
- **Auto-merge**: enabled

### `.github/repos/` — per-repository overrides

Reserved for future repo-specific settings. To override the org defaults for a specific repository, add a file named `.github/repos/<repo-name>.yml`. The directory currently contains only a `.gitkeep` placeholder.

## Branch protection baseline

Branch protection is currently configured for the `main` branch with:

| Setting | Value |
|---|---|
| Required approving reviews | 1 |
| Dismiss stale reviews | true |
| Require code owner reviews | false |
| Enforce admins | false |
| Required linear history | true |
| Allow force pushes | false |
| Allow deletions | false |
| Required status checks (strict) | true |
| Required status check contexts | *(intentionally empty — see below)* |

### Required status checks

`required_status_checks.contexts` is intentionally left empty (`[]`) in this baseline. This means no specific CI checks are required to pass before merging.

Once you have stable CI workflows in place, replace the empty list with your actual GitHub Actions check names, for example:

```yaml
required_status_checks:
  strict: true
  contexts:
    - ci / test
    - ci / lint
```

The exact check name must match what appears on the pull request checks tab.
