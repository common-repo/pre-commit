# common-repo/pre-commit

[![CI](https://github.com/common-repo/pre-commit/actions/workflows/ci.yaml/badge.svg)](https://github.com/common-repo/pre-commit/actions/workflows/ci.yaml)
[![Release](https://img.shields.io/github/v/release/common-repo/pre-commit?sort=semver)](https://github.com/common-repo/pre-commit/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A [common-repo](https://github.com/common-repo/common-repo) source template. Distributes a baseline pre-commit configuration and a PR lint workflow to downstream repositories.

## Distributed files

The template writes two files to the repo root when applied:

| File | Purpose |
| --- | --- |
| `.pre-commit-config.yaml` | Baseline hooks from prek's `builtin` repo — whitespace, EOF, line endings, JSON/YAML/TOML/XML parsing, merge-conflict markers, large-file detection, symlink checks, private-key detection, shebang checks |
| `.github/workflows/pre-commit.yaml` | PR-triggered GitHub Actions job that runs the hooks via [`j178/prek-action`](https://github.com/j178/prek-action) |

The config requires `prek >= 0.3.8` and installs `pre-commit` and `commit-msg` hook types.

## Usage

Add the template to `.common-repo.yaml`:

```yaml
- repo:
    url: https://github.com/common-repo/pre-commit
    ref: v1.3.0
```

Preview and apply:

```bash
cr diff     # preview changes
cr apply    # write files
```

No template variables to configure.

## Extending the hooks

Additions to `.pre-commit-config.yaml` append to the baseline via common-repo's `array_mode: append_unique` merge. Duplicates drop out.

### From a downstream leaf repo

Keep a `.pre-commit-config.yaml` at the repo root containing your additions. `cr apply` merges the template's baseline into it:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.8.0
    hooks:
      - id: ruff
      - id: ruff-format
```

### From another template repo

Chain this template as an upstream and layer a `src/.pre-commit-config.yaml` on top. `common-repo/conventional-commits` uses this pattern to add its commit-msg hook:

```yaml
# your-template/.common-repo.yaml
- include: ["src/**"]
- rename:
    - "^src/(.*)$": "$1"
- yaml:
    auto-merge: .pre-commit-config.yaml
    array_mode: append_unique
- repo:
    url: https://github.com/common-repo/pre-commit
    ref: v1.3.0
```

## Paths excluded from file-modifying hooks

Four hooks rewrite files in place: `trailing-whitespace`, `end-of-file-fixer`, `fix-byte-order-marker`, `mixed-line-ending`. These skip paths the repo doesn't author by hand. Without the exclude, release CI fails when one of these files is regenerated and doesn't match the hook's formatting rules.

- **Release artifacts** — `CHANGELOG.md`, `RELEASES.md`
- **LLM / agent-managed files** — `CLAUDE.md`, `AGENTS.md`, `llms.txt`, `.claude/**`, `.cursor/**`, `.github/copilot-instructions.md`

Checking hooks (`check-json`, `check-yaml`, and the rest) still run on these paths. The exclude only covers the four that modify files.

To run a file-modifying hook on one of these paths anyway, override `exclude:` for that hook in your own config.

## Versioning

Releases follow semver via [semantic-release](https://github.com/semantic-release/semantic-release). Pin to a specific tag:

```yaml
- repo:
    url: https://github.com/common-repo/pre-commit
    ref: v1.3.0   # tags: https://github.com/common-repo/pre-commit/releases
```

Breaking changes to the distributed files bump the major version.

## Repo layout

This repo inherits its CI, release, and pre-commit infrastructure from [common-repo/upstream](https://github.com/common-repo/upstream) via its own `.common-repo.yaml`. `src/` holds the files distributed to consumers. Root-level `.pre-commit-config.yaml`, `.releaserc.yaml`, `cog.toml`, and `.github/workflows/` govern this repo's own pipeline.

## License

[MIT](LICENSE)
