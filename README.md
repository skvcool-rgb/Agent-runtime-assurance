# Agent Runtime Assurance (ARA)

**A vendor-neutral control family for deterministic, tamper-evident, independently-verifiable runtime assurance of AI agents** — the software-agent port of aviation Run-Time Assurance (RTA / ASTM F3269).

> **Status:** Draft / proposed control language, offered for inclusion in emerging AI-agent standards. **Not yet adopted** by any standards body.
> **Maintainer:** Amworkplace Ltd (BoundaryAI). **License:** CC BY 4.0 (see [`LICENSE`](LICENSE)).

## One-line objective
When an AI agent can take a **consequential action**, that action MUST pass through a *deterministic, tamper-evident, independently-verifiable* runtime-assurance boundary whose safety properties are proven **before** deployment and replayable **after** an incident. ARA names a **capability**, not a product — the most complete implementation wins on merit; no vendor is named.

## The eight controls
| # | Control | Normative requirement (abridged) |
|---|---------|----------------------------------|
| **ARA-1** | Complete mediation | Every consequential action is evaluated before it reaches its effector; demonstrate no unmediated paths (chokepoint, not just adapter coverage). |
| **ARA-2** | Deterministic decision | allow / block / transform / escalate is a deterministic function of action + signed policy + bounded state. An LLM MAY advise; it SHALL NOT be the sole/final arbiter. |
| **ARA-3** | Evasion-resistant inspection | Normalize (NFKC, strip zero-width/bidi, homoglyph fold) + evaluate transport-decoded variants before matching. Raw substring matching SHALL NOT be relied upon. |
| **ARA-4** | Pre-runtime verification | Prove information-flow non-interference + mediation completeness before deploy; emit a reproducible signed **assurance certificate** bound to the policy's content hash. |
| **ARA-5** | Tamper-evident decision record | Each decision recorded in a signed, replayable, policy-version-bound log — sufficient to reconstruct an incident. |
| **ARA-6** | Policy integrity | Active policy cryptographically signed + verified before load; an unverifiable/altered policy SHALL fail closed (deny). |
| **ARA-7** | Defined safe-states | Every non-allow outcome resolves to a declared deterministic safe-state (reject-and-continue, human-checkpoint, degraded, halt-and-preserve). |
| **ARA-8** | Assurance independence *(advisory)* | The party issuing the ARA-4 certificate and operating the ARA-5 record SHOULD be independent of the model provider and cloud operator whose consumption the agent drives. |

Full normative text, mappings to **NIST AI RMF / OWASP Agentic Top-10 2026 / ISO/IEC 42001 / EU AI Act**, conformance evidence, and rationale: **[`ARA-control-family.md`](ARA-control-family.md)**.

**CSA AICM crosswalk:** ARA mapped control-by-control onto CSA's **AI Controls Matrix (AICM v1)** — showing where ARA supplies the deterministic, verifiable *how* for the AICM's agentic controls (`AIS-11` Agents Security Boundaries, `IAM-19` Agent Access Restriction, `TVM-11` Guardrails, `AIS-15` Prompt Differentiation, …): **[`ARA-AICM-crosswalk.md`](ARA-AICM-crosswalk.md)**.

## Why it's credible, not novel-risk
Run-Time Assurance is a **certified safety paradigm in aviation** (ASTM F3269; NASA/Skoog lineage; used to bound neural autopilots) — a simple *verified* monitor bounding a complex *opaque* controller with override authority. ARA ports that exact structure to software agents and, by extension, embodied/robotic systems. Trust in an ARA-4 certificate comes from **verifiability** (a reproducible signed proof bound to a policy hash — re-verifiable by anyone), not from a logo.

## Where this is headed (contribution channels)
This draft is offered under CC BY 4.0 to:
- **OWASP GenAI Security Project — Agentic Security Initiative** (map ARA into the Agentic Threats & Mitigations taxonomy / Solutions Reference Guide);
- **NIST AI Agent Standards Initiative (CAISI)** + the NIST AI Consortium;
- **ISO/IEC JTC1 SC42** via the national-body route.

Issues and PRs welcome.

## Citing
GitHub's **"Cite this repository"** (see [`CITATION.cff`](CITATION.cff)), or:
> Amworkplace Ltd (BoundaryAI). *Agent Runtime Assurance (ARA) Control Family.* 2026. https://github.com/skvcool-rgb/agent-runtime-assurance
