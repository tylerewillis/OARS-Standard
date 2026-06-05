# Contributing to OARS

OARS is an open standard maintained by [Knov.ai](https://knov.ai). Feedback, proposed changes, and corrections are welcome from anyone implementing or relying on the standard. This document explains how to open an issue and how to propose a change.

By participating, you agree that contributions you submit to the specification and schema may be incorporated into the openly published, license-free standard.

## Where the source of truth lives

This repository mirrors the published standard for public reading and discussion:

- [SPECIFICATION.md](SPECIFICATION.md) — the full specification.
- [schema/oars-manifest.json](schema/oars-manifest.json) — the `oars.json` JSON Schema.
- [CHANGELOG.md](CHANGELOG.md) — version history.
- [GOVERNANCE.md](GOVERNANCE.md) — how the standard is maintained and versioned.

The canonical, always-current copies are served at <https://knov.ai/standard> and <https://knov.ai/schema/oars-manifest.json>. When the two differ, the published versions on knov.ai are authoritative.

## Opening an issue

Open a GitHub issue for anything you'd like considered. Useful issues tend to include:

- **What** — the requirement, schema field, or section involved (link to the exact heading or line where possible).
- **Why** — the problem: an ambiguity, a contradiction, a requirement that's infeasible or out of date, a missing case, or a draft spec that has since changed.
- **Evidence** — for anything referencing an external standard (an RFC, W3C Recommendation, or vendor/community draft), cite it and note its maturity (ratified vs. draft). The specification's [Caveats](SPECIFICATION.md#caveats) section is normative about how draft-derived claims must be scoped, dated, and version-pinned — proposed changes should respect it.

Good issue categories:

- **Correction** — a factual error, broken reference, or internal inconsistency.
- **Clarification** — wording that is ambiguous or open to multiple readings.
- **New requirement / level change** — a capability the standard should cover, or a change to an existing requirement.
- **Schema change** — an addition, constraint, or fix to `oars-manifest.json`.

## Proposing a change

1. **Open an issue first** for anything beyond a trivial typo, so the change can be discussed before work is done.
2. **Open a pull request** against this repository describing the change and linking the issue.
3. For **schema changes**, ensure `schema/oars-manifest.json` remains valid JSON Schema (draft 2020-12) and that the change is backward compatible where possible. Manifests pin a `schemaVersion`; breaking changes are introduced as a new dated schema version, not by silently altering the existing one.
4. For **specification changes**, keep the level structure cumulative and preserve the distinction between normative requirements, verification procedures, and aspirational **Frontier** items.

## How changes are accepted

Changes to the specification are proposed publicly, reviewed with community input, and published with version history. Knov.ai is the maintainer and makes the final call on what is incorporated, consistent with [GOVERNANCE.md](GOVERNANCE.md). Accepted changes are reflected in [CHANGELOG.md](CHANGELOG.md) and, when they alter conformance, in a new specification version.

## Contact

Prefer email? Write to [support@knov.ai](mailto:support@knov.ai).
