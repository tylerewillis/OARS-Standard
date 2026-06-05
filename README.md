# OARS — Open Agent Readiness Standard

OARS defines what it means for a website or business entity to be **ready for interaction with AI agents**, autonomous systems, and language models acting on behalf of users. It is an open standard: any entity may implement it without license or fee.

**Maintained by [Knov.ai](https://knov.ai).**
**Current version:** 1.1 · **Human documentation:** <https://knov.ai/standard>

---

## What OARS is

OARS covers a foundational prerequisite tier (**Level 0: Reachable**) and five cumulative capability levels — from basic discoverability through full machine-operability. Each level builds on the previous: an entity cannot be certified at Level 3 without satisfying every requirement of Levels 0, 1, and 2.

| Level | Name | Sub-levels | Core question |
|-------|------|------------|---------------|
| 0 | Reachable | — | Is this entity operationally usable by an agent at all? |
| 1 | Discoverable | — | Can agents find and identify this entity? |
| 2 | Readable | 2a Parseable · 2b Interpretable | Can agents comprehend what this entity knows and offers? |
| 3 | Actionable | 3a Callable · 3b Tool-Exposed | Can agents take defined actions with this entity? |
| 4 | Transactable | 4a Priceable · 4b Payable | Can agents complete commercial transactions on behalf of users? |
| 5 | Operable | — | Does AI operate workflows inside this entity's business? |

OARS does not prescribe *how* requirements are implemented. It defines what successful implementation looks like and how compliance is verified — multiple technical approaches may satisfy any given requirement.

## What's in this repository

| File | Contents |
|------|----------|
| [SPECIFICATION.md](SPECIFICATION.md) | The full OARS specification — every level, requirement, and verification procedure. |
| [schema/oars-manifest.json](schema/oars-manifest.json) | The JSON Schema for the `oars.json` manifest published at OARS Level 2 and above. |
| [CHANGELOG.md](CHANGELOG.md) | Version history of the specification. |
| [GOVERNANCE.md](GOVERNANCE.md) | How OARS is maintained, versioned, and evolved — and who is accountable for it. |
| [CONTRIBUTING.md](CONTRIBUTING.md) | How to open issues and propose changes. |

The canonical, always-current copies live at:

- **Specification:** <https://knov.ai/standard>
- **Schema:** <https://knov.ai/schema/oars-manifest.json>

## The `oars.json` manifest

OARS Level 2 (Readable) introduces a machine-readable entity profile published at `/oars.json` (and/or `/.well-known/oars.json`). It declares an entity's identity, audience, knowledge, offerings, engagement process, agent capabilities, and OARS compliance posture in a single file that AI agents can read directly.

Validate your manifest against [schema/oars-manifest.json](schema/oars-manifest.json). Knov.ai publishes [its own `oars.json`](https://knov.ai/.well-known/oars.json) as the reference implementation.

## Getting started

1. **Assess** any domain for free against the standard — <https://knov.ai/assess>
2. **Generate** an `oars.json` manifest from the findings — <https://knov.ai/generate>
3. **Publish** the manifest at `/.well-known/oars.json` on your domain.
4. **Verify** — request verification and confirm domain ownership at <https://knov.ai/verify>. A Knov.ai reviewer completes the human review and assigns a verified level and tier.

Assessment, the generator, and a self-declared directory listing are free. Verification is reviewed by Knov.ai.

## License and use

The OARS specification and schema are open and may be implemented by any entity without license or fee. See [GOVERNANCE.md](GOVERNANCE.md) for how the standard is maintained and how changes are made, and [CONTRIBUTING.md](CONTRIBUTING.md) to propose changes or report issues.
