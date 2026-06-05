# OARS Specification
## Open Agent Readiness Standard v1.2

**Published by:** Knov.ai  
**Version:** 1.2  
**Status:** Published 6-5-2026  
**Schema:** https://knov.ai/schema/oars-manifest.json  
**Human documentation:** https://knov.ai/standard  

---

## Overview

OARS (Open Agent Readiness Standard) defines what it means for a website or business entity to be ready for interaction with AI agents, autonomous systems, and language models acting on behalf of users.

The standard covers a foundational prerequisite tier (**Level 0: Reachable**) and five cumulative capability levels — from basic discoverability through full machine-operability. Each level builds on the previous. A business cannot be certified at Level 3 without satisfying all requirements of Levels 0, 1, and 2.

Three levels are divided into two sub-levels each — **2a/2b, 3a/3b, 4a/4b** — because each bundles two genuinely distinct capability tiers. Sub-levels add precision without disturbing the cumulative ladder: a level is fully satisfied only when both of its sub-levels are satisfied, and a single satisfied sub-level is reported as a partial signal, not as level certification.

OARS does not prescribe how requirements are implemented. It defines what successful implementation looks like and how compliance is verified. Multiple technical approaches may satisfy any given requirement.

Many requirements below reference IETF RFCs, W3C Recommendations, and vendor or community draft specifications at varying stages of maturity. The **Caveats** section at the end of this document is normative with respect to how draft-derived conformance claims must be scoped, dated, and version-pinned. Read it.

---

## The Levels

| Level | Name | Sub-levels | Core Question |
|-------|------|------------|---------------|
| 0 | Reachable | — | Is this entity operationally usable by an agent at all? |
| 1 | Discoverable | — | Can agents find and identify this entity? |
| 2 | Readable | 2a Parseable · 2b Interpretable | Can agents comprehend what this entity knows and offers? |
| 3 | Actionable | 3a Callable · 3b Tool-Exposed | Can agents take defined actions with this entity? |
| 4 | Transactable | 4a Priceable · 4b Payable | Can agents complete commercial transactions on behalf of users? |
| 5 | Operable | — | Does AI operate workflows inside this entity's business? |

> **A note on Level 3's name.** Through OARS v1.0 this level was named **Interactable**. As of v1.1 it is **Actionable** — the 3a/3b split made the older name ambiguous between "can be called" and "exposes tools." Certifications, badges, and directory listings issued as "Interactable" are equivalent to "Actionable"; the change is nominal, not a regression.

---

## Certification Policies

### Cumulative Compliance
Each level assumes full compliance with all preceding levels, including **Level 0**. Level 3 certification requires that Levels 0, 1, and 2 are fully satisfied and verified. For a level with sub-levels, full certification at that level requires **both** sub-levels; a single sub-level is recorded as a partial designation (e.g., "Level 3a"), not as certification at the level.

### Recertification Cadence
- **Levels 0–2:** Annual recertification required
- **Levels 3–5:** Annual recertification with quarterly automated monitoring between cycles

### Regression Policy
Knov.ai's automated monitoring checks certified entities quarterly. If a regression is detected (previously passing requirements now failing), the entity enters a 30-day cure period with notification. If not resolved within 30 days, certification drops to the highest level (or sub-level) the entity currently fully satisfies.

### Partial Compliance Designation
Entities that pass all automated checks for a level and have submitted for human review but have an outstanding requirement within 90 days of a known deadline receive a "Level X Pending" designation. A single satisfied sub-level of a two-part level is likewise surfaced as a partial designation. These designations do not appear in the verified directory but do not block progress.

### Appeals Process
Entities who believe they were incorrectly assessed may submit an appeal with supporting evidence. A second Knov.ai reviewer assesses independently. Binding decision issued within 14 business days.

---

## Level 0: Reachable

### Definition
Level 0 establishes the operational baseline an automated agent requires before any higher OARS level is meaningful. An origin that cannot complete a TLS handshake, returns errors under routine load, responds too slowly for an interactive agent loop, omits baseline transport-security headers, refuses cross-origin requests from agent runtimes, or interposes a hard human-interaction wall cannot deliver any agent-facing capability — regardless of how well it implements discovery, structured actions, or authentication at Levels 1 and above. This tier asserts only that the surface is transport-secure, available, performant, minimally hardened, and not categorically hostile to non-browser, non-human clients. It introduces no agent-specific affordances; it guarantees the floor on which they are built.

### Requirements

#### Transport Security
- All agent-facing endpoints served over HTTPS; HTTP requests issued an unconditional permanent redirect to the HTTPS equivalent.
- TLS 1.2 (RFC 5246) negotiated at minimum; TLS 1.3 (RFC 8446) supported where possible. Protocol versions below TLS 1.2 not offered.
- Certificate valid: issued by a publicly trusted CA, unexpired, host-matching, and served with a complete intermediate chain.
- `Strict-Transport-Security` header (HSTS, RFC 6797) present on HTTPS responses with a `max-age` of at least 15552000 (180 days).

#### Availability
- Agent-facing endpoints sustain measured availability of at least 99.0% over the assessment window, returning success or well-formed error status codes rather than connection failures, timeouts, or 5xx responses under routine load.
- A single low-volume, conforming agent client is not blocked or rate-limited to the point of denial during ordinary, non-abusive request patterns.

#### Performance
- At the 75th percentile of agent-relevant loads, the origin meets "good" Core Web Vitals thresholds: Largest Contentful Paint (LCP) ≤ 2.5 s, Interaction to Next Paint (INP) ≤ 200 ms, Cumulative Layout Shift (CLS) ≤ 0.1.
- Primary agent-facing API/data endpoints return a complete response within a bounded time appropriate to an interactive agent loop; long-running operations are modeled asynchronously (see Level 3a) rather than held open past standard client timeouts.

#### Baseline Security Headers
- `X-Content-Type-Options: nosniff` emitted on agent-facing responses to prevent MIME-type sniffing of agent-consumed payloads.
- `Content-Security-Policy` (CSP Level 3) emitted on HTML responses; framing intent expressed via the CSP `frame-ancestors` directive, optionally with `X-Frame-Options` (RFC 7034) for legacy clients.
- `Referrer-Policy` present, declaring an explicit, non-default policy.
- Security headers are internally consistent and do not, in combination, prevent a conforming agent from retrieving or interpreting agent-facing resources.

#### Agent-Compatible CORS
- For browser-runtime agents, agent-facing endpoints return correct CORS headers (Fetch Living Standard): an `Access-Control-Allow-Origin` that resolves the requesting agent origin and, where credentialed or non-simple requests are required, valid `OPTIONS` preflight responses including `Access-Control-Allow-Methods` and `Access-Control-Allow-Headers`.
- CORS configuration does not reflect arbitrary origins together with `Access-Control-Allow-Credentials: true`; allowed origins are explicit or constrained.
- The CORS policy permits the request methods and headers the documented agent-facing surface requires.

#### No Hard Human-Interaction Walls
- Agent-facing endpoints are not gated behind a mandatory CAPTCHA, interactive proof-of-humanity, or unconditional JavaScript-challenge interstitial that a conforming, non-browser agent cannot pass.
- Where bot-management or challenge infrastructure is operated, a non-interactive path exists for verified agents — for example, honoring a cryptographically verifiable agent identity such as HTTP Message Signatures (RFC 9421) as applied by the Web Bot Auth architecture (`draft-meunier-web-bot-auth-architecture`) — rather than a hard human-only challenge.
- No blanket block, silent body substitution, or empty response is served to non-browser User-Agents as a categorical policy on the agent-facing surface.

### Verification

#### Automated Checks (all must pass)
- [ ] HTTPS reachable on agent-facing endpoints; HTTP redirects to HTTPS with a permanent status
- [ ] TLS handshake negotiates TLS 1.2 or higher; no sub-1.2 protocol offered
- [ ] Server certificate valid, unexpired, host-matching, and chain-complete
- [ ] `Strict-Transport-Security` present with `max-age` ≥ 15552000
- [ ] Sampled availability ≥ 99.0% across the assessment window with no connection-level failures on conforming low-volume requests
- [ ] Core Web Vitals at the 75th percentile within good thresholds (LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1)
- [ ] `X-Content-Type-Options: nosniff` present on agent-facing responses
- [ ] `Content-Security-Policy` and `Referrer-Policy` present and parseable on applicable responses
- [ ] CORS preflight (`OPTIONS`) and actual responses return an `Access-Control-Allow-Origin` resolving the test agent origin, without wildcard-origin-plus-credentials
- [ ] No mandatory CAPTCHA, proof-of-humanity, or unconditional JS-challenge returned to a conforming non-browser client on agent-facing endpoints
- [ ] A non-browser User-Agent receives the same substantive response as a browser User-Agent (no categorical block or body substitution)

#### Human Review (Knov reviewer)
- Confirm the certificate chain and TLS configuration reflect a current, non-deprecated cipher posture and that no mixed-content downgrade occurs on agent-facing pages.
- Review the availability sample for evidence that throttling or WAF rules are not selectively degrading conforming agent traffic.
- Inspect CSP and CORS policies for self-consistency and confirm they do not silently break agent retrieval despite passing presence checks.
- Exercise any bot-management challenge path manually and confirm a verified agent has a documented, non-interactive route through it.

#### Successful Verification
An origin attains Level 0 when every automated check passes against its agent-facing surface and the reviewer confirms that transport security, availability, performance, baseline headers, CORS, and the absence of hard human-interaction walls hold in practice rather than only in header presence. Failure of any automated check, or a reviewer finding that conforming agent traffic is categorically blocked or degraded, withholds Level 0 and blocks assessment of all higher levels.

