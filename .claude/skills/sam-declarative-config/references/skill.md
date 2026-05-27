# Kind: `skill`

Manifest path: `resources.skills`

A skill is a reusable instruction-and-asset bundle that agents can
attach via `skillRefs` (clean-spec authoring) or `skillIds` (the wire
shape; the apply layer resolves names to platform UUIDs).

Skills are *directory-shaped* resources. The canonical metadata lives
in `SKILL.md` frontmatter — the same convention as
[agentskills.io](https://agentskills.io). There is **no** separate
`skills/<name>.yaml` header file; the bundle directory IS the resource.

## Directory layout

```
skills/
  <name>/
    SKILL.md           required: YAML frontmatter holds name/description
    references/        optional: per-topic detail markdown
    assets/            optional: prompt templates, images, helper scripts
```

The manifest entry under `resources.skills:` names the skill; the
matching directory under `skills/<name>/` carries the body. The
frontmatter `name` MUST match both the directory name and the manifest
entry, or `sam config plan` hard-errors.

## SKILL.md frontmatter

```markdown
---
name: support
description: Customer-support skill — escalation paths and tone guide.
tags: [support, customer-facing]
---

# Support

Body content follows the frontmatter. `name` and `description` are
required; other fields (`tags`, `license`, `tools`, `required_builtins`)
follow the agentskills.io spec and are consumed by the platform on
upload.
```

Renaming a skill means renaming both the directory and the
frontmatter `name` together — the apply layer treats a name change as
delete + create, and any agent's `skillRefs` referencing the old name
will surface a plan-time dangling-reference error until updated.

## Apply flow

1. The resolver reads `skills/<name>/SKILL.md`, parses its
   frontmatter, and projects `CreateSkillRequest{name, description}`.
2. The reconciler bundles `skills/<name>/` into a deterministic ZIP
   (sorted entries, fixed mtime — same machinery as toolsets) and
   uploads it via `POST /skills/{id}/upload`.
3. The platform validates the ZIP server-side: SKILL.md must be at the
   root with frontmatter whose `name` matches the create request.

V1 always re-uploads on update because the platform exposes no
content-hash field on `SkillDetailResponse`. Description-only edits
still pay the upload cost; revisit when the platform exposes a stored
hash.

## Pull flow

`sam config pull` downloads each skill's ZIP and unpacks it under
`skills/<name>/`. The bundle's SKILL.md (with its frontmatter) is
written verbatim. If the platform has no uploaded bundle yet
(discovery_status == "created") or the unpack fails, pull writes a
synthesized SKILL.md from the platform's stored name/description so
the user can re-author the bundle locally.


## Frontmatter shape

Each `<skill>/SKILL.md` begins with YAML frontmatter the platform reads:

```yaml
---
name: my-skill            # required, kebab-case
description: One-line summary used by the harness for skill discovery.
# tags: [optional, list]   # optional; surfaced in skill listings
---
```

Any other frontmatter keys are passed through to the platform unchanged. The body of `SKILL.md` is rendered as-is by the harness — keep it terse, with prompts/templates broken out into `assets/`.
