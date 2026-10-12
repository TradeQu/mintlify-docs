# Documentation contribution notes

This repository contains MeshQu developer documentation published at
`docs.meshqu.com`. Pages are written in MDX; navigation is defined in
`docs.json`. Treat every tracked file as public, including files excluded from
the rendered site.

## Accuracy

- Check endpoint names, parameters, and response shapes against the published
  API contract before changing examples.
- Use [Receipt Reference](concepts/receipt-reference.mdx) for receipt fields,
  signed payloads, schema versions, and verification limits. Check examples
  against a valid receipt fixture rather than inventing signed values.
- Distinguish `POST /v1/decisions/evaluate`, which returns an unsigned preview,
  from `POST /v1/decisions/record`, which persists a Decision Receipt.
- Describe independent signature verification with public keys authenticated
  through an out-of-band channel. Issuer-provided JWKS and keys bundled with a
  receipt are discovery material, not independent trust roots.
- State deployment-dependent availability and assurance limits explicitly.
  Avoid describing planned features as available.

## Review

Before submitting a change, read the edited pages in context, check links and
examples, and confirm the wording matches the current public API and the
Receipt Reference. Keep private account details, credentials, and internal
process notes out of this repository.
