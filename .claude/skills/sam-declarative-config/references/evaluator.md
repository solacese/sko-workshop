# Kind: `evaluator`

Manifest path: `resources.evaluators`

An evaluator scores agent responses against per-example criteria.

Two evaluator types exist:
- `llm_judge` (user-creatable): scores a response by prompting an LLM
  with `promptTemplate`. Required: `model` (LLM model alias), the
  template (must contain `{{ Response }}`), and `choiceScores` (a map
  of choice → score, 2–26 entries).
- `heuristic` (system-seeded only): rule-based scorers like
  `valid_json`. These appear in the platform's evaluator list with
  `isDefault: true`; `sam config apply` does not manage them and they
  are filtered out of the desired/actual diff so the reconciler never
  attempts to update or delete them.

Authors only write `kind: evaluator` for `llm_judge` evaluators.


## Schema

CreateEvaluatorRequest is the body for POST /eval/evaluators.

| Field | Type | Required | Validation | Description |
|---|---|---|---|---|
| `name` | `string` | yes |  | (no description) |
| `description` | `string` | yes |  | (no description) |
| `type` | `string` |  |  | (no description) |
| `model` | `*string` |  | tri-state pointer | (no description) |
| `promptTemplate` | `*string` |  | tri-state pointer | (no description) |
| `choiceScores` | `map[string]any` |  |  | (no description) |

## Example

```yaml
kind: evaluator
name: example_evaluator
description: "Example evaluator description (replace me)."
spec:
  # optional: type: "standard"
  # optional: model: ""
  # optional: promptTemplate: ""
  # optional: choiceScores: {}
```