---

## Level 1: Discoverable

### Definition
AI agents can find, navigate, and identify this entity. They know the entity exists, what it fundamentally is, and how to reach it. Discoverability is the foundation — without it, all higher levels are unreachable by agents.

### Requirements

#### Crawl and Navigation
- `robots.txt` present, valid, and includes explicit directives for major AI agents. Required user-agents: `GPTBot`, `OAI-SearchBot`, `ClaudeBot`, `PerplexityBot`, `anthropic-ai`, `GoogleBot-Extended`. Directives may allow or disallow — both are valid. Absence of explicit directives is a failure.
- `sitemap.xml` present and valid XML at `/sitemap.xml` or declared in `robots.txt`
- Canonical URLs declared on all primary pages via `<link rel="canonical">` or HTTP header
- Site renders meaningful content without JavaScript execution. Primary content pages must return substantive text content when fetched without a JavaScript engine.

#### Identity and Structured Data
- Schema.org structured data present on homepage as JSON-LD. Required type: `Organization`, `LocalBusiness`, `Person`, `MedicalBusiness`, or other appropriate Schema.org type. Required fields: `@type`, `name`, `description`, `url`. At minimum one `sameAs` link pointing to a verifiable third-party profile (LinkedIn, Google Business Profile, licensed professional registry, etc.).
- `llms.txt` present at domain root (`/llms.txt`). Must contain substantive content — minimum 200 words describing what the entity is, what it does, and how to navigate its content. Placeholder or empty files do not pass.
- `Content-Signal` directives present in HTTP response headers declaring AI crawl permissions (`ai-search`, `ai-input`, `ai-train`). Values may be allow or disallow — explicit declaration required.
- Link headers (RFC 8288) present on homepage pointing agents to key resources (sitemap, llms.txt, structured data).

#### Web Bot Auth (cryptographic crawler identity)
- Automated clients identifying themselves to this origin do so cryptographically using HTTP Message Signatures (RFC 9421) per the Web Bot Auth architecture (IETF draft `draft-meunier-web-bot-auth-architecture`).
- A public-key directory is published at `/.well-known/http-message-signatures-directory`, served with `Content-Type: application/http-message-signatures-directory+json` (IETF draft `draft-meunier-http-message-signatures-directory`).
- The directory presents a JSON Web Key Set (RFC 7517) containing the public verification key(s); each key's `keyid` equals the base64url-encoded JWK SHA-256 Thumbprint (RFC 7638; RFC 8037 Appendix A.3 for Ed25519).
- The origin can verify `Signature` and `Signature-Input` headers (RFC 9421) on signed requests, binding the signature to the key advertised in the directory.
- Signed requests carry a short validity window via the `created`/`expires` signature parameters; the directory resource remains cacheable for no longer than 24h (`Cache-Control: max-age=86400`).

#### Content Signals in robots.txt
- `robots.txt` declares usage preferences using a `Content-Signal` directive carrying the signal names `search`, `ai-input`, and `ai-train`, each set to `yes` or `no` (per contentsignals.org / Cloudflare Content Signals Policy).
- An omitted signal is interpreted as no stated preference for that usage, not as denial.
- The `Content-Signal` directive appears within the standard `robots.txt` grammar, scoped to a `User-agent` group, alongside conventional `Allow`/`Disallow` rules.
- `robots.txt` also carries explicit allow/deny rules for named AI crawler user-agents, so access control and usage preference are both expressed.
- `Content-Signal` values express preferences and carry no technical enforcement; they are advisory and paired with server-side controls where enforcement is required.

#### Standardized .well-known Discovery Surface
- Machine discovery endpoints are exposed under the `/.well-known/` prefix (RFC 8615); see the **.well-known Inventory** table below for canonical paths and governing standards.
- OAuth-protected agent APIs publish authorization-server metadata at `/.well-known/oauth-authorization-server` (RFC 8414) and protected-resource metadata at `/.well-known/oauth-protected-resource` (RFC 9728).
- An API publisher exposes an API catalog at `/.well-known/api-catalog` serialized as `application/linkset+json` (RFC 9727).
- An A2A-capable origin publishes its Agent Card at `/.well-known/agent-card.json`; the legacy `/.well-known/agent.json` location is retired or redirected.
- An MCP origin may publish a server card for pre-connection discovery; the path is not yet ratified (current proposed path `/.well-known/mcp/server-card.json` per draft SEP-2127, with the earlier `/.well-known/mcp.json` superseded) and is treated as provisional.

#### Agent Skills Discovery
- An origin advertising agent-executable skills publishes an index at `/.well-known/agent-skills/index.json` per the Cloudflare Agent Skills Discovery RFC (agentskills.io).
- The index carries a `$schema` identifier (discovery schema version 0.2.0, e.g. `https://schemas.agentskills.io/discovery/0.2.0/schema.json`) and a `skills` array whose entries each provide `name`, `type`, `description`, `url`, and `digest`.
- Each skill resolvable as type `skill-md` is retrievable at its declared `url` as a `SKILL.md` document whose YAML frontmatter declares at minimum `name` and `description`.

#### Typed Link Headers and Brand Identity
- Discovery relationships surfaced via the HTTP `Link` header follow the Web Linking framework (RFC 8288), using registered or well-formed extension relation types.
- The Agent Card may carry brand assets to support consistent agent-facing presentation, including `iconUrl` and a `provider` object with `organization` and `url`.

#### DNS-Based Discovery (Aspirational)
- An origin may additionally advertise agent endpoints through DNS-based discovery; this surface is pre-standard and is not relied upon as the sole discovery mechanism.
- Candidate mechanisms include DNS-AID using SVCB records (IETF draft `draft-mozleywilliams-dnsop-dnsaid`, building on RFC 9460), the AID `_agent` TXT-record convention, and the NANDA index; all are aspirational and paired with a `/.well-known/` surface.

#### Accuracy
- All declared identity information is accurate and verifiable
- Identity information is consistent across homepage, about page, and contact page

### .well-known Inventory

The following table enumerates the discovery resources an OARS-conformant origin should or may expose under the `/.well-known/` prefix (RFC 8615). Status reflects the governing standard's maturity as of this revision; see **Caveats**.

| Path | Purpose | Governing standard | Status |
|------|---------|--------------------|--------|
| `/.well-known/http-message-signatures-directory` | Public-key (JWKS) directory for Web Bot Auth crawler identity; `Content-Type: application/http-message-signatures-directory+json` | `draft-meunier-http-message-signatures-directory` | IETF draft |
| `/.well-known/oauth-authorization-server` | OAuth 2.0 Authorization Server Metadata | RFC 8414 | Ratified RFC |
| `/.well-known/oauth-protected-resource` | OAuth 2.0 Protected Resource Metadata | RFC 9728 | Ratified RFC |
| `/.well-known/api-catalog` | API catalog as a Linkset (`application/linkset+json`) | RFC 9727 | Ratified RFC |
| `/.well-known/agent-card.json` | A2A Agent Card (capabilities, skills, `iconUrl`, `provider`) | A2A Protocol (a2a-protocol.org) | Vendor/community spec |
| `/.well-known/agent.json` | Legacy A2A Agent Card location — retire or redirect to `agent-card.json` | A2A Protocol (legacy) | Deprecated location |
| `/.well-known/agent-skills/index.json` | Agent Skills discovery index (`$schema` + `skills[]` of `name,type,description,url,digest`; schema v0.2.0) | Cloudflare Agent Skills Discovery RFC (agentskills.io) | Community draft |
| `/.well-known/mcp/server-card.json` *(current)* · `/.well-known/mcp.json` *(superseded)* | MCP server card for pre-connection capability discovery | MCP SEP-2127 (under review; originally filed as SEP-1649) | Community draft (path unratified) |

Notes:
- The `keyid` of each key in the message-signatures directory equals the base64url-encoded JWK SHA-256 Thumbprint (RFC 7638; RFC 8037 Appendix A.3 for Ed25519). Request signatures use HTTP Message Signatures (RFC 9421).
- The MCP server-card path is **not yet ratified**; treat both candidate paths as provisional and serve transitional dual paths until the relevant MCP SEP is accepted.
- `llms.txt` and `llms-full.txt` live at site **root**, not under `/.well-known/`; Content Signals live inside `robots.txt`.
- DNS-based discovery (DNS-AID via SVCB records, AID, NANDA index) is **aspirational** and out of the required `/.well-known/` inventory.

### Verification

#### Automated Checks (all must pass)
- [ ] `robots.txt` fetchable and parseable, contains directives for all six named AI agents
- [ ] `sitemap.xml` fetchable and valid XML
- [ ] Homepage JSON-LD present and validates against Schema.org for declared type
- [ ] Required Schema.org fields present: `@type`, `name`, `description`, `url`, `sameAs` (minimum one)
- [ ] `/llms.txt` fetchable, returns `text/plain`, contains minimum 200 words
- [ ] `Content-Signal` headers present in homepage HTTP response
- [ ] Homepage returns >500 characters of meaningful text content with JavaScript disabled
- [ ] Canonical URL declared on homepage
- [ ] `robots.txt` contains a `Content-Signal` directive using only the names `search`, `ai-input`, `ai-train` with `yes`/`no` values, plus explicit allow/deny rules for named AI crawler user-agents
- [ ] `/.well-known/http-message-signatures-directory` responds 200 with `Content-Type application/http-message-signatures-directory+json`, a valid JWKS, and `keyid` equal to each key's base64url JWK SHA-256 thumbprint; `Cache-Control max-age` ≤ 86400
- [ ] `/.well-known/oauth-authorization-server` (RFC 8414) and `/.well-known/oauth-protected-resource` (RFC 9728) respond with valid metadata when OAuth is in use
- [ ] `/.well-known/api-catalog` responds 200 with `application/linkset+json` (RFC 9727) when an API catalog is published
- [ ] `/.well-known/agent-card.json` responds 200 with a parseable A2A Agent Card; `/.well-known/agent.json` is absent or redirects to it
- [ ] `/.well-known/agent-skills/index.json` responds 200 with a `$schema` field and a `skills` array of `{name,type,description,url,digest}`, each `skill-md` `url` resolving to a `SKILL.md` with `name`/`description` frontmatter
- [ ] HTTP `Link` header relation types are well-formed per RFC 8288

