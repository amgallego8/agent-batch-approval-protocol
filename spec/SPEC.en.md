# Agent Batch Approval Protocol (Plan-Preview-Commit)

**Author:** Angel Gallego · ORCID 0009-0002-9459-4374
**Version:** 0.1.0 (draft) · **Date:** 2026-10-18 · **Status:** self-published draft, not peer reviewed
**Licenses:** text under CC BY 4.0; schemas and code under Apache-2.0
**Evidence convention:** [Sourced] · [General knowledge] (to be verified) · [Proposal] · [Hypothesis]
**Versión en español:** [SPEC.es.md](SPEC.es.md)

> **Status notice.** This is an independent specification, not an industry standard. It contains no published empirical validation. Novelty claims are limited to what section 10 describes.

---

## 0. Conventions
The words MUST, SHOULD and MAY carry their usual meaning in technical specifications. Third-party references are cited as context or prior art; no affiliation or endorsement is implied.

## 1. Abstract
AI agents execute actions with real effects on an organization's systems. Human approval action by action produces fatigue and habituation; bulk approval without reading removes oversight. This draft specifies a protocol in which the agent proposes a batch of actions, a human reviews it as a unit through a preview package, and execution is verifiably bound to what was approved. It defines the batch lifecycle, the package content, granularity requirements for composite plans, a decision record, and requirements for the human operator with supervision metrics. It contains no empirical validation; prior art is declared in section 3.

## 2. Problem and scope
**2.1 Problem.** Human oversight of agents degrades with volume: the literature reports reviewer habituation and approval fatigue (section 3). Common solutions approve action by action, or in bulk over homogeneous items.

**2.2 Scope.** A protocol for an agent to propose a batch of actions, for a human to review it as a unit, and for execution to be verifiably bound to what was approved.

**2.3 Out of scope.** It is not an "organizational operating system"; that concept is already occupied by third parties. It does not replace identity management, legal compliance, or a legal-evidentiary framework. It does not define the AI model or the transport (MCP or other).

## 3. Related work and positioning
Reading status: **V** = verified directly by the author while drafting; **R** = summary only, full reading pending.

