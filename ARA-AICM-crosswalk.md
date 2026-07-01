# ARA ↔ CSA AI Controls Matrix (AICM) Crosswalk

**Maps the eight Agent Runtime Assurance (ARA) controls onto the Cloud Security Alliance's AI Controls Matrix (AICM v1).**

> **Status:** Proposed crosswalk, offered under CC BY 4.0. **Not endorsed by the Cloud Security Alliance.** Control IDs and titles below are CSA's (AICM v1); ARA control text is this project's ([`ARA-control-family.md`](ARA-control-family.md)).
> **Maintainer:** Amworkplace Ltd (BoundaryAI).

## Why this crosswalk exists

The AICM is CSA's 243-control, 18-domain framework for trustworthy AI. It already names the **agentic** control surface — *"Agents Security Boundaries" (AIS-11)*, *"Agent Access Restriction" (IAM-19)*, *"Guardrails" (TVM-11)*, *"Prompt Differentiation" (AIS-15)*, *"AI Sandboxing" (AIS-13)*. What the AICM (like every governance framework) deliberately leaves open is the **testable engineering "how":** *how* do you prove an agent boundary has no bypass? *how* must a guardrail decide? *how* do you make an audit log sufficient to reconstruct an incident?

**ARA is that "how."** It is a runtime-assurance control family — the software-agent port of aviation Run-Time Assurance (ASTM F3269) — that turns the AICM's agentic *objectives* into **deterministic, tamper-evident, independently-verifiable requirements** with conformance evidence. This document maps each ARA control to the specific AICM controls it operationalizes, provides evidence for, or extends.

## How to read the relationship column

| Relationship | Meaning |
|---|---|
| **Operationalizes** | ARA gives a testable, engineering-level requirement for an AICM objective that the AICM states at the policy level. |
| **Evidence-for** | Conformance to the ARA control produces artifacts that directly satisfy / audit the AICM control. |
| **Extends** | ARA adds a normative requirement the AICM does not currently specify (the determinism / verifiability / fail-closed delta). |

---

## Summary matrix

| ARA control | Primary AICM controls | Relationship | The delta ARA adds |
|---|---|---|---|
| **ARA-1** Complete mediation | AIS-11, IAM-19, IAM-16, AIS-10 | Operationalizes | Chokepoint with a *proven-no-bypass* requirement, not just adapter coverage |
| **ARA-2** Deterministic decision | TVM-11, IAM-16, IAM-18, GRC-15 | Extends | The guardrail MUST be a deterministic function; an LLM may advise, not be the sole/final arbiter |
| **ARA-3** Evasion-resistant inspection | AIS-08, AIS-09, AIS-15, TVM-02, MDS-06/07 | Operationalizes | Named normalization (NFKC, zero-width/bidi strip, homoglyph fold, transport-decode) before match; raw substring matching disallowed |
| **ARA-4** Pre-runtime verification | A&A-01/03, AIS-05, MDS-03/04/05, GRC-10 | Extends | A reproducible **signed assurance certificate bound to the policy content-hash**, proving mediation completeness + non-interference *before* deploy |
| **ARA-5** Tamper-evident decision record | LOG-11, LOG-08, LOG-02/09, IAM-12, LOG-14/15, GRC-13/14 | Operationalizes | Each decision in a *signed, replayable, policy-version-bound* record sufficient to reconstruct an incident |
| **ARA-6** Policy integrity | MDS-08, MDS-09, CEK-03/04/10, CCC-*, GRC-04 | Operationalizes | Active policy signed + verified before load; unverifiable/altered policy **fails closed (deny)** |
| **ARA-7** Defined safe-states | BCR-09, BCR-03, AIS-13, MDS-11, GRC-15 | Extends | Every non-allow outcome resolves to a *declared deterministic* safe-state (reject-and-continue / human-checkpoint / degraded / halt-and-preserve) |
| **ARA-8** Assurance independence *(advisory)* | A&A-02, STA-05, STA-02, GRC-06 | Evidence-for | The party issuing the ARA-4 certificate + operating the ARA-5 record SHOULD be independent of the model provider / cloud operator |

