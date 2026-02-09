# Agent instructions

Guidance for AI agents working in this repository.

## What this repo is

- **Reusable GitHub Action** that reports releases to Amplitude.
- **Composite action** (YAML + bash steps in `action.yml`). No JavaScript/Node or Docker.
- Intended for **GitHub Marketplace**: one action per repo, single metadata file at root.

## Key files

| File | Role |
|------|------|
| `action.yml` | Action metadata (name, description, inputs, outputs, `runs`). **Only** action definition; keep at repo root for Marketplace. |
| `README.md` | User-facing docs: usage examples, input/output tables, Marketplace publishing steps. |
| `.github/workflows/test.yml` | CI: runs the action with `uses: ./` on push/PR to validate it. |

## Conventions

- **Single action**: Do not add a second `action.yml` (e.g. in a subfolder); Marketplace expects one at root.
- **Composite only**: New behavior goes in `runs.steps` in `action.yml` (bash `run:` or `uses:` other actions). Do not convert to Docker or JavaScript without explicit request.
- **Inputs/outputs**: Declare in `action.yml` and keep the README input/output tables in sync. All [Releases API](https://amplitude.com/docs/apis/analytics/releases) fields are supported (version, release_start, release_end, title, description, platforms, created_by, chart_visibility); plus `server` (standard/eu) and `release-name` (alias for title).
- **Secrets**: Document that API key and secret key should be passed via `with:` from `secrets.*` (e.g. `AMPLITUDE_API_KEY`, `AMPLITUDE_SECRET_KEY`). Never hardcode keys or add them to the repo.
- **Versioning**: Tags like `v1`, `v1.0.0` for releases; README examples use `@v1` or `@<sha>`.

## Making changes

- **New input**: Add under `inputs:` in `action.yml` (description, `required`, optional `default`). Add a row to the Inputs table in README.
- **New output**: Add under `outputs:` in `action.yml` with `value: ${{ steps.<id>.outputs.<name> }}`. Ensure a step has `id:` and writes to `$GITHUB_OUTPUT`. Add a row to the Outputs table in README.
- **New step**: Add to `runs.steps` in `action.yml`. Use `${{ inputs.* }}` for inputs; use `${{ github.action_path }}` or `$GITHUB_ACTION_PATH` for scripts next to the action.
- **Branding**: Optional in `action.yml` (`branding.icon`, `branding.color`). Icon must be from [Feather v4](https://feathericons.com/) (e.g. `bar-chart-2`).

## Testing

- Push or open a PR to trigger `.github/workflows/test.yml`.
- The workflow runs the action with `uses: ./` and checks the `reported` output; extend the workflow if you add new outputs or behavior.
