---
name: sam-declarative-config
description: Author SAM declarative-config repos (manifests, per-kind YAML, skills) and drive sam config plan/apply/pull/migrate. Auto-generated from the live DTO + type schemas at the bundled CLI version; re-run `sam skill install --force` after a CLI upgrade.
version: main-v2.35.0-158-gf7cabbfa
---

# SAM Declarative Config

## Overview

A SAM declarative-config repository captures the desired state of a SAM
platform as plain YAML. One **manifest** document lists which resources
to apply; per-kind YAML files (one per resource) live under
well-known top-level directories; non-config artefacts (skills, tool
binaries) live in their own directories.

`sam config plan` shows the diff between the manifest and the running
platform; `sam config apply` reconciles the platform to match. The same
files round-trip through `sam config pull`, which serialises a running
platform back into a fresh repo.

The schema for every kind is auto-generated from the runtime DTOs.
`sam config schema show <kind>` prints the live truth; this skill's
schema sections are a snapshot from the CLI version that wrote it
(see the frontmatter `version:` field — re-run `sam skill
install` after a CLI upgrade to refresh).


## How to use this skill

This skill is split into an orientation file (this SKILL.md) plus a `references/` directory of per-topic detail. SKILL.md is the table of contents — pull in the relevant `references/<topic>.md` when the work calls for it:

- **Authoring a manifest** — read `references/manifest.md`.
- **Authoring a resource YAML** — read `references/<kind>.md` for the kind you're touching.
- **Authoring a gateway** — `references/gateway.md`. Find the section matching the gateway type (`## type: slack`, `## type: email`, …); its field table + example are the canonical authoring source.
- **Authoring a connector** — `references/connector.md`. Each `## type: <type>` carries one or more `### subtype: <subtype>` sections.
- **Authoring a workflow** — `references/workflow.md`. Top-level workflow shape first, then per-node-type sections (`## node type: agent`, `## node type: switch`, …).
- **Authoring an evaluation corpus** — `references/dataset.md` for examples, `references/evaluator.md` for scorers, `references/experiment.md` to bind a dataset + evaluators to an agent. Trigger experiments from the CLI via `sam eval run <experiment-name>`.
- **Working with toolsets** — `references/toolset.md` is the entry point. It covers the on-disk layout (`toolsets/<name>.yaml` + `toolsets/<name>/{src,<name>.zip}`), the two lifecycle workflows (**author** with sources under `src/`, **mirror** with the pre-built zip from `sam config pull`), the full CLI surface (`sam toolset init/sync`, `sam config plan/apply/pull`, `sam config cache prune`), the toolset-level `spec.config` (secrets + shared defaults), and the per-agent `toolsetConfigs[*].configValues` overlay (per-agent overrides; flat for kind:toolset packages, nested-by-tool for builtin toolsets). Drill into `references/tool-build.md` for the build pipeline — resolution order, the `build.sh`/`build.bat` contract, the `.sam-cache/build/` cache, and `--no-build` semantics.
- **CLI authentication** — `references/cli-auth.md`. Covers `sam auth login/logout/status/list`, the `auth: { type: oauth }` target shape, and the `~/.sam/auth/<target>.json` cache layout. Also documents `sam api` — the generic authenticated HTTP client for poking at the gateway when authoring or debugging configs (read live DTOs, walk `PaginatedResponse` lists, reproduce FE calls).
- **Repo layout / common patterns / common mistakes** — `references/layout.md`, `references/common-patterns.md`, `references/common-mistakes.md`.

## Design guidance

Before authoring a config, read the relevant design reference. These cover *what to build and why*, not the YAML shape — pair them with the resource-kind references above.

- **Component selection** (workflow vs agent vs skill, when to split, peer delegation) — `references/design/best-practices.md`.
- **Core concepts** (terminology, agent card model, peer-delegation mechanics) — `references/design/concepts.md`.
- **Agent design** (instruction writing, tool selection, structured output, HIL, agent card, model selection) — `references/design/agent-design.md`.
- **Workflow design** (when to use workflows, design patterns, structured I/O, error handling, anti-patterns) — `references/design/workflow-design.md`.
- **Skill design** (when to package a skill, instruction shape, references layout) — `references/design/skill-design.md`.

## Resource kinds index

| Kind | Plural directory | Reference |
|---|---|---|
| `manifest` | `manifests/` | `references/manifest.md` |
| `model` | `models/` | `references/model.md` |
| `agent` | `agents/` | `references/agent.md` |
| `gateway` | `gateways/` | `references/gateway.md` |
| `workflow` | `workflows/` | `references/workflow.md` |
| `toolset` | `toolsets/` | `references/toolset.md` |
| `connector` | `connectors/` | `references/connector.md` |
| `skill` | `skills/` | `references/skill.md` |
| `dataset` | `datasets/` | `references/dataset.md` |
| `evaluator` | `evaluators/` | `references/evaluator.md` |
| `experiment` | `experiments/` | `references/experiment.md` |

## Pre-flight rules

- Every per-resource YAML file declares its own `kind:` (e.g. `kind: agent`). The manifest is `kind: manifest` and lives separately.
- YAML field names are **camelCase** (matches the platform REST API). snake_case keys are silently dropped.
- Use `${VAR}` placeholders for credentials, never literal tokens. See `references/common-patterns.md` for the full substitution rules.
- See `references/common-mistakes.md` before opening a PR.