---

## Detailed mappings

### ARA-1 — Complete mediation
> *Every consequential action is evaluated before it reaches its effector; demonstrate no unmediated paths (chokepoint, not just adapter coverage).*

| AICM | Title | Relationship |
|---|---|---|
| **AIS-11** | Agents Security Boundaries | **Operationalizes** — ARA-1 is the testable form of an agent security boundary: a single mediation chokepoint every action must traverse. |
| **IAM-19** | Agent Access Restriction | Operationalizes — action-level mediation is where agent access is actually restricted at runtime. |
| **IAM-16** | Authorization Mechanisms | Evidence-for — every action crossing the chokepoint is an authorization-mechanism invocation. |
| **AIS-10** | API Security | Evidence-for — mediation covers the tool-call / API surface the agent drives. |

**Delta:** AICM requires that agent boundaries *exist*; ARA-1 additionally requires **demonstrating that no unmediated path exists** — a chokepoint proven complete (see ARA-4), not merely a set of instrumented adapters that might be bypassed.

### ARA-2 — Deterministic decision
> *allow / block / transform / escalate is a deterministic function of action + signed policy + bounded state. An LLM MAY advise; it SHALL NOT be the sole/final arbiter.*

| AICM | Title | Relationship |
|---|---|---|
| **TVM-11** | Guardrails | **Extends** — AICM mandates guardrails but is silent on *how* they decide. ARA-2 requires the guardrail be a deterministic function, with any LLM confined to an advisory role. |
| **IAM-16** | Authorization Mechanisms | Operationalizes — the decision is a deterministic authorization function of (action, signed policy, bounded state). |
| **IAM-18** | Output Modification and Special Authorization | Operationalizes — the deterministic **transform** outcome. |
| **GRC-15** | Human Supervision | Evidence-for — the deterministic **escalate** outcome routes to human supervision. |

**Delta:** This is the sharpest ARA contribution. A guardrail whose verdict is produced by a non-deterministic model is not reproducible, not verifiable, and not replayable. ARA-2 makes determinism a **requirement**, which is the precondition for ARA-4 (pre-runtime proof) and ARA-5 (replay) to mean anything.

### ARA-3 — Evasion-resistant inspection
> *Normalize (NFKC, strip zero-width/bidi, homoglyph fold) + evaluate transport-decoded variants before matching. Raw substring matching SHALL NOT be relied upon.*

| AICM | Title | Relationship |
|---|---|---|
| **AIS-08** | Input Validation | **Operationalizes** — ARA-3 specifies the normalization pipeline input validation must apply for agent inputs. |
| **AIS-09** | Output Validation | Operationalizes — same pipeline applied to model/agent outputs. |
| **AIS-15** | Prompt Differentiation | Operationalizes — separating instruction from data survives only if evasion (encoding tricks) is normalized first. |
| **TVM-02** | Malware and Malicious Instructions Protection | Evidence-for — prompt-injection / malicious-instruction resistance. |
| **MDS-06 / MDS-07** | Adversarial Attack Analysis / Robustness | Evidence-for — evasion-resistant inspection is a runtime robustness measure. |

**Delta:** AICM says "validate input"; ARA-3 names the concrete, testable normalization (Unicode NFKC, zero-width & bidi stripping, homoglyph folding, transport-layer decode before match) and **forbids reliance on raw substring matching** — closing the class of encoding-based evasions.

### ARA-4 — Pre-runtime verification
> *Prove information-flow non-interference + mediation completeness before deploy; emit a reproducible signed assurance certificate bound to the policy's content hash.*

