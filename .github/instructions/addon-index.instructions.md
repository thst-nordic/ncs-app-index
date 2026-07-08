---
applyTo: "index/**/*.json"
---

# Add-on index submission review

Evaluate the changed organization index file against this checklist. Flag schema violations as **required fixes**. Flag missing recommended fields as **recommendations**.

## 1. PR scope and process

- PR must only add or update file(s) under `index/` (no unrelated repo changes).
- File name should match the GitHub org/user slug (e.g. `blecon.json`).
- Organization block must include `name`, `description`, and `apps`.

## 2. Per-app required fields

Each app entry must have:

- `name` — matches the GitHub repo slug in `repo` (e.g. `https://github.com/org/my-addon` → `"name": "my-addon"`).
- `repo` — valid HTTPS GitHub URL.
- `kind` — one of `template`, `sample`, or `project`.
- `tags` — non-empty array; every value must be from the allowed set in `resources/schema.json`: `bluetooth`, `zigbee`, `lte`, `dfu`, `thread`, `matter`, `bt-mesh`, `sidewalk`, `lora-basics-modem`, `CSS`, `FSK`, `ble`, `blecon`, `connectivity`, `edge-ai`.
- `releases` — at least one release; each with `tag`, `name`, ISO 8601 `date`, and `sdk` (NCS version, e.g. `v2.9.0`).
- `description` — non-empty, describes the add-on.
- `docsUrl` — public documentation URL.

## 3. Schema and data quality

- No extra properties (schema uses `additionalProperties: false`).
- `releases[].date` must be valid ISO 8601 date-time.
- `sdk` values should look like NCS versions (`vX.Y.Z`), not arbitrary strings.
- If `restricted` is set, `restricted.detailsUrl` is required.
- If `contact` is set, `contact.devzoneUsername` is required.
- `defaultBranch`, when present, should match a release `tag` or be explained.
- New tag values not in the schema enum require a separate schema PR first.

## 4. Recommended fields

- `contact.devzoneUsername` for Support dialog.
- `avatar` at org and/or app level.
- `testStatus.badge` — GitHub Actions workflow badge URL.
- `license` when known.
- `apps` glob when samples live in subdirectories (e.g. `samples/*`, `examples/zephyr/*`).

## 5. Add-on repository (cannot fully verify from index JSON)

Mark **Cannot verify** unless the PR description or linked repo provides evidence:

- `west.yml` (or custom `manifest`) exists in the add-on repo.
- Each `releases[].tag` exists as a git tag in the add-on repo.
- `west init -m "<repo>" --mr <tag>` + `west update` was smoke-tested.
- Apps are discoverable (`CMakeLists.txt` + `*.conf` at root or under `apps` glob).
- `docsUrl` is reachable and documents build steps.

Ask the author to confirm these in the PR **Testing evidence** section if missing.

## 6. PR description evidence

The PR should describe:

- Builds/tests run, NCS version(s), and boards verified.
- Supported hardware if not obvious from docs.
- External dependencies (cloud accounts, licenses, partner programs).

Flag missing evidence as **Cannot verify** items, not automatic failures.

## 7. Examples of common failures

```json
// FAIL: name does not match repo slug
"repo": "https://github.com/acme/acme-sdk",
"name": "acme"

// FAIL: invalid tag
"tags": ["wifi"]

// FAIL: release missing sdk
"releases": [{ "tag": "v1.0.0", "name": "v1.0.0", "date": "2024-01-01T00:00:00Z" }]

// FAIL: restricted without detailsUrl
"restricted": {}
```

```json
// PASS: minimal valid shape
{
  "name": "Example Org",
  "description": "Example add-ons",
  "apps": [{
    "name": "example-addon",
    "repo": "https://github.com/example-org/example-addon",
    "title": "Example Add-on",
    "description": "An NCS add-on.",
    "kind": "sample",
    "tags": ["connectivity"],
    "releases": [{
      "tag": "v1.0.0",
      "name": "Example Add-on v1.0.0",
      "date": "2024-06-01T12:00:00Z",
      "sdk": "v2.8.0"
    }],
    "docsUrl": "https://github.com/example-org/example-addon/blob/main/README.md"
  }]
}
```
