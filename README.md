# bedrock/github-action

GitHub Action for [bedrock](https://bedrock.searchhq.org) — builds a release
Android APK/AAB with no Android SDK, emulator, or queue behind other
customers' builds. This action installs the `bedrock` CLI, authenticates
with a token you provide, and runs `bedrock build`.

Your repo needs a `bedrock.json` already committed (`bedrock init`, run once
locally) — this action doesn't do interactive setup.

## Usage

```yaml
name: bedrock build
on: push
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ThembinkosiThemba/bedrock-github-action@v1
        with:
          api-token: ${{ secrets.BEDROCK_API_TOKEN }}
```

Get a token with `bedrock token create --name "GitHub Actions"` (or from the
dashboard's API Tokens page) and add it as `BEDROCK_API_TOKEN` in your repo's
Settings → Secrets and variables → Actions.

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `api-token` | yes | — | A bedrock API token. |
| `profile` | no | `release` | `debug` or `release`. |
| `aab` | no | `false` | Build an App Bundle instead of an APK. |
| `version` | no | latest | Pin a specific `bedrock` CLI version. |

## Outputs

| Output | Description |
|---|---|
| `build-id` | The build's ID. |
| `status` | `success`, `failed`, or `canceled`. |
| `download-url` | Presigned download URL — only set when `status` is `success`. |

The job also gets a summary (`$GITHUB_STEP_SUMMARY`) with the build ID,
status, and download link, so the result is visible without opening logs.

## Using the outputs

```yaml
      - uses: ThembinkosiThemba/bedrock-github-action@v1
        id: bedrock
        with:
          api-token: ${{ secrets.BEDROCK_API_TOKEN }}
      - run: echo "Download at ${{ steps.bedrock.outputs.download-url }}"
```

A failed or canceled build fails this step (non-zero exit) — no extra
`if:` check needed to stop the job.