| AICM | Title | Relationship |
|---|---|---|
| **A&A-01 / A&A-03** | Audit & Assurance Policy / Risk-Based Planning | **Extends** — ARA-4 provides a machine-checkable assurance artifact, not a periodic manual assessment. |
| **AIS-05** | Application Security Testing | Operationalizes — pre-deploy verification of the enforcement configuration. |
| **MDS-03 / 04 / 05** | Model Documentation / Requirements / Validation | Evidence-for — the certificate is a validated, documented assurance artifact. |
| **GRC-10** | AI Impact Assessment | Evidence-for — verification precedes deployment. |

**Delta:** ARA-4 requires a **reproducible signed certificate bound to the policy's content hash**, attesting mediation completeness and information-flow non-interference *before* the agent goes live — re-verifiable by any party from the policy hash. This is the "certificate of validity" pattern realized as a verifiable artifact rather than an attestation letter.

### ARA-5 — Tamper-evident decision record
> *Each decision recorded in a signed, replayable, policy-version-bound log — sufficient to reconstruct an incident.*

| AICM | Title | Relationship |
|---|---|---|
| **LOG-11** | Transaction/Activity Logging | **Operationalizes** — every mediated decision is a logged transaction. |
| **LOG-08 / LOG-02 / LOG-09** | Log Records / Audit Logs Protection / Log Protection | Evidence-for — the signed record is protected + integrity-verifiable. |
| **IAM-12** | Safeguard Logs Integrity | Operationalizes — cryptographic signing *is* the integrity safeguard. |
| **LOG-14 / LOG-15** | Input / Output Monitoring | Evidence-for — inputs & outputs captured at the decision point. |
| **GRC-13 / GRC-14** | Explainability Requirement / Evaluation | Evidence-for — a replayable, policy-bound record is the substrate for explaining any decision. |

**Delta:** Beyond "protect the logs," ARA-5 requires each record be **signed, replayable, and bound to the exact policy version** in force — sufficient to *deterministically reconstruct* the decision during an incident (which is only possible because of ARA-2).

### ARA-6 — Policy integrity
> *Active policy cryptographically signed + verified before load; an unverifiable/altered policy SHALL fail closed (deny).*

| AICM | Title | Relationship |
|---|---|---|
| **MDS-08 / MDS-09** | Model Integrity Checks / Model Signing & Ownership Verification | **Operationalizes** — ARA-6 applies the AICM's model-signing discipline to the *enforcement policy*. |
| **CEK-03 / CEK-04 / CEK-10** | Data Encryption / Encryption Algorithm / Key Generation | Evidence-for — the signing + verification cryptography. |
| **CCC-\*** | Change Control & Configuration Management | Operationalizes — policy changes are integrity-gated at load time. |
| **GRC-04** | Policy Exception Process | Evidence-for — exceptions are themselves signed policy. |

**Delta:** ARA-6 adds the **fail-closed** requirement: an unsigned, unverifiable, or altered policy MUST deny rather than fall back to permit. The AICM signs *models*; ARA-6 signs the *authority that governs the agent* and makes tampering self-defeating.

### ARA-7 — Defined safe-states
> *Every non-allow outcome resolves to a declared deterministic safe-state (reject-and-continue, human-checkpoint, degraded, halt-and-preserve).*

| AICM | Title | Relationship |
|---|---|---|
| **BCR-09 / BCR-03** | Disaster Response Plan / Business Continuity Strategy | **Extends** — ARA-7 pre-declares the deterministic end-states the agent lands in, per outcome. |
| **AIS-13** | AI Sandboxing | Operationalizes — sandboxing is one concrete safe-state (containment). |
| **MDS-11** | Model Failure | Operationalizes — model/agent failure resolves to a named safe-state, not undefined behavior. |
| **GRC-15** | Human Supervision | Operationalizes — the **human-checkpoint** safe-state. |

**Delta:** AICM handles resilience at the *business-continuity* layer; ARA-7 requires that **every individual non-allow decision** resolve to one of a small set of *declared, deterministic* safe-states — so "what happens on block" is specified and testable, not emergent.

### ARA-8 — Assurance independence *(advisory)*
> *The party issuing the ARA-4 certificate and operating the ARA-5 record SHOULD be independent of the model provider and cloud operator whose consumption the agent drives.*

