# Copilot code review — nRF Connect SDK Add-on index

When reviewing pull requests that change files under `index/`, treat them as **add-on index submissions**.

## Review focus

1. Apply every check in `.github/instructions/addon-index.instructions.md` (path-scoped rules for `index/**/*.json`).
2. Cross-check the changed JSON against `resources/schema.json` required fields and allowed enum values.
3. Read the PR description for testing evidence (section 4 of the onboarding checklist).

## Output format

Post **one summary review comment** on the pull request (in addition to any inline comments on specific JSON issues). Use this structure:

```markdown
## Add-on index checklist review

### Summary
<One sentence: ready to merge / needs changes / blocked>

### Checklist
| Item | Status | Notes |
|------|--------|-------|
| ... | Pass / Fail / N/A / Cannot verify | Brief note |

### Required fixes
- <numbered list, or "None">

### Recommendations
- <optional improvements, or "None">

### PR description evidence
- <what the author provided vs what is missing>
```

Evaluate every checklist item from the path-scoped instructions. Mark items you cannot verify from the PR diff or description as **Cannot verify** and say what evidence is missing.

Do not approve or block merges. Human review by `@nrfconnect/ncs-ci` is still required.

## Out of scope

- Pull requests that only change non-`index/` paths: apply normal review standards only; skip the add-on checklist.
