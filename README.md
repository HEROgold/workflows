# workflows

Reusable GitHub Actions workflows and actions for HEROgold's Python repos. Each repo keeps a thin `ci.yml` that calls these, so a fix lands here once and reaches every repo through a Dependabot update.

## Workflows

All of them trigger on `workflow_call` only. Each file's header comment lists its inputs.

| Workflow | What it does |
| --- | --- |
| `test.yml` | pytest on one Python version, plus an optional clean-install import check |
| `lint.yml` | One matrix job per tool, from a JSON list of `{"tool", "args"}` |
| `public-api.yml` | mypy, pyright, pyrefly, ty and zuban against the public API. Blocking or not, per caller |
| `gitleaks.yml` | Secret scan over the full history |
| `workflow-lint.yml` | zizmor and actionlint |
| `coverage.yml` | Coverage upload to Coveralls |
| `docs.yml`, `docs-publish.yml` | Docs build, PR previews and the gh-pages publish |
| `docker-lint.yml` | hadolint and droast |
| `docker-publish.yml` | Build, Trivy scan, then push to GHCR by digest |
| `skylos.yml` | Dead-code gate with PR annotations |
| `release.yml` | Tags and releases an untagged version, then dispatches `new-release-created` |

`.github/actions/setup-uv-python` installs uv and Python and syncs the locked dependencies. The workflows here use it through `uses: $/...`, so it always matches the version of the calling workflow.

## Calling a workflow

Pin by full commit SHA with the version as a comment. Dependabot keeps it current.

```yaml
jobs:
  test:
    strategy:
      matrix:
        python-version: ["3.13", "3.14"]
    uses: HEROgold/workflows/.github/workflows/test.yml@<sha> # v1.0.0
    with:
      python-version: ${{ matrix.python-version }}
```

## Releases

Each release is an immutable GitHub release, `vX.Y.Z`. The plain tags `vX` and `vX.Y` move to the latest release.
