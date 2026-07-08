# Add-On Index Onboarding Checklist

Use this checklist when preparing an add-on repository and opening a pull request to join the [nRF Connect SDK Add-on index](https://github.com/nrfconnect/ncs-app-index).

For JSON field details, see [`resources/schema.json`](../resources/schema.json). For submission rules, see [`CONTRIBUTING.md`](../CONTRIBUTING.md).

---

## 1. Before you open a PR

- [ ] **Register on [Nordic DevZone](https://devzone.nordicsemi.com/)** using your organization's contact email (required for support routing).
- [ ] **Confirm your add-on extends nRF Connect SDK** — supplementary west manifest project, not a standalone unrelated repo.
- [ ] **Ensure the repository is accessible** to index users:
  - Public GitHub repo, **or**
  - Restricted repo with a `restricted.detailsUrl` explaining how to obtain access (see [`index/ant.json`](../index/ant.json), [`index/SmartThingsCommunity.json`](../index/SmartThingsCommunity.json)).

---

## 2. Add-on repository requirements

These are not validated by CI in *this* repo, but are required for the add-on to work in nRF Connect for VS Code.

- [ ] **West manifest present** — `west.yml` at repo root (or set `manifest` in the index entry if using a different filename).
- [ ] **At least one git tag** matching each entry in `releases[].tag`.
- [ ] **Each release declares NCS compatibility** — `releases[].sdk` must reflect the nRF Connect SDK version the tag builds against (e.g. `v2.9.0`).
- [ ] **Release metadata is accurate** — each release has `tag`, human-readable `name`, ISO 8601 `date`, and `sdk`.
- [ ] **Applications are discoverable by the extension**:
  - Default: single app at repo root with `CMakeLists.txt` and a `*.conf` file.
  - Multiple apps: set `apps` to a glob (e.g. `samples/*`, `examples/zephyr/*`) where each matched directory has `CMakeLists.txt` + `*.conf`.
- [ ] **Smoke-tested with west** — from a clean workspace:

  ```bash
  west init -m "<repo-url>" --mr <latest-tag>
  west update
  ```

- [ ] **Documentation URL is public and stable** — required `docsUrl` field; README-only URLs are acceptable (see [`index/blecon.json`](../index/blecon.json)).
- [ ] **(Recommended) README** explains prerequisites, hardware, and build steps for at least one supported NCS version.

---

## 3. Index submission (this repository)

- [ ] **Open a PR against [ncs-app-index](https://github.com/nrfconnect/ncs-app-index)** — do not fork-and-self-merge.
- [ ] **PR touches only `index/`** — add `index/<YourOrg>.json` or amend your existing org file. PRs changing other paths are rejected ([`CONTRIBUTING.md`](../CONTRIBUTING.md)).
- [ ] **File naming** — use your GitHub org/user slug (e.g. `blecon.json`, `golioth.json`).
- [ ] **Schema compliance** — validate locally before opening the PR:

  ```bash
  npm install
  npm run validate-index
  ```

- [ ] **Organization block** — required: `name`, `description`, `apps`.
- [ ] **Per-app block** — required: `name`, `repo`, `kind`, `tags`, `releases` (min 1), `description`, `docsUrl`.
- [ ] **`name` matches GitHub repo slug** — e.g. repo `https://github.com/blecon/blecon-device-sdk` → `"name": "blecon-device-sdk"`.
- [ ] **`kind` is one of** `template` | `sample` | `project`.
- [ ] **`tags` use allowed values** from [`resources/schema.json`](../resources/schema.json): `bluetooth`, `zigbee`, `lte`, `dfu`, `thread`, `matter`, `bt-mesh`, `sidewalk`, `lora-basics-modem`, `CSS`, `FSK`, `ble`, `blecon`, `connectivity`, `edge-ai`.
- [ ] **(Recommended) `contact.devzoneUsername`** — shown in the Support dialog.
- [ ] **(Recommended) `avatar`** — org and/or per-app image URL.
- [ ] **(Recommended) `testStatus.badge`** — GitHub Actions workflow badge URL ([`CONTRIBUTING.md`](../CONTRIBUTING.md)).
- [ ] **(If restricted) `restricted.detailsUrl`** — link to access instructions.

### Requesting a new tag

If none of the allowed `tags` values fit your add-on, open a **separate PR** that adds the tag to [`site/src/schema.ts`](../site/src/schema.ts) (the `validTags` array), then run `npm run generate-schemas` to update [`resources/schema.json`](../resources/schema.json). Get that PR merged before referencing the new tag in your index entry.

### Minimal valid example

```json
{
  "name": "Blecon",
  "description": "Blecon IoT Connectivity for Bluetooth SDK",
  "contact": { "devzoneUsername": "your-devzone-handle" },
  "apps": [{
    "name": "blecon-device-sdk",
    "repo": "https://github.com/blecon/blecon-device-sdk",
    "title": "Blecon Device SDK",
    "description": "...",
    "kind": "sample",
    "tags": ["blecon", "bluetooth", "connectivity"],
    "releases": [{
      "tag": "v2.0.1",
      "name": "Blecon Device SDK v2.0.1",
      "date": "2024-11-17T15:30:00Z",
      "sdk": "v2.8.0"
    }],
    "docsUrl": "https://github.com/blecon/blecon-device-sdk/blob/main/README.md"
  }]
}
```

---

## 4. Quality and review evidence (PR description)

- [ ] **Describe tests executed** — e.g. build on DK, NCS version(s), sample(s) run.
- [ ] **List supported boards / hardware** if not obvious from docs.
- [ ] **Note any external dependencies** (cloud accounts, licensed stacks, partner program enrollment).

---

## 5. CI and merge gates

- [ ] **`Validate index files` workflow passes** — triggered on PRs changing `index/*.json` ([`index-validation.yaml`](./workflows/index-validation.yaml)).
- [ ] **`Deploy to GitHub Pages` build job passes** — also runs `validate-index` and type-check ([`pages-deploy.yaml`](./workflows/pages-deploy.yaml)).
- [ ] **Copilot checklist review** — PRs that change `index/**/*.json` automatically request [GitHub Copilot code review](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/request-a-code-review). Copilot evaluates the submission against [`.github/instructions/addon-index.instructions.md`](./instructions/addon-index.instructions.md) and posts a summary comment. Address required fixes before merge.
- [ ] **Approval from `@nrfconnect/ncs-ci`** ([`CODEOWNERS`](./CODEOWNERS)).
- [ ] **After merge**, index rebuilds automatically; no manual publish step.

---

## 6. After merge (maintainer responsibilities)

- [ ] **Keep `releases` updated** when shipping new tags — add new release objects with correct `sdk` and `date`.
- [ ] **Update `defaultBranch`** if the recommended checkout tag changes.
- [ ] **Keep `docsUrl` current** when documentation moves.
