# Changelog

All notable changes to the **Open Agent Readiness Standard (OARS)** are recorded here. The standard is versioned with a major.minor version; every `oars.json` records the `schemaVersion` (an ISO 8601 date) it was written against, and each manifest's `@context` points at the permanent schema URL so existing files never break when the standard advances.

The full, always-current specification lives at <https://knov.ai/standard>.

---

## [1.2] — 2026-06-05

Added the optional **`identity.sameAs`** field to the `oars.json` schema — an array of URLs to public profiles or records (social accounts, code repositories, business registries, directory listings) that externally verify the entity. It is the schema.org `sameAs` equivalent, and it supports the Level 1 requirement that schema.org identity be verifiable against publicly verifiable information by giving agents and reviewers independent sources to cross-check against.

Backward compatible: the field is optional and additive — existing manifests remain valid, and each manifest's `@context` still points at the permanent schema URL.

## [1.1] — 2026-05-31

Added **Level 0: Reachable** prerequisite tier. Split Levels 2/3/4 into sub-levels (2a Parseable / 2b Interpretable, 3a Callable / 3b Tool-Exposed, 4a Priceable / 4b Payable). Renamed Level 3 **Interactable → Actionable**.

Also added:

- Web Bot Auth (HTTP Message Signatures, RFC 9421) and Content Signals.
- The full `/.well-known/` discovery inventory.
- Agent Skills discovery.
- Markdown content negotiation and machine-readable feeds.
- Idempotency, RFC 9457 Problem Details, and async-operation API conventions.
- MCP Server Cards plus the MCP security control baseline.
- The ACP / UCP / AP2 / x402 agentic-commerce stack at Level 4.
- Agent identity, observability (OpenTelemetry GenAI), and SLAs at Level 5.
- New **Frontier** (aspirational) and **Caveats** (normative scoping) sections.

> **A note on Level 3's name.** Through OARS v1.0 this level was named *Interactable*. As of v1.1 it is *Actionable*. Certifications, badges, and directory listings issued as "Interactable" are equivalent to "Actionable"; the change is nominal, not a regression.

## [1.0] — 2026-05-28

Initial specification draft.

---

## Schema versions

The `oars.json` manifest schema is versioned by date (`schemaVersion`).

| schemaVersion | Notes |
|---------------|-------|
| 2026-06-05 | Current. Adds the optional `identity.sameAs` field for externally verifiable identity references; aligns with OARS v1.2. |
| 2026-05-31 | Level vocabulary covers Level 0 (Reachable) through Level 5 (Operable) with sub-levels; aligns with OARS v1.1. |