#### Human Review (Knov reviewer)
- Reviews Schema.org identity data for accuracy against publicly verifiable information
- Reviews `llms.txt` for quality — does it meaningfully help an agent understand this entity?
- Checks consistency of identity information across homepage, about, and contact pages
- Confirms published `Content-Signal` values match the organization's actual content-usage policy and legal stance
- Verifies the Web Bot Auth signing key in the directory corresponds to the private key actually used by the organization's outbound agents, and that key rotation is operationally managed
- Confirms the api-catalog and OAuth metadata endpoints do not leak internal-only or unauthenticated-restricted endpoints

#### Successful Verification
An AI agent navigating to the domain can unambiguously identify the entity — its name, type, what it does, and who it serves — within a single interaction using only the structured data and `llms.txt` as sources, and can discover the entity's machine-readable surfaces from the `/.well-known/` inventory alone. A Knov reviewer demonstrates this by prompting a live LLM with only the domain and confirming it returns accurate, complete identity information derived from the site's structured data.

---

## Level 2: Readable

### Definition
Agents can read, comprehend, and accurately extract meaningful information from the entity. Not just basic identity — the entity's full capabilities, offerings, knowledge, and context are accessible and correctly understood by AI systems. Level 2 divides into **2a Parseable** (format and negotiation — getting the content out cleanly) and **2b Interpretable** (structure, provenance, and freshness — understanding what the content means and how current it is).

### Level 2a: Parseable

Level 2a covers the format and negotiation properties that let an agent obtain a resource's *content* without reconstructing it from presentational HTML. These items are low-cost, high-leverage, and several are independently verifiable over HTTP.

#### Content Accessibility (baseline)
- Semantic HTML throughout primary pages — logical heading hierarchy (H1→H2→H3), meaningful alt text on all images, proper form labels, descriptive button text.
- Clean, valid accessibility tree. Primary pages pass accessibility tree validation with no critical structural failures. Agents use the same accessibility tree as screen readers.
- All primary content pages render substantive content without JavaScript dependency.

#### Markdown Content Negotiation (highest-ROI item)
- A request carrying `Accept: text/markdown` (content negotiation per RFC 9110 §12.5.1) returns a representation with `Content-Type: text/markdown` whose body is the resource's semantic content as Markdown, not HTML chrome.
- The Markdown representation preserves heading hierarchy, lists, tables, links, and code blocks so structure survives without an HTML parser.
- The HTML and Markdown representations share the same canonical URL; negotiation does not require a distinct URL. A parallel `.md`-suffixed or `/index.md` URL may be offered additionally for clients that cannot set request headers, advertised via `<link rel="alternate" type="text/markdown">` or a `Link:` header (RFC 8288).
- Responses negotiated by `Accept` emit `Vary: Accept` so intermediary caches do not cross-serve HTML and Markdown.
- Markdown delivery is the highest-ROI parseability measure: vendor benchmarks (Cloudflare, Feb 2026) report on the order of an ~80% token reduction versus the equivalent HTML page. This figure is vendor-sourced and directional, not independently ratified (see Caveats).

#### llms.txt and llms-full.txt at site root
- A site publishes `llms.txt` at the document root path `/llms.txt` — not under `/.well-known/` — formatted in Markdown per the llmstxt.org community convention, beginning with a single H1 name and a blockquote summary, with H2-delimited sections linking key resources.
- Large sites additionally publish `llms-full.txt` at the root, concatenating the full text of key pages into one Markdown file (a community extension of the base convention).
- Large or multi-product sites may publish hierarchical per-directory `llms.txt` files (e.g. `/docs/llms.txt`) scoped to that subtree.
- Links inside `llms.txt`/`llms-full.txt` resolve to retrievable resources and are not broken (4xx/5xx).

#### Locale and Language Declarations
- Every HTML representation declares its primary natural language via a `lang` attribute on the root `<html>` element.
- Responses declare language at the transport layer via a `Content-Language` response header (RFC 9110 §8.5; values per BCP 47 / RFC 5646).
- Sites serving multiple languages or regions declare variant relationships via `hreflang` (`<link rel="alternate" hreflang="...">` or equivalent sitemap/`Link`-header annotations), including an `hreflang="x-default"` entry. `hreflang` values are valid BCP 47 tags and consistent with `Content-Language`.

#### Document (PDF) Accessibility
- PDFs used as primary content are Tagged PDFs carrying a logical structure tree (Tagged PDF, PDF 1.4 / ISO 32000) so reading order and semantics are recoverable without visual heuristics, and conform to PDF/UA (ISO 14289) where practical.
- Every PDF contains a real, extractable text layer; scanned or image-only documents have OCR-derived text applied.
- Where a PDF is the authoritative form, an HTML or Markdown representation of the same content is also exposed for lower-cost machine retrieval.

### Level 2b: Interpretable

Level 2b raises a site from merely *parseable* to *interpretable*: an agent can identify the entities described, judge how fresh the content is, and trace where authoritative content lives.

#### Structured Knowledge (Schema.org / JSON-LD)
- Schema.org expanded beyond Organization identity to cover primary offerings. Every service or product offered has structured data with minimum fields: `name`, `description`, `provider`, and a pricing signal (exact price, price range, or `"priceSpecification": {"description": "contact for pricing"}` — no silent omissions). Acceptable offering types: `Service`, `Product`, `HealthcareService`, `LegalService`, `FinancialProduct`, `Course`, or domain-appropriate type.
- `FAQPage` schema implemented where FAQ content exists.
- `knowsAbout` property populated in the Organization schema listing the entity's authoritative topics (minimum 5 entries).
- `HowTo` or `SpecialAnnouncement` schema used where applicable to capture procedural or time-sensitive knowledge.
- Structured data is a corroborating signal, not a substitute for content quality. Raw JSON-LD alone yields only marginal retrieval-augmented-generation gains; **entity clarity** — clean semantic HTML, a single canonical `<h1>`, citable self-contained prose, and unambiguous entity naming — is the dominant signal and takes precedence where effort must be prioritized.
- Each structured entity carries a stable, dereferenceable `@id`, so the same offering is identifiable across pages and feeds.
- Structured data does not contradict the human-visible content. Every claim asserted in JSON-LD (price, availability, author, date) is also present in the rendered HTML.

#### Knowledge Declaration File
A dedicated knowledge declaration file present at `/oars.json` (and/or `/.well-known/oars.json`). This file is distinct from `llms.txt` (a content navigation aid) and from `AGENTS.md` (a coding-agent convention). It serves as the machine-readable entity profile for the OARS standard. Minimum content:
- Entity identity (name, domain, type, description)
- Audience declaration (who this entity serves, who it does not serve)
- Knowledge topics and areas of authority
- Primary offerings with structured descriptions
- Engagement information (how to begin working with this entity)
- OARS compliance declaration (current self-declared level, Knov.ai verification ID if verified)
- Freshness metadata (last updated date, next review date)

See `/schema/oars-manifest.json` for the full JSON Schema definition of this file.

#### Freshness Signals
- Resources expose a machine-readable last-modification time. At minimum the HTTP `Last-Modified` response header (RFC 9110 §8.8.2) is emitted and reflects the true last substantive change.
- Content-level freshness is human- and machine-legible: dated changelogs, `dateModified`/`datePublished` in JSON-LD, or visible publication and revision dates. Dates are accurate and not auto-bumped on every deploy.
- Where a sitemap is published, `<lastmod>` values are present and truthful, changing only when the underlying resource changes.
- Deprecated, superseded, or duplicate content is consolidated *away from* the canonical resource: a `rel="canonical"` link (RFC 6596) points to the preferred URL and retired URLs return a permanent redirect (HTTP 301) to their replacement. Per RFC 6596, the canonical target is not itself the source of a permanent redirect.

#### Machine-Readable Feeds
- Sites that publish a stream of dated content expose at least one machine-readable feed: Atom (RFC 4287), RSS 2.0, or JSON Feed 1.1.
- The feed is discoverable via an autodiscovery `<link rel="alternate">` in `<head>` carrying the correct media type (`application/atom+xml`, `application/rss+xml`, or `application/feed+json`).
- Feed entries carry a stable unique identifier and an accurate timestamp so consumers can detect new and changed items without re-fetching the full corpus.

#### Media Provenance (Aspirational)
> **Aspirational.** Not required to pass Level 2b. C2PA is an actively evolving consortium specification with limited deployment tooling.
- Published media carry C2PA Content Credentials: cryptographically signed provenance manifests recording origin, authorship, editing history, and any AI involvement, per the C2PA Technical Specification.
- Provenance manifests are embedded in the asset or resolvable via an external claim, and validate against a trusted signer such that tampering is detectable.

#### Accuracy and Consistency
- NAP consistency for local businesses: Name, Address, Phone identical across all pages and matching third-party profiles.
- No contradictory information between structured data and page content.
- Pricing signals honest and consistently declared — no unexplained gaps between structured data and page content.

