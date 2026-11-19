---
title: Common
permalink: /preset/common/
---
## .pre-commit-config.yaml

```yaml
default_install_hook_types:
  - pre-commit
  - commit-msg

repos:
  - repo: builtin
    hooks:
      - id: trailing-whitespace
        priority: 0
      - id: check-added-large-files
        priority: 20
      - id: check-case-conflict
        priority: 20
      - id: end-of-file-fixer
        priority: 1
      - id: fix-byte-order-marker
        priority: 2
      - id: check-json
        priority: 20
      - id: check-yaml
        priority: 20
      - id: check-xml
        priority: 20
      - id: mixed-line-ending
        priority: 3
      - id: check-symlinks
        priority: 20
      - id: check-merge-conflict
        priority: 20
      # - id: no-commit-to-branch
      - id: check-executables-have-shebangs
        priority: 20
  - repo: https://github.com/crate-ci/typos
    rev: v1.50.2
    hooks:
      - id: typos
        priority: 4
  - repo: https://github.com/compilerla/conventional-pre-commit
    rev: v4.4.0
    hooks:
      - id: conventional-pre-commit
        priority: 20
        args: [feat, fix, refactor, perf, test, docs, infra, deps, revert, release]
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.15.14
    hooks:
      - id: ruff-check
        priority: 9
        args: [--fix, --select, I]
        types_or: [python, pyi]
        stages: [pre-commit]
  - repo: https://github.com/psf/black-pre-commit-mirror
    rev: 25.1.0
    hooks:
      - id: black
        priority: 10
        types: [python]
        stages: [pre-commit]
  - repo: https://github.com/biomejs/pre-commit
    rev: v2.5.13
    hooks:
      - id: biome-format
        priority: 10
        files: ""
        types_or: [javascript, ts, vue, css, html]
        stages: [pre-commit]
  - repo: https://github.com/tombi-toml/tombi-pre-commit
    rev: v1.11.1
    hooks:
      - id: tombi-format
        priority: 10
        stages: [pre-commit]
      - id: tombi-lint
        priority: 20
  - repo: local
    hooks:
      - id: cargo-fmt
        priority: 10
        name: cargo-fmt
        entry: cargo fmt --all
        language: system
        pass_filenames: false
        types: [rust]
        stages: [pre-commit]
```

### Initial setup

```sh
prek install --prepare-hooks
```

```sh
prek run --all-files
```

## .github/renovate.json

{% raw %}
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["config:recommended"],
  "dependencyDashboard": false,
  "timezone": "Asia/Seoul",
  "platformAutomerge": false,
  "minimumReleaseAge": "7 days",
  "constraints": { "rust": "stable" },
  "vulnerabilityAlerts": { "automerge": true, "platformAutomerge": true, "commitMessageSuffix": "" },
  "osvVulnerabilityAlerts": true,
  "prHourlyLimit": 0,
  "separateMajorMinor": false,
  "lockFileMaintenance": { "enabled": true, "commitMessageAction": "update", "commitMessageTopic": "lockfiles" },
  "prFooter": "",
  "prBodyTemplate": "{{{header}}}{{{table}}}{{{warnings}}}{{{notes}}}{{{changelogs}}}{{{controls}}}",
  "commitMessagePrefix": "deps: ",
  "commitMessageAction": "update",
  "commitMessageTopic": "{{depName}}",
  "commitMessageExtra": "to {{#if isPinDigest}}{{{newDigestShort}}}{{else}}{{#if isMajor}}v{{{newMajor}}}{{else}}{{#if isSingleVersion}}v{{{newVersion}}}{{else}}{{{newValue}}}{{/if}}{{/if}}{{/if}}",
  "packageRules": [
    {
      "matchPackageNames": ["*"],
      "commitMessageTopic": "{{depName}}",
      "groupName": "dependencies",
      "groupSlug": "dependencies"
    }
  ]
}
```
{% endraw %}

## .github/pull_request_template.md

```markdown
- [ ] I grant the repository owner the [Blue Oak Contributor License Version 1.0.0](https://blueoakcouncil.org/contributor/1.0.0) for my contributions to the repository.
```

## .github/workflows/cla.yml

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