| Topic | Source | Status | Relation |
|---|---|---|---|
| Homonymous governed-orchestration framework (22 JSON Schemas, conformance suite) | [AI Orchestration Framework (aof-framework), v1.0 LTS, 5 Sep 2026, Apache-2.0](https://github.com/aof-framework/aof) | V | Prior art in orchestration; does not address the preview↔commit binding |
| Action-bound, fail-closed approval | [Microsoft Agent Governance Toolkit, ADR 0030](https://github.com/microsoft/agent-governance-toolkit/blob/main/docs/adr/0030-action-bound-approval-protocol.md) | R | Direct prior art for the verifiable binding (5.4) |
| Pending approvals returned together | [OpenAI Agents SDK, human-in-the-loop](https://openai.github.io/openai-agents-js/guides/human-in-the-loop/); [LangChain HITL](https://docs.langchain.com/oss/python/langchain/human-in-the-loop) | R | Implement approval; do not define the preview package |
| "Intent, plan, preview, commit" principle | [N. Bertino, *AI's affordance problem*](https://www.nicbertino.com/blog/ai-affordance/) | V | Design principle; no protocol or schema |
| "Plan-preview-commit" as planned functionality | [TDMCP roadmap](https://pantani.xyz/tdmcp/roadmap) | V | Described as not yet implemented |
| Reversible pseudonymization in an MCP server | [dbi services, Feb 2026](https://www.dbi-services.com/blog/rag-mcp-skills-three-paradigms-for-llms-talking-to-your-database-and-why-governance-changes-everything/) | R | Pattern with a reference SQL schema |
| Reversible pseudonymization, prototype | [daslabhq/pii-proxy (MIT)](https://github.com/daslabhq/pii-proxy) | V | Prototype; no documented MCP support |
| LLM proxy with masking and unmasking | [LiteLLM + Presidio](https://docs.litellm.ai/docs/proxy/guardrails/pii_masking_v2) | R | Available implementation |
| Batch review as fatigue mitigation | Mitchell et al., [arXiv:2608.23642](https://arxiv.org/abs/2608.23642) | R | Academic basis for batch review |
| Reviewer habituation | Yu et al., [arXiv:2606.22721](https://arxiv.org/abs/2606.22721); Turan, [arXiv:2606.08919](https://arxiv.org/abs/2606.08919) | R | Basis for operator requirements (5.9) |
| Deceptive approval ("Loopjacking") | Arun Kumar, [arXiv:2609.21081](https://arxiv.org/abs/2609.21081) | R | Threat motivating the verifiable binding |
| Traces for generative AI | [OpenTelemetry GenAI semantic conventions](https://github.com/open-telemetry/semantic-conventions-genai) | R | Possible basis for the record (5.7) |
| Macro context "AI(-first) operating system" | [WEF/Kearney, Jun 2026](https://www.weforum.org/publications/ai-jp-2097); [McKinsey, Oct 2026](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-ai-operating-system-a-new-way-for-ceos-to-run-the-enterprise) | V | Context only |

## 4. Layered reference model
A conceptual model in which the protocol occupies the orchestration layer and depends on the others.

| Layer | Function | Relation to the protocol |
|---|---|---|
| 0. Human operator | Skills to delegate, review and reject | Approver requirements (5.9) |
| 1. Isolated execution | Explicit permissions and isolation | Where the commit runs |
| 2. Orchestration | Tool connector (e.g., MCP) and protocol | **Core of this document** |
| 3. Memory and record | Shared state and decision record | Destination of the record (5.7) |

## 5. Protocol core
**5.1 Actors.** Proposing agent · Operator/approver · Executor · Record. The proposing role MUST be separate from the approving role.

**5.2 Batch lifecycle.** Plan → Preview → Approval → Commit → Record. Terminal states: rejected · expired · invalidated by change · executed · partially executed and compensated.

**5.3 Preview package (minimum conceptual content).**

| Element | Purpose |
|---|---|
| Batch identifier and version | Traceability |
| Batch type (homogeneous or composite) | Sets the rules of 5.5 |
| Ordered list of proposed actions | What will be done, in understandable language |
| Expected effects and irreversible-action flag | Inform the risk |
| Highlighted exceptions | Prevent approval without reading |
| Dependencies between actions | Execution order and failure semantics |
| Verifiable fingerprint of the set | Basis of the binding (5.4) |
| Validity period | Prevent use of stale approvals |

**5.4 Verifiable binding between preview and commit.**
The commit MUST execute exactly what was approved. Any change after approval MUST invalidate it. The approval MUST expire. On failure or doubt, behavior MUST be fail-closed (do not execute). The fingerprint, signature and expiry method is defined in v0.2. [Hypothesis] This binding has prior art (section 3); this document's contribution is incorporating it as a requirement of the batch protocol.

**5.5 Batch granularity and composite plans.**
- *Homogeneous batch:* independent items of the same type (e.g., N records of one kind).
- *Composite batch:* actions with dependencies on each other (e.g., create an element, create others under it, and assign them).
- The package MUST declare its type. In a composite batch it MUST express the dependency graph, the commit order and the failure semantics (all-or-nothing, or partial with compensation).
- Approval applies to the set. If the approver rejects or edits items, a new package with a new fingerprint MUST be generated.
- The package MUST highlight exceptions: outliers, irreversible actions and the highest-impact actions.
- **Risk tiers.** Each domain profile MUST be able to declare actions that are never approved in batch and require individual approval. [Sourced: approval guidance cited in research reports, summary only] Some guidance advises against bulk approval for the highest-risk actions; the protocol supports this as configuration.
- *Maximum size:* a configurable limit SHOULD exist. No value is set; it will be determined with data.
- [Hypothesis] In the products reviewed, bulk approval of homogeneous items predominates; composite plans with dependencies did not appear as a documented case.

**5.6 Reversal and compensation.** What can be undone, what cannot, and how it is recorded. Developed in v0.2.

**5.7 Decision record.** Minimum conceptual fields: reviewer identity, timestamp, review duration, decision (approved, edited, rejected), reason code, difference between proposed and executed, and batch fingerprint. [Sourced: approval guidance cited in research reports; direct reading pending] The formal schema is published in v0.2.

**5.8 Data protection (optional module).** Reversible tokenization or pseudonymization before data is sent to the model. This is pseudonymization, not anonymization. The mapping table is sensitive data and MUST be protected (encryption at rest and restricted access). Implementations should consider the personal-data regulations of their jurisdiction.

**5.9 Human operator requirements (Layer 0).**
- *Minimum skills (declared, not a certification):* state the "what" of a delegation clearly; read a preview package; identify exceptions and irreversible effects; reject or edit with a reason; recognize automation bias and fatigue.
- *Training:* [Proposal] practice with sample packages containing planted errors.
- *Continuous calibration:* [Proposal] periodic injection of control packages with a known error, to measure real reviewer vigilance.
- *Supervision metrics (definitions):*
  - rejection and edit rates;
  - review time per item and per batch;
  - detection rate of planted errors;
  - habituation drift: change in the detection rate across a session or several;
  - load: number of batches and items per session.
- *Alerts:* reviews too fast relative to batch size; long sessions.
- *Thresholds:* not set. They will be determined empirically.
- *Scope:* it does not certify people; it is a process requirement.
- *Use of the metrics:* they serve to calibrate the system and the training, not to evaluate people. In any study they are reported aggregated and anonymized, with informed consent and without individual identification.
- [Hypothesis] In the searches performed, no framework was found that treats these requirements as a normative component of an operating architecture; this is not proof that none exists.

## 6. Conformance
Conformance levels and minimum tests: under development. They must define what an implementation must demonstrate to claim conformance.

## 7. Domain profiles (informative annexes)
Each profile defines domain objects and specific risks without changing the core. **Status: planned; no evidence of use.**

**7.1 Project management offices.** Objects: project, module, task, schedule. Risks: mass alteration of plans, wrong assignments, date inconsistencies.
**7.2 Marketing agencies.** Objects: campaign, asset, calendar, publication, budget. Risks: irreversible publications, ad spend, brand consistency.
**7.3 Template for new profiles.** Objects · permitted actions · irreversible actions · actions requiring individual approval · additional package fields · operator metrics.

## 8. Threat model
Deceptive approval (what is shown differs from what is executed) · reviewer habituation · bulk approval without reading · reuse of expired approvals · leakage of the pseudonym mapping · instruction injection in batch content. Mitigations per threat are developed in v0.2.

## 9. Empirical validation
**Current status: no published results.** Planned design:
- Reproducible local environment with synthetic data generated with a fixed seed and published.
- Manual baseline versus the protocol.
- Multiple runs per condition, with confidence intervals.
- Planted errors to measure operator vigilance.
- Results, model configuration and raw records will be published together.
- Studies with people: voluntary participation with informed consent and withdrawal without consequences; anonymized data and aggregate reporting; in addition to process metrics, perception measures (friction, workload, trust). They are not evaluative.

## 10. Limitations and bounded novelty statement
Permitted wording: "In the searches performed, no prior publication was found that integrates these elements." It is not claimed that the individual components are new, that no prior art exists, or that there are cases applied in organizations. Several sources in section 3 were read only as summaries, and the searches were not exhaustive.

## 11. Project governance
Semantic versioning · licenses (text CC BY 4.0; schemas and code Apache-2.0) · contributions via issues · citation via `CITATION.cff` · the project uses this descriptive name and avoids acronyms.

## 12. Roadmap
- [ ] v0.2: fingerprint and expiry method (5.4); JSON Schemas for the package and the record.
- [ ] v0.2: selection and validation of perception instruments (friction, workload, trust, emotions) for studies with people.
- [ ] Full reading of sources marked R and BibTeX.
- [ ] Conformance levels and tests.
- [ ] Threat-model mitigations.
- [ ] Reproducible sandbox with synthetic data and measured results.
- [ ] Domain profiles: from draft to evidence.
- [ ] Reference implementation, sandbox and evidence in separate repositories linked from here.
- [ ] External review.

## 13. References
Sources are linked in section 3. Pending: BibTeX list with the fully read sources.
