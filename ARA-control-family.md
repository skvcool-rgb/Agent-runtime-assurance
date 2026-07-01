# Proposed Control Family: Agent Runtime Assurance (ARA)
### A vendor-neutral contribution for NIST CAISI / OWASP GenAI / ISO-IEC SC42

**Submitter:** Amworkplace Ltd (BoundaryAI). **Date:** 2026-06-28. **License:** offered under CC-BY / Apache-2.0 (OWASP) — contribution grants the project a perpetual OSI license.
**Status:** draft control language for inclusion in the NIST AI Agent Standards Initiative (AI Agent security/authorization workstream), the OWASP Top 10 for Agentic Applications mitigation set, and ISO/IEC 42001 Annex-A agent guidance.

> **One-line objective:** when an AI agent can take a consequential action, the action MUST pass through a *deterministic, tamper-evident, independently-verifiable* runtime-assurance boundary whose safety properties are proven *before* deployment and replayable *after* an incident. This is the software-agent port of aviation Run-Time Assurance (RTA / ASTM F3269); it names a *capability*, not a product.

---

## 1. Gap this addresses (research-grounded)
- **No current framework was designed for agentic AI.** EU AI Act, NIST AI RMF, and ISO/IEC 42001 predate tool-using agents; their controls assume a model that *outputs*, not an agent that *acts*.
- The emerging consensus requirement is explicit: agents need *"unique identity, task-scoped authorization, **runtime policy enforcement**, human accountability, **immutable audit trails**, and scope isolation,"* and *"static IAM, prompt rules, and after-the-fact logs are not enough once an agent can execute actions."* ARA specifies **how** that runtime-enforcement-plus-immutable-audit obligation is met and verified.
- **Prompt-injection defeats text-matching guardrails** unless the inspected content is normalized first (homoglyph, zero-width, transport-encoding); naive substring policies are a *silent fail-open*. ARA requires evasion-resistant inspection as a control, not author discretion.

## 2. Normative control language (the contribution)

> **ARA-1 — Complete mediation.** Every *consequential action* (tool/function invocation, code or shell execution, network egress, payment, data write, model call carrying enterprise data) issued by an agent **SHALL** be evaluated by a runtime-assurance boundary *before* the action reaches its effector. Deployments **SHALL** demonstrate the absence of unmediated action paths (adapter coverage tests + an enforcement chokepoint that does not depend on agent cooperation).

> **ARA-2 — Deterministic decision.** The enforcement decision (allow / block / transform / escalate) **SHALL** be a deterministic function of the action, the signed policy, and bounded trace state. A nondeterministic component (including an LLM judge) **SHALL NOT** be the sole or final arbiter of a consequential action. *(An LLM may advise; it shall not decide.)*

> **ARA-3 — Evasion-resistant inspection.** Where the decision depends on inspecting content, the content **SHALL** be normalized before matching (Unicode NFKC, removal of zero-width/bidirectional formatting, homoglyph folding) and **SHALL** be evaluated against transport-decoded variants (e.g. base64/hex/percent-encoding) to a declared depth. Substring matching on raw content alone **SHALL NOT** be relied upon as a security control.

> **ARA-4 — Pre-runtime policy verification.** Before deployment, the policy **SHALL** be verified to satisfy declared safety properties — at minimum **information-flow non-interference** (no path from an untrusted source to a high-authority sink without a mandated mediation step) and **mediation completeness** (every high-authority capability has a required control). The verification **SHALL** emit a reproducible, signed **assurance certificate** bound to the policy's content hash.

> **ARA-5 — Tamper-evident decision record.** Each enforcement decision **SHALL** be recorded in a tamper-evident, cryptographically-signed log bound to the policy version in force, sufficient to **replay** the decision and prove the boundary behaved per policy. This record **SHALL** satisfy incident-reconstruction and serious-incident-reporting obligations.

> **ARA-6 — Policy integrity.** The active policy **SHALL** be cryptographically signed and its signature verified before load; an unverifiable or altered policy **SHALL** fail closed (deny).

> **ARA-7 — Defined safe-states.** Every enforcement outcome other than *allow* **SHALL** resolve to a declared, deterministic safe-state (e.g. reject-and-continue, human-checkpoint, degraded sub-capability, halt-and-preserve). Undefined behaviour on violation **SHALL NOT** be permitted.

> **ARA-8 — Assurance independence (advisory).** For high-impact deployments, the party issuing the ARA-4 certificate and operating the ARA-5 record **SHOULD** be independent of the model provider and the cloud operator whose consumption the agent drives, to avoid the conflict of interest inherent in self-attestation. *(Direct analog of financial-auditor independence.)*

## 3. Mapping to existing frameworks (so it slots in, not competes)
| ARA control | NIST AI RMF | OWASP Agentic Top-10 2026 | ISO/IEC 42001 | EU AI Act |
|---|---|---|---|---|
| ARA-1 mediation | MANAGE 2.x | Tool misuse, Excessive agency | A.6 operational controls | Art 15 (robustness) |
| ARA-2 determinism | MEASURE 2.x | Privilege/decision compromise | A.6 | Art 15 |
| ARA-3 evasion-resistant | MEASURE 2.x | Prompt injection, Data leakage | A.6 | Art 15 |
| ARA-4 pre-runtime cert | MAP / MEASURE | Untraceable reasoning | A.4 design verification | Art 9 (risk mgmt), Art 43 (conformity) |
| ARA-5 signed record | MANAGE 4.x | Insufficient observability | A.5 logging | **Art 12 logging, Art 73 incident reporting** |
| ARA-6 policy integrity | GOVERN | Supply-chain / policy tampering | A.5 | Art 15 (cybersecurity) |
| ARA-7 safe-states | MANAGE 1.x | Excessive agency | A.6 | Art 14 (human oversight) |
| ARA-8 independence | GOVERN 1.x | — | A.2 governance | Art 28 (third-party conformity bodies) |

## 4. Conformance evidence (how an assessor checks it)
1. Coverage proof that all consequential action types route through the boundary (tests + chokepoint).
2. Determinism proof: identical inputs → identical verdicts across N runs; no network/LLM call alters a verdict.
3. Evasion corpus pass: homoglyph/zero-width/encoded injection samples are caught.
4. A valid ARA-4 certificate whose content-hash matches the deployed policy.
5. A replayable signed decision log reproducing a sampled incident.
6. Signed-policy load-rejection on tamper.
7. A documented safe-state for every violation class.

## 5. Why this is credible, not novel-risk
Run-Time Assurance is a **certified safety paradigm in aviation** (ASTM F3269; NASA/Skoog lineage; deployed to bound neural autopilots). ARA ports the *same* structure — a simple verified monitor bounding a complex opaque function — to software agents and, by extension, embodied/robotic systems. Standardizing the *capability* gives regulators an enforceable, vendor-neutral control where none exists, and gives implementers a conformance target. **No vendor is named; the most complete implementation of the control wins on merit.**

---
*Contribution channels (verified live):* NIST AI Consortium (open membership, biannual review) + CAISI AI Agent Standards Initiative RFIs/listening sessions; OWASP GenAI Security Project Agentic Security Initiative (Slack `#team-genai-*`, bi-weekly syncs, GitHub — no contributor agreement required); ISO/IEC JTC1 SC42 national-body route. Map ARA into the OWASP **Agentic Risks & Mitigations Taxonomy** + **Solutions Reference Guide** first (fastest path to a citable, mapped control).
