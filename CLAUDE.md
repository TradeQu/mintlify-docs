# CLAUDE.md — mintlify-docs

The MeshQu developer documentation (Mintlify, deployed at docs.meshqu.com,
which tracks this repo's `main` directly). Pages are `.mdx`; navigation lives in
`docs.json`.

Read this before editing any page. These docs are **layer 4–5** of a
seven-layer truth hierarchy, which makes them the highest-authority *public*
surface — everything the website and the sales material say is downstream of
what is written here.

## The guardrail rule

> Any public content that shows, names or explains a **receipt field,
> signature, digest, verification result, evidence manifest or Decision Chain**
> must cite the source type, valid fixture or Receipt Reference section used to
> validate it.

Cite it in the PR description, or beside the change. "Checked against
`receipt-reference.mdx` §1.1" or "generated from the committed v2 fixture" is
enough. A claim with no cited source is not ready to merge.

**Scope is all public material, not just repositories** — documentation,
website copy, diagrams, sample JSON, decks, event pages, PDFs, screenshots,
sales collateral, conference assets, research summaries.

**Automated enforcement is only possible inside repositories, and this repo has
no build gate at all.** `meshqu-site` runs a build-time receipt-truth check
(`scripts/validate-content.ts`, PSA-060) that fails its build on hand-authored
receipt content. Nothing equivalent runs here, and nothing can run over a slide
deck or a screenshot. **Review is the control here — which is exactly why these
rules are written down rather than left to judgement.** For assets outside any
repository, use the checklist: `tradequ/docs/public-material-checklist.md`.

> **Human review governs meaning; automated validation governs structural
> truth.** Where a mechanical check exists it is what actually holds; this rule
> is the layer on top of it, not a substitute. Do not read the absence of a
> checker in this repo as a lower standard — it is the opposite. Every
> fabricated receipt on the website passed human review, repeatedly, for
> months.

## `concepts/receipt-reference.mdx` is the public-copy authority

It is the single page that governs what any public surface may say about the
receipt. Before writing about the receipt anywhere, check it:

| Section | What it governs |
| --- | --- |
| §1.1 | The v1/v2 field table — which fields exist, and their types |
| §2 | Exact hash payloads, the signing envelope, the canonicalisation profile |
| §10.3 | The verifier sub-claims — there are **ten** |
| §10.4 | The full failure-code list |
| §15 | The status matrix — Implemented / Partial / Stub / Planned |
| §16 | The use/avoid glossary for public terms |
| §17 | The two highest-risk public-copy traps |
| §18 | The publication order for wire-format changes |

Two hard rules follow from it:

- **No capability may be described as implemented unless §15 lists it as
  Implemented.** "Partial", "Stub" and "Planned" are not "implemented soon".
  Recursive governance, live-witness inclusion-proof verification and RFC 8785
  canonicalisation are Planned today.
- **Receipt-shaped examples must be checked against a repository fixture or the
  Receipt Reference.** Hand-authored receipt examples are not permitted on any
  public surface. If you need a sample, take it from the committed fixture set
  (`meshqu-site/public/fixtures/`, generated and signed in `tradequ`) or copy a
  shape straight out of §1.1 and say so.

If a page and the Receipt Reference disagree, the Reference wins and the page is
the bug — unless the Reference itself contradicts the implementation, in which
case both are bugs and the implementation wins.

## Fixed wordings — do not paraphrase

**The assurance ladder.** Used verbatim on `concepts/decision-assurance.mdx` and
`security/trust-model.mdx`:

| Rung | Meaning |
| --- | --- |
| **L1 Recorded** | A structured decision record exists |
| **L2 Integrity protected** | Signed content can be checked for alteration |
| **L3 Context bound** | Policy, evidence and decision context are cryptographically bound |
| **L4 Lineage verified** | Approval, custodian and chain relationships can be checked |
| **L5 Independently governed trust** | Verification relies on independently distributed trust roots, operational key governance and deployment-specific external assurance |

Both pages must keep splitting **implemented mechanics** (receipt v2, evidence
manifests, custodian signatures, approval lineage, chain verification, offline
transparency proof checks) from **deployment and governance responsibilities**
(how verifiers obtain trusted roots, key rotation and revocation, independent
custody, external log availability, organisational controls, retention and
publication policy). The remaining gap is operational trust architecture, not
unfinished receipt structure.

**The sub-claim count is ten.** The verification bundle binds ten sub-claims,
not eight. Three checks is the *single-receipt* number; do not mix them.

**The canonical Decision Receipt definition** in `index.mdx` is deployed
byte-identical across five public placements (six source occurrences) spanning
this repo and `meshqu-site`. AI systems quote whichever phrasing recurs. Never
reword it in one place; if it must change, change all six in one coordinated
pass and verify byte-identity with a grep.

## Trust-model precision

- **A valid signature does not by itself prove the content is unaltered.** Edit
  a receipt's content and the signature stays valid — it covers the stored
  integrity hash, which the edit did not touch. Rewrite the hash to paper over
  the edit and the signature goes invalid. **The two checks together** are what
  make tampering evident. `guides/verifying-offline.mdx` has the honest two-step
  demo; that is the framing to reuse, and that page should not be rewritten away
  from it.
- Verification proves issuance and integrity, never correctness. It does not
  prove the decision was right, the inputs truthful, or any legal requirement
  met.
- Tamper-**evident**, never tamper-proof. Verifiable **offline**, never
  trustless.
- Rekor is a public transparency log, **not a blockchain**. No on-chain or
  notarisation framing.
- MeshQu does not store evidence content. Evidence *references* and evidence
  *digests* only.
- Verification results are verifier output computed from a bundle — never
  fields inside a receipt.

## Publication order for wire-format changes

`tradequ/CLAUDE.md` § "Surface-update order for wire-format changes" is the
standing source; §18 of the Receipt Reference is its docs-side statement.

1. Implementation and tests
2. Migration and compatibility tests
3. Signed public fixture
4. **Receipt Reference — this repo**
5. Developer guides
6. Website summaries and visuals
7. Sales and conference assets

Each step gates the next, and step 4 cuts both ways: nothing downstream may run
ahead of the Receipt Reference, and the Receipt Reference may not run ahead of
the signed fixture.

**Standing embargo:** no page here describes receipt v3 fields, semantics or
timelines until steps 1–4 are complete. Receipt v3 is DAH-301, owned by
`tradequ/.harness/decision-assurance-hardening/`.

## Repo mechanics

- **This repo is a git submodule of `tradequ`, and the checkout there sits in
  detached HEAD.** If you are working through that checkout, run
  `git checkout main && git pull && git checkout -b <branch>` *inside* the
  submodule first. Committing after a bare `git checkout main` pushes straight
  to `main` and bypasses review.
- **Never bump the parent submodule pointer as part of a docs PR.** The
  submodule and the parent are reviewed separately; the pointer is bumped
  deliberately, only when asked. It can lag this repo's `main` — when it does,
  `TradeQu/mintlify-docs@main` is the current authority, because docs.meshqu.com
  tracks this repo, not the pointer.
- There is no build or link checker in CI. Verify links by inspection, or run
  `mintlify dev` locally if it is installed.