### Verification

#### Automated Checks (all must pass)
- [ ] Semantic HTML: no critical heading hierarchy failures, all images have alt attributes, all forms have labels
- [ ] Accessibility tree: no critical structural failures
- [ ] Markdown content negotiation functional (Accept: text/markdown or `/index.md`) for minimum 80% of primary pages, with `Vary: Accept` on negotiated responses
- [ ] `/llms.txt` (and `/llms-full.txt` where applicable) present at root with resolvable links
- [ ] Root `<html lang>` present; `Content-Language` header present; `hreflang` valid and consistent where multi-locale
- [ ] All primary pages return >500 characters content with JavaScript disabled
- [ ] Schema.org structured data present on minimum 80% of primary offering pages, each entity carrying a stable `@id`
- [ ] `knowsAbout` property present with minimum 5 entries
- [ ] Knowledge declaration file present and validates against OARS manifest schema; `freshness.lastVerified` within 365 days
- [ ] `Last-Modified` and/or JSON-LD `dateModified` present and truthful on dated content
- [ ] At least one valid machine-readable feed (Atom/RSS/JSON Feed) with autodiscovery `<link>` where dated content is published

#### Human Review (Knov reviewer)
- Structured data accuracy review: Schema.org data accurately reflects actual offerings, pricing, and identity
- Knowledge file quality review: does the declaration file give an AI agent a complete, accurate picture of what this entity offers and knows?
- Agent simulation test: reviewer uses a live LLM to research the entity using only the site as a source, then verifies the LLM's understanding matches reality
- Contradiction scan: checks for inconsistencies between structured data and page content
- Freshness review: confirms dates reflect genuine change and deprecated content redirects toward canonical resources
- Pricing signal review: confirms all pricing information is honestly and completely declared

#### Successful Verification
A Knov reviewer demonstrates that prompting a live LLM to "research [entity] and describe their offerings, pricing, who they serve, and what they're expert in" — using only the site as a source — returns accurate, complete, and useful information with no significant gaps, errors, or contradictions, and that the agent can obtain clean Markdown content and judge content freshness from machine-readable signals. The site is genuinely comprehensible to an agent, not just technically crawlable.

---

## Level 3: Actionable

### Definition
Agents can take defined actions with the entity — not just read about it. At least one agent-callable capability is exposed, documented, and verified working. An agent can do something on behalf of a user: book, inquire, get a quote, query real-time information, or initiate a process. Level 3 divides into **3a Callable** (a documented, idempotent, error-structured API contract) and **3b Tool-Exposed** (MCP/Agent Skills with an enforced security baseline).

### Level 3a: Callable

Where Level 3 requires that at least one agent-callable capability exists, Level 3a raises the bar to *reliably* callable: the actionable surface is described by a documented, machine-readable contract an agent can consume autonomously, mutating operations are safe to retry, errors are structured and explicitly retryable, and long-running work resolves to definitive terminal states. The contract — not prose, not a human-facing UI — is the source of truth. OARS is agnostic between REST and GraphQL; the requirement is that *a* documented contract exists and is authoritative.

#### Machine-Readable Contract
- A documented, machine-readable API contract is published at a predictable, discoverable path. For HTTP/REST surfaces this is an OpenAPI document (OpenAPI 3.1.x or later); for GraphQL surfaces this is an introspectable, published schema (SDL). Agents derive callable operations, parameters, and response shapes from the contract without scraping prose.
- The contract location is declared in the knowledge declaration file (Level 2b) and/or the agent card. A conventional location (e.g. `/.well-known/openapi.json`, `/openapi.json`, or a path linked from the agent card) is used. There is no ratified IANA `.well-known` registration for OpenAPI; the path is a convention and is explicitly declared, not assumed.
- The contract is complete: every agent-callable operation present at runtime is described, and every operation in the contract is callable. Drift between contract and implementation is a conformance failure.
- For ordered multi-step interaction (e.g. create → confirm → finalize), a machine-readable workflow description is provided. The Arazzo Specification (an OpenAPI Initiative specification, v1.0.x or later) is the recommended format; where absent, multi-step ordering is documented in prose with explicit preconditions.

#### Idempotency
- All mutating operations (HTTP POST and PATCH, GraphQL mutations, and any operation non-idempotent under RFC 9110 method semantics) accept a client-supplied idempotency key. Agents retry aggressively on timeout and network failure; a retried request bearing the same key yields the same result as the original and produces no duplicate side effect.
- The mechanism is documented in the contract: the carrier of the key (the `Idempotency-Key` request header per the IETF HTTPAPI draft is recommended for HTTP surfaces; a documented field is acceptable for GraphQL), key format/uniqueness, the retention/expiration window, and behavior on key reuse with a differing payload.
- A replayed request matching a stored key returns the original recorded response (or a documented in-progress status) rather than re-executing. Methods already idempotent under RFC 9110 (PUT, DELETE, GET, HEAD) need not require a key but remain safe to retry.
- An idempotency-key conflict (same key, different request fingerprint) returns a documented typed error rather than silently mutating state.

#### Structured Errors
- All agent-facing error responses are machine-parseable. For HTTP surfaces the RFC 9457 Problem Details format (`application/problem+json`) is required; RFC 9457 obsoletes RFC 7807. No HTML error pages and no untyped 5xx bodies are returned to API calls.
- Each error carries explicit retryability semantics in a machine-readable field: whether the operation may be safely retried, and under what conditions. Transient failures are distinguishable from permanent ones.
- Time-sensitive retryable errors include backoff guidance (e.g. a `Retry-After` header).
- Every error state an operation can emit is enumerated in the contract with its code, `type`/identifier, and meaning.

#### Asynchronous and Long-Running Operations
- Operations that cannot complete synchronously use an explicit asynchronous pattern: the initiating call returns a job/operation identifier and a documented means of resolving status (a polling status endpoint and/or a status URL).
- The set of operation states is documented and includes explicit terminal states (e.g. `succeeded`, `failed`, `cancelled`) distinguishable from non-terminal states (e.g. `pending`, `running`).
- Polling guidance (interval or `Retry-After`) is provided; the terminal success state exposes or links to the operation result.
- Long-running operations are idempotent on initiation: re-submitting the same idempotency key does not start a second job.

#### Real-Time and Agent-Initiated Channels (Optional)
- Where real-time updates or server-initiated notifications are offered, the entity may expose WebSocket, Server-Sent Events, or webhook channels. These are optional and supplement, never replace, a pollable terminal-state contract.
- Any webhook or callback delivery is documented (payload schema, delivery guarantees, retry/redelivery) and is itself authenticated and verifiable (e.g. a signed payload).
- Real-time channels reuse the same structured error and event schemas as the synchronous contract.

#### Sessions and Capability Versioning
- Session and connection management is documented where state spans multiple calls. Stateless operation is acceptable and, where used, stated explicitly.
- The contract is explicitly versioned, and the version is discoverable. A documented compatibility and deprecation policy describes how breaking changes are introduced and how long prior versions remain callable; breaking changes ship under a new version.

### Level 3b: Tool-Exposed

Level 3b applies to entities that expose capabilities through a standardized tool interface. It layers a packaged-capability format on top of that interface and imposes a security baseline appropriate to a surface that, by definition, accepts instructions and tool definitions from outside the trust boundary.

#### Baseline Tool Interface — MCP Server
- The entity exposes its tool surface through a Model Context Protocol (MCP) server speaking the current MCP specification revision over a supported transport: `stdio` for local servers, Streamable HTTP for remote servers. The server exposes minimum one tool that agents can call. Acceptable minimum capabilities: appointment booking initiation, quote request submission, availability query, FAQ query with real-time response, product/service search, document retrieval, intake form submission.
- Every advertised tool carries a machine-readable input schema and MCP tool annotations declaring its behavioral contract (`readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`), so a client can reason about side effects before invocation.
- Tool names, titles, and descriptions are stable across server versions; any change to a tool's name, schema, or stated behavior increments the server version.
- Remote (HTTP-transport) MCP servers enforce authorization per the MCP Authorization specification. Unauthenticated requests to privileged tools are rejected at the server, not merely hidden from the client.
- An A2A (Agent-to-Agent) agent card is present at `/.well-known/agent-card.json` declaring entity identity, capabilities summary, supported protocols, and interaction terms.

#### Server Card — Discovery and Trust Posture
- The MCP server publishes a Server Card describing its identity (name, title, description, version, repository, `websiteUrl`), declared transports, and complete tool surface with annotations.
- The Server Card is retrievable as an MCP resource; HTTP-transport servers additionally expose it at a `.well-known` URI (per draft SEP-2127, current proposed path `/.well-known/mcp/server-card.json`, the earlier `/.well-known/mcp.json` superseded), so aggregators and clients can read the tool surface and policy posture without spawning or installing the server.
- The Server Card declares the server's privacy and security posture — authorization requirements, consent levels, data-retention windows — as machine-readable trust signals.

> **Status note.** The Server Card discovery mechanism is an in-progress MCP proposal (SEP-2127, draft; renumbered from the original SEP-1649). The exact `.well-known` path remains unsettled; implementers track the SEP for the ratified path, serve transitional dual paths, and treat the path as unstable.

