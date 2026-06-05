# Governance

How OARS is maintained, versioned, and evolved — and who is accountable for it. A standard is only as trustworthy as the body that keeps it.

## Who maintains OARS

The Open Agent Readiness Standard is published and maintained by **Knov.ai**. We author the specification, run the verification process, and operate the directory that exposes verified entities to AI agents. The standard is open: any entity may implement it without license or fee, and the [machine-readable schema](schema/oars-manifest.json) and [full specification](SPECIFICATION.md) are published for anyone to read and build against.

## Open and free to implement

Implementing OARS never requires payment to Knov.ai. Assessing your site, generating an `oars.json`, and listing in the directory as self-declared are free. Verification adds a human review by a Knov.ai reviewer who confirms what an automated check cannot; the standard itself remains open regardless of whether you pursue verification.

## How the standard changes

Changes to the specification are proposed publicly, reviewed with community input, and published with version history. Every `oars.json` records the `schemaVersion` it was written against, and each manifest's `@context` points at the permanent schema URL so existing files never break when the standard advances.

The current version is **OARS v1.1**. The full [change history](CHANGELOG.md) is maintained in this repository, and a copy lives at the bottom of the [specification](SPECIFICATION.md#changelog).

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to propose a change or report an issue.

## Certification and monitoring

Verification reflects our assessment at a point in time against the standard. To keep verified status meaningful as sites change, the specification defines:

- **Cumulative compliance** — each level requires full compliance with every level below it.
- **Recertification** — verified entities recertify on a defined cadence, with automated monitoring between cycles for higher levels.
- **Regression handling** — if monitoring detects a previously passing requirement now failing, the entity enters a cure period before its level is adjusted.
- **Appeals** — an entity that believes it was incorrectly assessed may submit an appeal, which a second reviewer assesses independently.

The exact cadences and timelines are defined in the [Certification Policies](SPECIFICATION.md#certification-policies) section of the specification.

## What we do and don't do

Knov.ai is infrastructure, not a marketing service. We define and verify the standard; we do not optimize sites for AI-search rankings, write content, or build websites. That separation keeps verification credible — we are not grading work we were paid to produce.

## Participate

Feedback on the standard, proposed changes, and corrections are welcome. To weigh in, report an issue with an assessment, or ask about implementation, open an issue (see [CONTRIBUTING.md](CONTRIBUTING.md)) or write to [support@knov.ai](mailto:support@knov.ai).

---

[Read the specification](SPECIFICATION.md) · [Assess your site](https://knov.ai/assess)
