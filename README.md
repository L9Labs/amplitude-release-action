# Amplitude Release Action

Reusable GitHub Action to create releases in [Amplitude](https://amplitude.com) via the [Releases API](https://amplitude.com/docs/apis/analytics/releases).

## Usage

### Basic (release event)

```yaml
on:
  release:
    types: [published]

jobs:
  report-release:
    runs-on: ubuntu-latest
    steps:
      - uses: your-org/amplitude-release-action@v1
        with:
          amplitude-api-key: ${{ secrets.AMPLITUDE_API_KEY }}
          amplitude-secret-key: ${{ secrets.AMPLITUDE_SECRET_KEY }}
          version: "3.5.1"
          title: "Production release 3.5.1"
```

### Using GitHub release title, description, and version

When your workflow runs on `release: types: [published]`, you can pass the release’s title, body, and tag explicitly so Amplitude matches your GitHub release:

```yaml
on:
  release:
    types: [published]

jobs:
  report-release:
    runs-on: ubuntu-latest
    steps:
      - uses: your-org/amplitude-release-action@v1
        with:
          amplitude-api-key: ${{ secrets.AMPLITUDE_API_KEY }}
          amplitude-secret-key: ${{ secrets.AMPLITUDE_SECRET_KEY }}
          version: ${{ github.event.release.tag_name }}
          title: ${{ github.event.release.name }}
          description: ${{ github.event.release.body }}
```

- **version** → `github.event.release.tag_name` (e.g. `v1.2.3`)
- **title** → `github.event.release.name` (release title in the UI)
- **description** → `github.event.release.body` (release notes)

### With version and title

```yaml
- uses: your-org/amplitude-release-action@v1
  with:
    amplitude-api-key: ${{ secrets.AMPLITUDE_API_KEY }}
    amplitude-secret-key: ${{ secrets.AMPLITUDE_SECRET_KEY }}
    version: v1.2.3
    title: "Production release 1.2.3"
    description: "Bug fixes and new dashboard"
    platforms: "Web,iOS,Android"
```

## Inputs

All parameters map to the [Amplitude Releases API](https://amplitude.com/docs/apis/analytics/releases).

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `amplitude-api-key` | No* | - | Amplitude API key (or use `AMPLITUDE_API_KEY` secret) |
| `amplitude-secret-key` | No* | - | Amplitude secret key for server-side API auth (or use `AMPLITUDE_SECRET_KEY` secret) |
| `server` | No | `standard` | Server region: `standard` or `eu` (EU residency server) |
| `version` | No | `github.ref_name` | Release version (required by API) |
| `release_start` | No | Current UTC time | Start time in UTC; format `yyyy-MM-dd HH:mm:ss` |
| `release_end` | No | - | End time in UTC; format `yyyy-MM-dd HH:mm:ss` |
| `title` | No | `github.event.release.name` or ref | Release name/title (required by API) |
| `description` | No | `github.event.release.body` | Release description |
| `platforms` | No | - | Comma-separated platforms (e.g. `iOS,Android,Web`) |
| `created_by` | No | `github.actor` | Name of the user creating the release |
| `chart_visibility` | No | `true` | Show release on Amplitude charts as annotation |

\* If both API key and secret key are omitted, the action skips the API call and succeeds (useful for CI without credentials).

## Outputs

| Output | Description |
|--------|-------------|
| `reported` | Whether a release was reported to Amplitude (`true`/`false`) |
| `release_id` | Amplitude release id from the API response (empty when skipped) |

## Publishing to GitHub Marketplace

1. **Repository**: Public repo with a single `action.yml` (or `action.yaml`) at the **root**.
2. **Release**: Create a release (e.g. tag `v1`) and in the release form check **Publish this Action to the GitHub Marketplace**.
3. **Metadata**: Set category, description, and ensure `action.yml` has `name`, `description`, and optional `branding` (icon + color).
4. **Versioning**: Prefer semantic versions (`v1`, `v1.0.0`). Consumers can pin with `@v1` or `@<sha>`.

See [Publishing actions in the GitHub Marketplace](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-the-github-marketplace).

## License

[MIT](LICENSE)