#### Packaged Capabilities — Agent Skills
- Packaged capabilities are distributed as Agent Skills: a folder rooted at a `SKILL.md` whose YAML frontmatter declares at minimum a `name` and a `description` stating what the skill does and when to invoke it (the Agent Skills open specification, agentskills.io; released by Anthropic as an open standard on 2025-12-18).
- A Skill bundles its instructions, metadata, and optional resources (scripts, templates, reference material) within the skill folder, so the capability is self-contained and portable across conforming runtimes.
- Skill `description` fields are discriminating enough for an agent to select the correct skill without loading the full body.
- Bundled executable scripts run under the same sandboxing, consent, and least-privilege controls as any other tool. A Skill is not a path around tool authorization.

#### MCP Security
A tool-exposed surface accepts tool definitions and instructions from outside the trust boundary, so model-level alignment is insufficient as the primary control. The MCPTox benchmark (arXiv:2508.14925) — built on 45 live MCP servers, 353 authentic tools, and 1,312 malicious test cases — measured an average tool-poisoning attack-success rate of ~36.5% across 20 evaluated LLM agents, rising to ~72.8% for `o1-mini`, with the strongest model (`Claude-3.7-Sonnet`) still refusing fewer than 3% of poisoned calls. The load-bearing controls therefore live in the server and the client harness, not in the model.

**Server-side authorization and allowlisting**
- Authorization is enforced server-side on every inbound tool call. The server independently verifies the caller's identity and entitlement for the specific tool and does not treat a claimed scope, a prior session, or client-supplied state as sufficient.
- An MCP server does not accept a token that was not explicitly issued for that server (no token passthrough). The server validates token audience and, where downstream access is required, performs a token exchange for a credential scoped to the specific downstream service and user — per the MCP Security Best Practices and OAuth 2.0 security guidance (RFC 9700).
- The client maintains an explicit allowlist of servers and tools. Tools are denied by default and admitted only by deliberate operator decision, under a least-privilege, progressively-elevated scope model.
- Sessions are not used for authentication. Session identifiers are non-deterministic (CSPRNG-generated), bound to user-specific identity (e.g. `<user_id>:<session_id>`), and expirable.

**Human-in-the-loop and tool-description integrity**
- Tools annotated destructive or non-idempotent, and any action with irreversible external effect, require an explicit human-in-the-loop confirmation checkpoint that names the tool, the target, and the parameters before execution.
- One-click configuration of a local MCP server presents the exact command to be executed, without truncation, and obtains explicit consent before any code runs; local servers run sandboxed with minimal default privilege.
- Tool descriptions and schemas are treated as untrusted, attacker-controllable content. The runtime pins and integrity-checks the tool surface so a silent post-approval change (a rug pull) is detected and surfaced for re-review rather than auto-trusted.

**Named attack classes addressed:** tool poisoning, rug pulls, confused-deputy, token-passthrough abuse, and session hijacking. The threat model documents a verifiable mitigation for each.

### Authentication, Governance, and Documentation (applies across 3a/3b)

#### Authentication and Security
- OAuth 2.1 with PKCE implemented for any protected or state-changing actions. Read-only informational queries may be unauthenticated.
- Scoped token issuance — agents receive minimum necessary permissions for their declared purpose.
- All agent-facing endpoints use HTTPS with valid certificates (Level 0).
- Rate limiting enforced and documented. Rate-limit responses use HTTP 429 with `Retry-After`. Not enforcing rate limits is a security failure, not a leniency.
- No critical OWASP vulnerabilities on agent-facing endpoints (OWASP Top 10 current version).
- Coordinated disclosure policy published for security researchers.

#### Legal and Governance
- Privacy policy updated to explicitly address: what data is collected when agents interact, how agent-interaction data is handled, data retention for agent logs, and user rights over agent-generated data.
- Terms of service updated to explicitly address: permitted agent uses, prohibited agent behaviors, rate limits and fair use, and liability for agent-initiated actions.
- Agent interaction audit log maintained, retrievable for any 30-day period upon legitimate request.
- Human oversight boundaries declared in published agent instructions: specific actions that require human confirmation before execution are named and enforced technically.

#### Documentation
All exposed capabilities documented with: capability name, plain-language description, required and optional parameters with types, response format and example, all possible error states with codes and descriptions, and at least one complete example call and response.

### Verification

#### Automated Checks (all must pass)
- [ ] Machine-readable API contract fetchable at the declared path and parses as valid OpenAPI 3.1.x (or GraphQL introspection responds with valid SDL); contract location resolves from discovery alone
- [ ] Sampled operations declared in the contract are reachable at runtime (no contract/implementation drift)
- [ ] Mutating endpoints accept a client-supplied idempotency key; a repeated request with an identical key returns the original response and produces no duplicate side effect; key reuse with a differing payload returns a documented typed error
- [ ] Error responses returned as RFC 9457 `application/problem+json` (not HTML, not untyped 5xx) with a machine-readable retryable indicator; time-sensitive retryable errors include backoff guidance
- [ ] Long-running operations return a job/operation identifier, a resolvable status mechanism, explicit terminal states, and polling cadence guidance
- [ ] Contract is explicitly versioned and a deprecation/compatibility policy is published
- [ ] MCP server responds to capability discovery at the declared endpoint with minimum one callable, annotated tool
- [ ] Server Card fetchable (MCP resource and/or `.well-known` path) describing identity, transports, tool surface, and security posture
- [ ] Agent card fetchable and validates against the A2A specification
- [ ] Where Agent Skills are advertised, the discovery index resolves and each `SKILL.md` carries `name`/`description` frontmatter
- [ ] Auth endpoints functional and returning correct OAuth 2.1 responses; rate limiting active (429 with `Retry-After`); HTTPS enforced
- [ ] Agent instructions file fetchable and contains required sections

#### Human Review (Knov reviewer)
- **Contract-driven agent call:** Reviewer loads the contract from discovery, has an agent generate and execute a call, and confirms the contract alone sufficed.
- **Retry safety:** Reviewer triggers a mutating operation twice with the same idempotency key (including a forced-timeout retry) and confirms exactly one side effect and a recorded replay response.
- **Error semantics:** Reviewer induces auth, validation, rate-limit, and transient-backend failures and confirms each returns RFC 9457 Problem Details with a correct retryable indication.
- **Async lifecycle:** Reviewer initiates a long-running operation, polls to a terminal state, and confirms result retrieval using only documented states.
- **Live tool interaction:** Reviewer uses an actual AI agent to complete a full cycle through the MCP/tool surface: discovery → authentication → action execution → result received, logged and documented.
- **Security review:** Reviewer confirms server-side authorization on every tool call, no token passthrough, allowlist/HITL for destructive ops, and tool-description integrity; checks agent-facing endpoints for OWASP Top 10 critical vulnerabilities; sends malformed, out-of-scope, and unauthenticated requests to verify graceful handling.
- **Privacy/ToS and audit log:** confirms agent interactions are specifically addressed and a 30-day interaction log is retrievable.

#### Successful Verification
A Knov reviewer, using a real AI agent, consumes the entity's documented machine-readable contract and successfully executes both a synchronous mutating operation (with verified idempotent retry behavior) and, where applicable, a long-running operation through to a terminal state — and completes a full interaction through the MCP/tool surface from capability discovery through action completion. Errors are RFC 9457-structured with accurate retryability, no operation produces a duplicate side effect under retry, server-side authorization and human-in-the-loop controls hold under adversarial probing, and the verified capability is recorded in the Knov directory listing.

---

## Level 4: Transactable

### Definition
Agents can complete commercial transactions on behalf of users — purchases, bookings, contracts, or payments — with appropriate user authorization. Level 4 divides into **4a Priceable** (a machine-readable catalog with price, tax, currency, and availability) and **4b Payable** (authorized payment execution through a recognized agentic-commerce protocol). OARS is **protocol-neutral** at Level 4: a conforming implementation must implement *a* recognized protocol, not a specific one.

### Level 4a: Priceable

A site is Priceable when an agent can obtain, without human assistance and without scraping rendered HTML, a structured answer to "what is this, what does it cost right now, in what currency, with what tax, and is it available?" Level 4a is the catalog-and-pricing substrate a checkout flow is built on; it does not by itself require an agent to *complete* a purchase. Implementations conform to a recognized agentic-commerce profile — the OpenAI/ACP product feed or the UCP Catalog capability — rather than an ad hoc shape.

#### Machine-Readable Catalog
- A machine-readable product/service catalog is published in a structured format (JSON, XML, CSV, or TSV) retrievable and parseable without scraping rendered HTML.
- Each catalog item carries a stable unique identifier, a title, a description, a canonical product URL, and at least one image URL, plus the attributes an agent needs to disambiguate selection (brand, condition, and where applicable variant axes such as size/color with a shared group/variant identifier).
- The catalog conforms to a recognized agentic-commerce feed schema — the OpenAI/ACP product feed or the UCP Catalog capability — rather than an undocumented proprietary shape. Where a UCP Catalog capability is offered, products and variants are retrievable in real time by identifier lookup and by search, and the capability is declared in the merchant's `/.well-known/ucp` profile.

#### Real-Time Price
- Every priceable item exposes a current price as a numeric amount paired with an explicit ISO 4217 currency code.
- Sale/promotional pricing, when applicable, is represented in a distinct field (e.g. `sale_price` / `list_price`).
- Catalog prices are refreshed frequently enough to remain accurate. The merchant is the source of truth for price; catalog prices are advisory, and the checkout session is authoritative for the final amount.
- Monetary amounts in transactional/checkout responses are expressed as integers in the currency's ISO 4217 minor unit, avoiding floating-point representation.