| AICM | Title | Relationship |
|---|---|---|
| **A&A-02** | Independent Assessments | **Evidence-for** — ARA-8 is the runtime-assurance instance of independent assessment. |
| **STA-05 / STA-02** | SSRM Control Ownership / SSRM Policy | Operationalizes — assigns assurance ownership away from the party whose consumption is being governed, within CSA's Shared Security Responsibility Model. |
| **GRC-06** | Governance Responsibility Model | Evidence-for — independence is a governance-responsibility placement. |

**Delta:** Ported directly from aviation RTA's independence principle: the monitor that can override the agent, and the party that certifies it, SHOULD NOT be the same party that profits from the agent's consumption. AICM's SSRM provides the ownership vocabulary; ARA-8 states the independence direction.

---

## Reverse view — AICM's agentic controls, and ARA's verifiable implementation

For readers coming *from* the AICM, these are the AI-/agent-specific controls where ARA supplies the missing engineering requirement:

| AICM control | What it requires | ARA implementation |
|---|---|---|
| **AIS-11** Agents Security Boundaries | Agents operate within security boundaries | **ARA-1** — a proven-complete mediation chokepoint |
| **IAM-19** Agent Access Restriction | Restrict what agents can access | **ARA-1 + ARA-2** — action-level mediation + deterministic authorization |
| **TVM-11** Guardrails | Guardrails constrain AI behavior | **ARA-2** — the guardrail must decide deterministically (LLM advisory only) |
| **AIS-15** Prompt Differentiation | Separate instructions from data | **ARA-3** — evasion-resistant normalization so differentiation can't be smuggled past |
| **AIS-08 / AIS-09** Input / Output Validation | Validate agent I/O | **ARA-3** — named normalization pipeline, no raw substring matching |
| **AIS-13** AI Sandboxing | Contain AI execution | **ARA-7** — sandboxing as a declared safe-state |
| **MDS-09** Model Signing / Ownership | Sign & verify model artifacts | **ARA-6** — the same discipline applied to the enforcement policy, fail-closed |
| **LOG-11** Transaction/Activity Logging | Log AI transactions | **ARA-5** — signed, replayable, policy-version-bound decision records |
| **GRC-15** Human Supervision | Keep humans in the loop | **ARA-2 + ARA-7** — deterministic escalate → human-checkpoint safe-state |

---

## Methodology, sources & caveats

- **AICM version:** control IDs and titles reflect **CSA AI Controls Matrix v1** (v1.0 released July 2025; v1.1 current). 243 control objectives across 18 domains.
- **Source of control catalog:** the AICM control IDs/titles used here were taken from a public rendering of the AICM v1 control set (Open Security Architecture, *CSA AICM v1 Mappings*). **Before any formal submission to CSA, these mappings should be reconciled against CSA's official AICM v1.1 spreadsheet** (the authoritative artifact, distributed by CSA), including the AICM Implementation & Auditing Guidelines and the AI-CAIQ.
- **Mapping intent:** this crosswalk is a *contribution proposal* — it argues ARA belongs as a referenced runtime-assurance pattern strengthening the AICM's agentic controls. It is **not** a claim of CSA endorsement.
- **Relationship types** (Operationalizes / Evidence-for / Extends) are defined at the top of this document.
- **License:** CC BY 4.0, consistent with the rest of this repository.

## Sources
- CSA, *Introducing the CSA AI Controls Matrix* — https://cloudsecurityalliance.org/blog/2025/07/10/introducing-the-csa-ai-controls-matrix-a-comprehensive-framework-for-trustworthy-ai
- CSA, *AI Controls Matrix v1.1* (artifact) — https://cloudsecurityalliance.org/artifacts/ai-controls-matrix-v1-1
- Open Security Architecture, *CSA AICM v1 Mappings* — https://opensecurityarchitecture.org/frameworks/csa-aicm/
