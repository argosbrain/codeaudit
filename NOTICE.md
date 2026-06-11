# Notice & Terms

© ManageVendors Inc. (operating as **ArgosBrain**). All rights reserved.

## Scope of this repository
This repository contains **two kinds of content, both publishable**:

1. **Specification & documentation** (`README.md`, `METHODOLOGY.md`,
   `VERIFY.md`, `schema/`) — the public, citable specification of the
   ArgosBrain Code Audit. You may reference and quote it with attribution
   for the purpose of understanding or verifying audit scores.

2. **Published audit records** (`registry/`, `reports/`) — factual,
   signed score summaries published by project owners through the
   ArgosBrain service. Each record states the code it describes and the
   commit it was produced from.

## What this repository does NOT contain
- It does **not** contain the ArgosBrain engine or any of its source.
  The engine is **proprietary** and distributed exclusively as signed
  binaries via argosbrain.com.
- It does **not** contain any audited project's source code, file paths,
  or exploitable finding details. Only score-level summaries are
  published.

## Integrity
Audit records are written **only** by the ArgosBrain publishing service
after signature and publisher validation. A file placed in `reports/` or
`registry/` by any other means, or lacking a valid ed25519 signature, is
not a valid ArgosBrain audit and should be disregarded.

## No warranty / not a certification
An ArgosBrain Code Audit score is structural, deterministic evidence —
**necessary but not sufficient**, and **not a security or compliance
certification**. It is provided without warranty. High-stakes decisions
should pair it with human review and, where applicable, an accredited
audit.

Contact: https://argosbrain.com