#### Tax Calculation
- The merchant computes tax server-side, derived from the buyer-supplied fulfillment/shipping address at checkout time, not hard-coded into the catalog feed (the ACP product feed itself carries no tax fields).
- The checkout/cart response returns an itemized totals breakdown including, at minimum, a subtotal, a separately stated `tax` line, `fulfillment`, any discounts, and a grand `total`.
- Totals are internally consistent: `total == items_base_amount − items_discount − discount + fulfillment + tax + fee`.
- The merchant remains the system of record for tax, orders, payment, and compliance; the agent surface does not compute or assume tax on the merchant's behalf.

#### Multi-Currency
- Currency is always stated explicitly alongside every amount using an ISO 4217 code; an agent never infers currency from locale.
- Where the catalog is offered in more than one market, the target market(s) and store country are declared using ISO 3166-1 alpha-2 codes.
- Minor-unit handling follows ISO 4217 for the stated currency (zero-decimal currencies are not assumed to carry cents).

#### Availability and Inventory
- Every catalog item exposes a machine-readable availability state from a defined enumeration (e.g. `in_stock`, `out_of_stock`, `pre_order`, `backorder`, `unknown`), with an availability date for `pre_order`/`backorder` items where the schema provides for it.
- Availability is kept current through frequent feed refresh and/or real-time catalog lookup. Catalog availability is advisory; the checkout session is authoritative and re-validates availability before the order is committed.

> **Status note.** ACP and the OpenAI product feed are open but vendor-maintained specifications (OpenAI/Stripe, Apache-2.0, beta; spec `2026-04-17`). UCP is a multi-party industry specification publishing dated, stable releases (latest catalog spec `http://ucp.dev/2026-04-08/specification/catalog/`; founding co-developers Google, Shopify, Etsy, Target, and Wayfair, with Salesforce joining the Tech Council 2026-04-24); its profiles use a dated `YYYY-MM-DD` version and its `/.well-known/ucp` URI is not, as of this writing, IANA-registered. Only the underlying code systems — ISO 4217 (currency/minor units) and ISO 3166-1 alpha-2 (country codes) — are formally ratified standards. Conformance targets may change as these specifications stabilize (see Caveats).

### Level 4b: Payable

Level 4b extends the agent surface from quotable offers to authorized payment execution: an agent acting on behalf of a verified principal can complete a purchase. The merchant remains the merchant of record, and the agent never handles raw card data.

#### Commerce Protocol Conformance (one compliant protocol required)
- The agent surface executes authorized payments through at least one recognized agentic-commerce protocol. Conformance to any one recognized stack satisfies this level; no specific protocol is mandated.
- A conforming implementation declares, in machine-readable form, which commerce protocol(s) and version(s) it supports, so a counterparty can negotiate a common protocol before any payment is initiated.
- Recognized protocols and their published discovery surfaces:
  - **ACP** (Agentic Commerce Protocol, OpenAI/Stripe) — exposes a Delegate Payment surface and an Agentic Checkout surface; settles via single-use Shared Payment Tokens.
  - **UCP** (Universal Commerce Protocol, Google/Shopify and partners) — advertises capabilities at `/.well-known/ucp`.
  - **AP2** (Agent Payments Protocol, Google) — carries signed Intent, Cart, and Payment Mandates.
  - **x402** (HTTP 402 micropayments/stablecoins) — payable resources are discovered through a facilitator-hosted REST API (the "Bazaar", `GET /discovery/resources`, formalized in x402 v2), **not** a `.well-known` path.
- Where the chosen protocol defines a discovery document at a well-known path, that document is served over HTTPS, is publicly resolvable without authentication, and enumerates supported capabilities, endpoints, accepted payment handlers, and protocol version.
- Protocol selection follows a server-selects (intersection) negotiation: the seller computes the intersection of its declared capabilities with those declared by the agent and returns the negotiated active capability set. The agent does not assume a capability the seller has not declared.

#### Payment Credential Handling and the PCI-DSS Boundary
- The agent does not receive, transmit, store, or process a raw Primary Account Number (PAN) or other raw cardholder data. Card data is exchanged only as a scoped, single-use payment token (e.g. an ACP Shared Payment Token) or an equivalent tokenized payment-handler credential.
- The merchant (or its PSP) remains the merchant of record and retains the PCI-DSS obligation for cardholder data. The agent operates outside the cardholder data environment and does not expand the merchant's PCI-DSS scope.
- Payment tokens issued to an agent are scoped to a specific merchant and cart total, single-use, and time-limited. Expiration is handled gracefully; tokens are not reused.
- For on-chain settlement (x402 / stablecoin), the agent presents a signed authorization (e.g. an ERC-3009 `transferWithAuthorization` or equivalent gasless authorization) bounded by an explicit value and a `validAfter`/`validBefore` window; it does not hold custody of, or expose, the principal's private spending keys beyond the scoped authorization.

#### Consent Proof and Authorization Mandates
- Every executed payment is backed by a verifiable, tamper-evident proof of user consent cryptographically bound to the specific transaction (cart contents and charged amount), not merely to a session.
- For human-present purchases, the consent proof binds the user's authorization to the exact, final cart. For human-not-present (delegated) purchases, it encodes the scope and constraints under which the agent was authorized (item criteria, spend ceiling, validity window), and the executed charge falls within that declared scope. Maximum transaction value is configurable per authorization; agents cannot exceed the granted scope.
- Where the protocol represents consent as signed mandates (e.g. AP2 Intent, Cart, and Payment Mandates), each mandate is carried as a W3C Verifiable Credential (VC Data Model 2.0) signed by a key the authorizing party controls, and is independently verifiable by the merchant, payment network, and any dispute resolver.
- The consent proof is retained as a non-repudiable audit record linking the authorizing human, the acting agent, and the executed payment.

#### Agent Wallet Identity and Delegated Authority
- An agent that initiates payment presents a verifiable identity (e.g. a Decentralized Identifier or equivalent agent key) and a verifiable delegation tying its authority to a known, KYC-verified human or organizational principal.
- KYC/identity assurance is inherited from the verified principal to the agent through a signed delegation credential; the agent does not assert payment authority it cannot prove was delegated. The delegation is revocable and time-bounded.
- The chain of authority — verified principal → authorized agent → executed transaction — is reconstructable from the credentials attached to the transaction.

#### Recurring, Held, and B2B Authorization
- Subscription/recurring authorization is represented as an explicit mandate distinct from a one-time purchase, declaring cadence, per-charge ceiling, and total/expiry bounds; each recurring charge is attributable to that standing authorization and within its declared limits.
- Escrow/held-payment flows express the hold and the release as separate authorized events, each bounded by an explicit validity window, and do not settle outside the authorized window.
- B2B/purchase-order flows, where accepted, are supported as a distinct payment instrument carrying the purchase-order reference and approval authority as part of the consent proof, rather than collapsing into a card-style charge.

#### Trust, Safety, and Receipts
- No agent-blocking authentication (CAPTCHA, SMS verification, knowledge-based authentication) present in documented agent transaction paths.
- Agent identity verification implemented (Web Bot Auth / RFC 9421 HTTP Message Signatures or equivalent) so the entity can distinguish a legitimate, authorized agent from a malicious bot without blocking legitimate agent traffic.
- A complete transaction record is maintained for every agent-initiated transaction: agent identity/signature, user authorization record, authorization scope at time of transaction, transaction details, timestamp, and outcome. Receipts are accessible to the authorizing user through their account.
- Cancellation, refund, and modification policy is machine-readable; a dispute-initiation mechanism is available to users for agent-initiated transactions.

> **Protocol status note.** ACP (OpenAI/Stripe) and UCP (Google/Shopify) are vendor/industry-published open specifications; AP2 (Google) is a vendor spec extending A2A with FIDO Alliance standardization underway; x402 is community-governed (donated to the Linux Foundation x402 Foundation, 2026-04-02; gasless authorization via ERC-3009). The W3C Verifiable Credentials Data Model 2.0 (Recommendation, 2025-05-15) and RFC 9110 §15.5.3 (which reserves HTTP 402) are ratified; PCI-DSS v4.0.1 is governed by the PCI Security Standards Council. None of the four commerce protocols is itself an IETF RFC; OARS recognizes any one of them as a compliant path (see Caveats).

### Verification

#### Automated Checks (all must pass)
- [ ] Machine-readable catalog endpoint/feed present and parseable; each item has a unique id, title, description, product URL, and image URL
- [ ] Each priceable item exposes a price with a numeric amount and valid ISO 4217 currency code; `sale_price`/`list_price` valid when on promotion; transactional amounts are integers in the currency minor unit
- [ ] Checkout/cart response returns a totals breakdown with a separately stated `tax` line; tax responds to a change of fulfillment/shipping address (server-side calculation); totals internally consistent
- [ ] `target_countries`/`store_country` are valid ISO 3166-1 alpha-2 codes where present
- [ ] Each item exposes an availability value within the allowed enumeration; `availability_date` present for `pre_order`/`backorder`
- [ ] At least one recognized commerce protocol declared in machine-readable form with its version, and its discovery surface (e.g. `/.well-known/ucp`, ACP checkout, or the x402 Bazaar `GET /discovery/resources`) responds correctly
- [ ] No CAPTCHA or human-only auth present in declared agent transaction paths
- [ ] Agent identity verification (RFC 9421 or equivalent) present on transaction endpoints
- [ ] Transaction status endpoint returns structured data

