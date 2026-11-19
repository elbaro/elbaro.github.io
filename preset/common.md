---
title: Common Preset
permalink: /preset/common/
---
[Preset](../) / Common

## .pre-commit-config.yaml

Git hooks, taken from [zero-postgres](https://github.com/elbaro/zero-postgres/blob/main/.pre-commit-config.yaml).
Run them with [prek](https://github.com/j178/prek); `repo: builtin` does not work with `pre-commit`.

```yaml
default_install_hook_types:
  - pre-commit
  - commit-msg

repos:
  - repo: builtin
    hooks:
      - id: trailing-whitespace
      - id: check-added-large-files
      # - id: check-case-conflict
      - id: end-of-file-fixer
      - id: fix-byte-order-marker
      - id: check-json
      - id: check-toml
      - id: check-yaml
      - id: check-xml
      - id: mixed-line-ending
      - id: check-symlinks
      - id: check-merge-conflict
      # - id: no-commit-to-branch
      - id: check-executables-have-shebangs
  - repo: https://github.com/crate-ci/typos
    rev: v1.40.0
    hooks:
      - id: typos
  - repo: https://github.com/compilerla/conventional-pre-commit
    rev: v3.4.0
    hooks:
      - id: conventional-pre-commit
        args: [feat, fix, refactor, perf, test, docs, infra, deps, revert, release]
  - repo: local
    hooks:
      - id: rustfmt
        name: rustfmt
        description: Check if all files follow the rustfmt style
        entry: cargo fmt --all -- --check --color always
        language: system
        pass_filenames: false
        types: [rust]
```

### Initial setup

Run once after cloning. It installs the `pre-commit` and `commit-msg` hooks
listed in `default_install_hook_types`, and downloads the hook environments.

```sh
prek install --prepare-hooks
```

Check the whole repository once:

```sh
prek run --all-files
```

## biome.json

[Biome](https://biomejs.dev/) formatter config.

```json
{
	"formatter": {
		"lineWidth": 100
	},
	"html": {
		"experimentalFullSupportEnabled": true,
		"formatter": {
			"enabled": true
		}
	}
}

```

## .github/workflows/cla.yml

Fails a pull request until the author ticks the [Blue Oak Contributor License](https://blueoakcouncil.org/contributor/1.0.0) checkbox in the PR description, taken from [zero-postgres](https://github.com/elbaro/zero-postgres/blob/main/.github/workflows/cla.yml).
The checkbox line comes from `.github/pull_request_template.md`. Replace `elbaro` with the repository owner's login.

{% raw %}
```yaml
name: CLA

on:
  pull_request:
    types: [opened, edited, reopened, synchronize]

permissions: {}

jobs:
  cla:
    if: github.event.pull_request.user.type != 'Bot' && github.event.pull_request.user.login != 'elbaro'
    runs-on: ubuntu-latest
    steps:
      - env:
          PR_BODY: ${{ github.event.pull_request.body }}
        run: |
          grant='I grant the repository owner the [Blue Oak Contributor License Version 1.0.0](https://blueoakcouncil.org/contributor/1.0.0) for my contributions to the repository.'
          body=$(printf '%s\n' "$PR_BODY" | tr -d '\r' | perl -0pe 's/<!--.*?(-->|\z)//gs')
          if printf '%s\n' "$body" | grep -qxF -e "- [x] $grant" -e "- [X] $grant"; then
            echo "Contributor license granted."
          elif printf '%s\n' "$body" | grep -qxF -e "- [ ] $grant"; then
            echo "::error::Check the Blue Oak Contributor License checkbox in the PR description."
            exit 1
          else
            echo "::error::The PR description is missing the Blue Oak Contributor License line from .github/pull_request_template.md."
            exit 1
          fi
```
{% endraw %}
