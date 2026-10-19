# Agent Batch Approval Protocol (Plan-Preview-Commit)

> **Status: draft v0.1.0.** Specification skeleton. This is an independent, self-published specification: it is **not an industry standard**, it has **not been peer reviewed**, and it contains **no published empirical validation** yet.

Español: [README.es.md](README.es.md)

## What this is
An open specification for how a human reviews and approves a **batch** of actions proposed by an AI agent, and how execution is **verifiably bound** to exactly what was approved.

## Why
- Approving every agent action one by one produces fatigue and habituation.
- Approving in bulk without reading removes real oversight.
- This specification defines a middle path: the agent proposes a batch, a human reviews it as a unit through a preview package, and the commit can only execute what was approved.

## What it covers
- Lifecycle of a batch: Plan → Preview → Approval → Commit → Record.
- Content of the preview package.
- Verifiable binding between preview and commit (requirements; method planned for v0.2).
- Batch granularity, including composite plans with dependencies.
- Decision record (ledger), optional reversible-pseudonymization module, and requirements for the human operator with supervision metrics.
- Informative domain profiles (project offices, marketing agencies) — planned, with no evidence of use.

## What it does not cover
- It is not an "organizational operating system".
- It does not replace identity management, legal compliance, or a legal-evidentiary framework.
- It does not define the AI model or the transport (MCP or others).

## Repository contents
| Path | Content |
|---|---|
| `spec/SPEC.en.md` | Specification (English) |
| `spec/SPEC.es.md` | Especificación (Español) |
| `LICENSE` | Apache-2.0 (schemas and code) |
| `LICENSE-docs.md` | CC BY 4.0 notice (text) |
| `CITATION.cff` | Citation metadata |

## Related work and novelty claim
Prior art exists for each individual component (human approval in agent SDKs, reversible pseudonymization proxies, hash-chained decision logs, action-bound approval). See section 3 of the specification. The only claim made is bounded: **in the searches performed, no prior publication was found that integrates these elements.** Absence of findings is not proof of absence.

## Roadmap
- v0.2: binding method and expiry; JSON Schemas for the preview package and the decision record.
- Full reading of the sources marked "summary only"; BibTeX.
- Conformance levels and tests.
- Reproducible sandbox with synthetic data and measured results.
- Domain profiles moving from draft to evidence.
- The reference implementation, sandbox and evidence will live in separate repositories linked from here.

## License
- Schemas and code: [Apache-2.0](LICENSE).
- Text and documentation: [CC BY 4.0](LICENSE-docs.md).

## How to cite
See [CITATION.cff](CITATION.cff). A DOI will be added after the first archived release.

## Author
Angel Gallego — ORCID [0009-0002-9459-4374](https://orcid.org/0009-0002-9459-4374)

## Contributing
Issues and discussion are welcome: terminology, missing prior art, threat scenarios, proposals for domain profiles.

## AI assistance
Claude (Anthropic) acted as the author's AI copilot for research, drafting and review. The author reviewed and approved the content and is solely responsible for it.

## Disclaimer
This is not legal advice. Third-party projects and organizations mentioned are cited as context or prior art; their names belong to their owners, and no affiliation or endorsement is implied.