#### Human Review (Knov reviewer)
- Confirm the catalog conforms to a recognized agentic-commerce schema and that catalog price/availability match the live storefront and source-of-truth system.
- Verify tax is genuinely computed server-side against the buyer's address (compare totals for two addresses in different tax jurisdictions).
- **End-to-end transaction test:** complete an actual or fully documented simulated agent transaction from discovery through payment authorization through confirmation through receipt. For high-value/regulated entities, a complete documented test environment demonstrating the full flow is required.
- Authorization scope review: is the delegation model clear, technically enforced, auditable, and appropriately scoped (spend ceiling, validity window, recurring/escrow/B2B as applicable)?
- Consent-proof review: confirm each payment is backed by a tamper-evident proof cryptographically bound to the specific cart and amount, retained as a non-repudiable record.
- PCI/credential review: confirm the agent never receives raw card data and the merchant remains merchant of record.
- Fraud control review: entity demonstrates its systems distinguish authorized agents (passing RFC 9421 signatures or equivalent) from unsigned bot traffic.
- Dispute handling and audit-trail verification.

#### Successful Verification
A Knov reviewer documents a complete agent transaction flow from initiation through completion through receipt, demonstrating that a user's AI agent can execute a commercial action — within user-authorized scope — without human intervention, through a recognized commerce protocol, with the merchant as merchant of record and the agent never touching raw card data. The transaction is correctly attributed, reversible within stated policies, backed by a verifiable consent proof, and auditable. The entity's commerce implementation is documented in the Knov directory listing with supported protocols and transaction capabilities.

---

## Level 5: Operable

### Definition
AI operates workflows inside the entity's business. The entity runs as a documented human-AI hybrid system — agents handle defined operational processes end to end, with formally defined and technically enforced human oversight boundaries. This is machine-operability in the fullest sense: the business can be substantially operated with AI, not just interacted with from outside.

### Requirements

#### Internal Agent Infrastructure
- At minimum one core business workflow substantially operated by AI agents end-to-end. Acceptable workflows: customer inquiry intake and routing, appointment scheduling and confirmation, quote generation and delivery, order processing and fulfillment, invoice generation, support ticket triage and resolution, lead qualification, document processing, or equivalent operational function with comparable scope.
- Internal MCP server or equivalent infrastructure exposing core internal business systems (CRM, scheduling, inventory, communication, knowledge base) to authorized internal agents.
- Agent-accessible internal knowledge base: the entity's operational knowledge, SOPs, decision criteria, and escalation rules are structured and accessible to their operating agents.
- Deployed agent framework or platform managing the internal agent workflows (LangChain, LlamaIndex, CrewAI, Salesforce Agentforce, Microsoft Copilot Studio, custom orchestration — platform agnostic, but documented and operational).

#### Agent Identity and Credentialing (internal agents)
- Every internal agent holds a unique, first-class identity in the corporate directory or identity provider — distinct from any human user account and from generic service principals — with a stable, non-reassignable identifier (e.g. a Microsoft Entra Agent ID object ID / app ID, or a W3C Decentralized Identifier).
- Each agent identity declares a human-accountable sponsor (owner) and a human-readable display name, so every autonomous action traces to a responsible party.
- Agent identities authenticate to downstream systems using their own credentials and are subject to conditional-access, lifecycle, and de-provisioning controls equivalent to those governing workforce identities; shared or hard-coded credentials across agents are absent. Credentials are short-lived and rotatable; an inventory of active agent identities, sponsors, and granted scopes is maintained and reviewable.
- *Aspirational:* authorization granted to an agent is expressed as a scoped, verifiable delegation chain — a W3C Verifiable Credential rather than (or in addition to) an opaque OAuth token — recording who authorized the agent, the exact scope, applicable constraints, and an expiry, independently verifiable by relying parties, composable, and revocable.

#### Agent Observability (OpenTelemetry GenAI)
- Agent execution is instrumented with distributed tracing using the OpenTelemetry GenAI semantic conventions, emitting agent-invocation spans (`invoke_agent`) and, where applicable, agent-creation spans (`create_agent`) carrying the agent identifier (`gen_ai.agent.id`).
- Traces propagate context across agent and tool boundaries so a single end-to-end trace captures multi-step reasoning, tool calls, and hand-offs between sub-agents — no orphaned or black-box steps.
- Per-step token consumption and operation latency are recorded as telemetry (e.g. `gen_ai.client.token.usage`, `gen_ai.client.operation.duration`), enabling cost and performance attribution at the step level.
- Effectful agent actions are written to a tamper-evident audit log correlated by trace and agent identity, retained per the data-governance policy.

#### Agent Memory, State, and Versioning
- Agent memory and persisted state have a declared storage location, retention policy, and access-control boundary; persisted state is scoped to the owning agent identity and not silently shared across agents or tenants.
- Each agent has a versioned, machine-identifiable release, and a defined rollback procedure exists to revert to a prior known-good version without data loss or orphaned in-flight state.
- Agent configuration, prompts, tool grants, and model bindings are version-controlled, so any deployed agent version is reproducible and auditable.

#### Model Governance
- A machine-readable policy declares which models (and versions) are authorized for which decision classes, including classes that require human review or are prohibited from full automation.
- Agents are bound to authorized models per the policy; attempts to invoke an unauthorized model for a decision class are blocked and logged. Model-to-decision-class authorizations are reviewable and carry an effective date and owner.

#### Multi-Agent Coordination
- Where agents coordinate across vendor or framework boundaries, they interoperate via an open agent-to-agent protocol (e.g. A2A) and publish a discoverable capability descriptor at `/.well-known/agent-card.json` (RFC 8615).
- Inter-agent calls authenticate both the calling and receiving agent identities and carry sufficient context to attribute the action to an originating principal.
- *Aspirational:* agents are discoverable through a federated agent registry or index (e.g. a NANDA-style index) that validates cryptographic signatures on registry entries, enabling cross-organization discovery without a single point of control.

#### Reliability: SLAs, Error Budgets, and Cost Circuit Breakers
- Each agent or agent service publishes a machine-readable SLA defining target availability, latency, and error objectives, with an associated error budget that governs change velocity when exhausted.
- Token-budget circuit breakers enforce per-request, per-session, and per-key ceilings on token and cost consumption; when an agent exceeds a defined spend velocity, its access is automatically tripped to halt runaway loops (denial-of-wallet protection).
- Circuit-breaker trips and SLA/error-budget breaches emit alerts and are recorded in the audit and observability pipeline, and a documented kill-switch can suspend an agent identity immediately.

#### Human-AI Governance
- Human oversight boundaries formally documented and technically enforced. Specific decision categories requiring human approval are named explicitly, technically blocked from autonomous agent completion, and logged when triggered.
- Agent handoff protocols defined and functional: when agents hand off to humans (triggers, conditions, handoff data format) and how humans hand back to agents (re-entry conditions, state transfer).
- Human review of consequential AI decisions tracked: for any AI-made decision above defined thresholds (financial, customer-facing, legal, or otherwise defined by the entity), a human review record exists and is retrievable.
- Staff documentation: team members who work alongside AI agents have documented operational knowledge of what the agents do and don't do, their error patterns, how to identify a mistake, and how to intervene and correct.

#### Operational Visibility
- Operational metrics tracked and reportable: at minimum — percentage of workflow handled by agents vs. humans, agent error rate, human intervention rate, and average processing time. Accessible to Knov.ai during verification.
- Agent activity log maintained with sufficient detail to reconstruct any agent action for audit: action taken, input received, decision made, output produced, timestamp, agent identity.
- Incident response procedure documented and demonstrated: detection, containment, correction, and user notification when an agent makes an error, takes a wrong action, or fails to complete a task.

#### Data Governance
- Data governance policy explicitly addresses agent-generated data, agent-accessed data, and agent logs — retention periods, access controls, and deletion procedures.
- PII handling in agent workflows complies with applicable regulations: GDPR, CCPA, HIPAA, or other jurisdiction-specific requirements, documented.
- Data retention policy covers agent interaction logs with defined retention periods and deletion procedures.

### Verification

#### Automated Checks (all must pass)
- [ ] Internal agent infrastructure: minimum one operational workflow documented with evidence of continuous operation (logs covering minimum 30 days prior to verification)
- [ ] Each internal agent resolves to a unique directory/DID identifier distinct from human and generic service-principal accounts, declaring a human sponsor and display name; no shared/hard-coded credentials reused across agents
- [ ] Agent execution emits OpenTelemetry GenAI `invoke_agent` spans carrying `gen_ai.agent.id`; a single trace spans agent-to-tool and agent-to-agent boundaries; per-step token/latency telemetry present
- [ ] Effectful actions written to a tamper-evident audit log correlated by trace and agent identity; activity log retrievable from a minimum 30-day period
- [ ] Each deployed agent reports a machine-identifiable version and a documented rollback procedure
- [ ] A machine-readable model-governance policy maps authorized models/versions to decision classes; invoking an unauthorized model is blocked and logged
- [ ] Each agent service publishes a machine-readable SLA and error budget; token-budget circuit breakers enforce per-request/session/key ceilings; a kill-switch is documented and reachable
- [ ] Operational metrics endpoint or report accessible to Knov.ai; incident response contact and procedure accessible

#### Human Review (Knov reviewer — structured half-day review)
- **Operational demonstration:** entity demonstrates (live or via documented evidence) an AI agent handling a real operational workflow from initiation to completion, including decision points, handoff triggers, and output.
- Governance documentation review: human oversight boundaries, handoff protocols, and staff documentation reviewed for completeness — real and operational, not paper-only.
- Identity and accountability review: confirm unique, non-reassignable agent identities with named human owners, and that de-provisioning removes downstream access.
- Observability review: inspect a sample of end-to-end traces for intact cross-agent context propagation and tamper-evident, complete audit logs.
- Reliability review: assess SLA targets, error-budget policy, and circuit-breaker thresholds against real cost and reliability exposure; rehearse or review an actual rollback/kill-switch.
- Incident response review: entity demonstrates or documents at least one invocation of the incident response procedure.
- Data governance review and staff interview (minimum one person actively working alongside the AI systems).
- Metrics review: confirm AI is genuinely handling a meaningful portion of the declared workflow — not just connected but dormant.

