# allsaintsfermentery/.github

This repository is the `allsaintsfermentery` organization's policy-as-code home for [`safe-settings`](https://github.com/github-community-projects/safe-settings).

## What this repo is for

- Storing the shared `safe-settings` baseline for repositories in the organization
- Leaving room for future per-repository overrides
- Running `safe-settings` from GitHub Actions in this repository

## Repository layout

```text
.github/
  settings.yml                 # Org-wide baseline applied across repositories
  repos/                       # Optional per-repository overrides
    .gitkeep
  workflows/
    safe-settings.yml          # Dry-run on PRs, apply on pushes to main
```

### `.github/settings.yml`

`.github/settings.yml` holds the baseline configuration that `safe-settings` reads and applies across the organization unless a more specific override exists.

Current baseline highlights:

- private repositories by default
- rebase-only merges
- branch deletion after merge
- issues enabled
- projects, wiki, and discussions disabled
- a `main` branch protection baseline with linear history enabled

### `.github/repos/`

`.github/repos/` is reserved for repository-specific overrides. To customize one repository later, add `.github/repos/<repo-name>.yml`. It is intentionally empty today except for `.gitkeep`.

## How safe-settings runs here

This repository runs `safe-settings` from GitHub Actions instead of as a long-lived webhook service.

- Pull requests that change `README.md`, `.github/settings.yml`, `.github/repos/**`, or `.github/workflows/safe-settings.yml` run `safe-settings` in dry-run mode.
- Pushes to `main` for those same paths apply the settings.
- `workflow_dispatch` is also available for an on-demand dry-run.

The workflow follows the usage model documented by the community project: it checks out [`github-community-projects/safe-settings`](https://github.com/github-community-projects/safe-settings) at a pinned release, installs dependencies, and runs `npm run full-sync`. `safe-settings` does not currently provide a dedicated first-party GitHub Action for this flow, so this repository uses the supported Node-based invocation instead.

## Authentication

The workflow uses a private GitHub App, not a personal access token.

- `actions/create-github-app-token@v3` mints a short-lived installation token for checking out this repository from the app credentials.
- `safe-settings` itself runs with the same app's `APP_ID` and private key, which is the supported authentication model for `npm run full-sync`.
- No PAT is required.
- A webhook secret is not required for this workflow-only setup because the workflow is not hosting the `safe-settings` webhook server.

## Required repository or organization configuration

Configure these as either:

- repository variables/secrets in `allsaintsfermentery/.github`, or
- organization variables/secrets scoped to this repository

### Variables

- `SAFE_SETTINGS_APP_ID` — numeric GitHub App ID (example placeholder: `1234567`)
- `SAFE_SETTINGS_APP_CLIENT_ID` — GitHub App client ID used by `actions/create-github-app-token` (example placeholder: `Iv1.0123456789abcdef`)

### Secrets

- `SAFE_SETTINGS_APP_PRIVATE_KEY` — the full PEM private key for the GitHub App
- `SAFE_SETTINGS_APP_CLIENT_SECRET` — the GitHub App client secret

## Recommended GitHub App permissions

For the current baseline in this repository, start with the least privilege that supports repo settings management:

### Repository permissions

- Administration: **Read and write**
- Contents: **Read-only**
- Metadata: **Read-only**

If you later manage additional settings such as teams, repository variables, environments, or custom properties, expand permissions only as needed for those features.

Install the app on the `allsaintsfermentery` organization. For an org-wide baseline, install it on all repositories that should receive the managed settings.

## Setup checklist

- [ ] Create a private GitHub App for `safe-settings`
- [ ] Record the App ID and Client ID
- [ ] Generate and download a private key for the app
- [ ] Grant the app the minimum required permissions
- [ ] Install the app on the `allsaintsfermentery` organization
- [ ] Save `SAFE_SETTINGS_APP_ID` and `SAFE_SETTINGS_APP_CLIENT_ID` as variables
- [ ] Save `SAFE_SETTINGS_APP_PRIVATE_KEY` as a secret
- [ ] Open a pull request that changes one of the managed files and confirm the dry-run workflow passes
- [ ] Merge to `main` to apply the settings