#### Successful Verification
A Knov reviewer has observed or reviewed documented evidence of AI agents handling a real operational workflow within the entity's business — not a demo, but production operation. Agent identities are unique and accountable, execution is observable end-to-end, human oversight boundaries are technically enforced, and reliability controls (SLAs, circuit breakers, kill-switch) are real. The entity can demonstrate they know what their agents are doing, can audit what they've done, can intervene when needed, and have governance documentation that would satisfy a basic operational audit. Verification at Level 5 results in the entity being listed in the Knov directory as a verified machine-operable organization.

---

## Frontier (Aspirational)

> **Status: Aspirational.** Every item in this section is in research, draft, or proposal form and appears in **no shipping agent-readiness standard**. These items are **optional**, provided to signal direction of travel. An implementation is not marked non-conformant for omitting any Frontier item, and does not claim OARS conformance *on the basis of* implementing them. Treat all references in this section as subject to change.

### Machine-Readable Agent-Behavioral Contracts
- An origin may publish a machine-readable behavioral contract declaring the rate expectations it sets for agents (request ceilings, concurrency limits, backoff guidance), in a structured, parseable format rather than prose.
- The contract enumerates allowed actions and explicitly prohibited actions for autonomous clients, keyed to identifiable scopes or endpoints, so an agent can determine permitted behavior before acting.
- The contract declares an escalation path (a contact, endpoint, or procedure) for an agent that encounters ambiguity or a prohibited-action boundary.
- Behavioral contracts are advisory and complementary to — not a replacement for — enforced controls (robots exclusion per RFC 9309, authentication, rate limiting). No part of OARS depends on agent goodwill for enforcement.

### Cryptographically Verifiable Delegation Chains
- An agent acting on behalf of a principal may present a verifiable delegation chain binding the agent's identity to the authorizing principal, verifiable by a relying party without out-of-band contact with the principal.
- Identities may be expressed as W3C Decentralized Identifiers (DIDs) and authorizations as W3C Verifiable Credentials, enabling cryptographic verification of issuer, subject, and validity.
- Where a credential carries more attributes than a transaction requires, the holder may use selective disclosure or zero-knowledge proof mechanisms (e.g. BBS-family signatures) to reveal only what the verifier needs.
- *Note:* DID Core v1.0 and VC Data Model v2.0 are ratified W3C Recommendations, but agent delegation-chain profiles built on them are not yet standardized; zero-knowledge selective-disclosure mechanisms remain non-normative and implementation-dependent.

### Content Provenance (C2PA)
- An origin serving agent-generated or agent-modified media may attach C2PA Content Credentials — cryptographically signed provenance manifests recording asset creation, authorship, and edit history.
- Relying parties may validate manifest signatures and trust assertions against a declared trust model before treating provenance claims as authoritative.
- *Note:* C2PA is a consortium specification (Content Credentials v2.x). Its native W3C Verifiable Credentials binding existed only in C2PA v1.x and was **removed in v2.0** — a current v2.x manifest does not embed W3C VCs. Its ISO profile, ISO/DIS 22144, is a Draft International Standard and not yet finalized.

### Machine-Readable SLAs and Error Budgets
- An origin exposing agent-facing endpoints may publish a machine-readable service-level declaration covering availability targets, latency targets, and an error budget, in a structured format an agent can parse and reason over.
- The declaration aligns error reporting with RFC 9457 (Problem Details) so budget-relevant failures are machine-classifiable.
- *Note:* no standardized machine-readable SLA/error-budget schema for agent endpoints exists in any readiness standard.

### Declared Liability and Accountability Model
- An origin participating in agent-initiated transactions may publish a declared accountability model identifying which party bears responsibility for a disputed, fraudulent, or erroneous agent action.
- The model may adopt the authorization / authenticity / accountability triad seeded by the Agent Payments Protocol (AP2): *authorization* (proof the principal granted specific authority), *authenticity* (proof the agent's request reflects the principal's true intent), and *accountability* (an attributable, non-repudiable audit trail).
- Accountability rests on tamper-evident, cryptographically signed records (e.g. AP2-style verifiable mandates) rather than on log assertions alone.
- *Note:* AP2 is a vendor/community specification (Google and partners), not a ratified standard.

### Trust Hierarchies and Reputation
- An agent ecosystem may participate in a trust-and-discovery layer mapping agent identities to signed, schema-validated capability and operator metadata, enabling relying parties to assess provenance and reputation before transacting.
- Such metadata may follow the NANDA AgentFacts model: signed JSON-LD documents describing an agent's capabilities, operator, and secure connection details, with claims signed by issuers for tamper-resistance.
- Reputation signals (liveness, telemetry, scoring) may inform, but do not solely substitute for, cryptographic verification of identity and authorization.
- *Note:* NANDA / AgentFacts is an academic/community draft (MIT Media Lab), not a ratified standard.

---

## Caveats

This section qualifies the maturity, stability, and evidentiary basis of the requirements elsewhere in OARS. It is **normative** with respect to how conformance claims are scoped and dated.

### Ratified vs. Draft Status
- Implementers distinguish ratified IETF RFCs from drafts and treat draft-derived requirements as subject to change. The following referenced RFCs are **finalized** (Internet Standards Track):
  - **RFC 8615** — Well-Known URIs
  - **RFC 8414** — OAuth 2.0 Authorization Server Metadata
  - **RFC 9728** — OAuth 2.0 Protected Resource Metadata
  - **RFC 9727** — `/.well-known/api-catalog`
  - **RFC 9309** — Robots Exclusion Protocol
  - **RFC 8288** — Web Linking
  - **RFC 9457** — Problem Details for HTTP APIs
  - **RFC 9421** — HTTP Message Signatures (the basis for Web Bot Auth)
- The following are **NOT ratified** and remain in draft or proposal form: **MCP Server Cards** (SEP-2127, originally filed as SEP-1649), **A2A agent-card** discovery paths, the **Agent Skills** specification (community draft, agentskills.io; released by Anthropic as an open standard 2025-12-18), and **Web Bot Auth** (IETF Internet-Drafts, `draft-meunier-*`, building on the ratified RFC 9421).
- A conformance claim against a draft-derived item is scoped to a **pinned draft revision**; an origin does not claim conformance to a moving draft without naming the revision it implements.

### Version Pinning and Transitional Dual Paths
- Where a draft has changed its canonical `.well-known` path, an origin serves both the legacy and current paths during the transition window and does not assume a single canonical location.
- **MCP server discovery:** account for both proposed locations — `/.well-known/mcp/server-card.json` (current, per SEP-2127) and the superseded `/.well-known/mcp.json` — as placement is unsettled.
- **A2A agent-card discovery:** serve both the legacy `/.well-known/agent.json` and the current `/.well-known/agent-card.json` until clients converge.
- Discovery documents carry an explicit protocol/spec version field so consumers can detect and adapt to the revision being served.

### Commerce Protocol Flux
- Agent commerce protocols are in active flux with no ratified standard. OARS requires that an origin supporting agent-initiated commerce declare and implement **a** protocol with a verifiable authorization and audit model — it does **not** mandate a specific named protocol (ACP, UCP, AP2, x402, or a successor).
- The required properties are the accountability guarantees, not the brand. An origin does not hard-code assumptions that any single commerce protocol becomes the durable standard.

### Effectiveness Hedges and Directional Figures
- Low-cost surface signals such as `llms.txt` and JSON-LD structured data are recommended because their cost is low, but their effectiveness is **not guaranteed**: no major model provider has committed to honoring `llms.txt`, and structured-data consumption by agents is inconsistent. Treat these as low-cost hedges, not load-bearing guarantees.
- Adoption and traffic figures cited anywhere in OARS (e.g. the ~80% markdown token-reduction figure) are primarily **Cloudflare-sourced and directional** — trend and order of magnitude, not audited ecosystem-wide measurement.
- Because the underlying drafts and ecosystem are moving, conformance claims are **dated** and re-evaluated against then-current specification revisions.

---

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 1.2 | 6-5-2026 | Added the optional `identity.sameAs` field to the oars.json schema — an array of URLs to public profiles or records (social, code repositories, business registries, directory listings) that externally verify the entity. Supports the Level 1 requirement that schema.org identity be verifiable against publicly verifiable information. Backward compatible: the field is optional and additive. |
| 1.1 | 5-31-2026 | Added **Level 0: Reachable** prerequisite tier. Split Levels 2/3/4 into sub-levels (2a Parseable / 2b Interpretable, 3a Callable / 3b Tool-Exposed, 4a Priceable / 4b Payable). Renamed Level 3 **Interactable → Actionable**. Added Web Bot Auth, Content Signals, the full `.well-known` inventory, Agent Skills discovery, Markdown content negotiation, machine-readable feeds, idempotency/RFC 9457/async API conventions, MCP Server Cards + security controls, the ACP/UCP/AP2/x402 commerce stack, agent identity/observability/SLAs at Level 5, and new **Frontier** and **Caveats** sections. |
| 1.0 | 5-28-2026 | Initial specification draft |

---

## Governance

OARS is published and maintained by Knov.ai. The standard is open — any entity may implement it without license or fee. Changes to the specification are proposed publicly, reviewed with community input, and published with version history.

To get started, run a free [self-assessment](https://knov.ai/assess) against the standard. To earn a verified badge and directory listing, [request verification](https://knov.ai/verify) — a Knov.ai reviewer completes the human review.
