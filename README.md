# ArgosBrain Code Audit — public registry

**A public registry of signed, reproducible code-audit scores.**
Maintained by ArgosBrain (ManageVendors Inc.).

> **What this repository is:** a public, append-only registry of
> **audit *scores*** — never source code, never finding locations.
> Each entry is a deterministic, ed25519-signed summary produced by the
> ArgosBrain engine on a real codebase, published by the project's owner.
>
> **What this repository is NOT:** it is not the ArgosBrain engine
> (proprietary, distributed as signed binaries), and it never contains
> anyone's source code, file paths, or exploitable finding details. A
> published audit shows *the score and what was checked* — the map a
> reader needs to trust the code, not the map an attacker needs to break
> it.

---

## Why this exists

Two audiences, one number neither could trust before:

- **People who write code** (especially AI-assisted) want a credible,
  shareable way to say *"my codebase is measured, not vibes."*
- **People who buy / depend on code** (acquirers, procurement, clients,
  downstream maintainers) want to verify that a codebase is held to a
  real standard — **without being handed the source to inspect.**

A self-claimed "our code is clean" is worth nothing. An ArgosBrain Code
Audit score is worth something because it is **deterministic** and
**reproducible**: the same code at the same commit always produces the
same signed score, so anyone with legitimate access to the code can
re-run the audit and confirm the published number — byte for byte.

---

## How to read a score

Every published audit carries:

| Field | Meaning |
|---|---|
| **AI Slop %** | dead weight + half-finished stubs (how *unfinished* the code is) |
| **Risk %** + band | severity-weighted danger **reachable from untrusted input** (not a raw pattern count) |
| **Codebase maturity** | per-category grades — Strong / Satisfactory / Moderate / Weak — for Auth, Injection, Crypto, Secrets, Memory Safety, Supply Chain, Config, Data Privacy, AI-Generated Code Integrity, Prompt-Injection Surface, LLM Cost Efficiency, Dead Code |
| **Commit** | the exact git commit the score is for |
| **Date** | when the audit ran |
| **Freshness** | 🟢 recent · 🟠 aging (the code may have moved on since — re-run to refresh) |
| **Signature** | ed25519 signature over the deterministic report body |

**Exposure vs Risk — read this once.** The audit lists the full surface
of *candidate* patterns (honest — nothing hidden), but **Risk only counts
what an external attacker can actually reach.** A flagged pattern with
"no untrusted-input path found" is shown for awareness, **not** as a
proven exploit. The score tells you what was checked *and* what the
analysis concluded.

A score is **not a certification.** It is structural, deterministic
evidence — necessary, not sufficient. Pair a high-stakes decision with a
human review.

## How freshness works

A code audit reflects the code **at one commit, on one date.** Code
moves on. After a period without a re-run, an entry's freshness turns
from 🟢 to 🟠 — *not* "expired" (the score was true when produced), just
"this may no longer reflect the current code; re-run to refresh."
We never show 🟢 on a stale audit. Truth in labeling.

## How to trust a score (verification)

ArgosBrain audits are **reproducible**: re-running the engine on the same
commit yields a byte-identical signed report. So a score can be confirmed
**by anyone who has legitimate access to the code** — without ArgosBrain
ever seeing it.

- **Open-source / public repos:** the code is already public; anyone can
  re-run and compare.
- **Private repos (e.g., an acquirer in due diligence):** the party that
  legitimately receives the code re-runs the audit and checks the
  signature matches the published one. The seller never hands the code to
  ArgosBrain; the buyer verifies for themselves, at the moment it matters.

This is the property no cloud scanner can offer: **a trustworthy audit
that never requires surrendering your source.**

See [VERIFY.md](VERIFY.md) for the exact steps.

## How a score gets here

1. A project owner runs `argosbrain audit` on their codebase (locally —
   the source never leaves their machine).
2. They choose **Publish**. The ArgosBrain client sends **only the
   score-level report** (the fields above — no source, no file:line) to
   the ArgosBrain publishing service.
3. The service verifies the signature and the publisher's ownership of
   the project, then writes the entry to this registry and to the public
   directory.

The registry is written **only by the ArgosBrain service** — never by
arbitrary pushes. See [METHODOLOGY.md](METHODOLOGY.md) for the integrity
model.

---

## Layout

```
registry/index.json        # the public directory: one row per project
reports/<project-id>/
    latest.json            # latest score-only signed report
    <commit>.json          # historical entries (append-only)
schema/report.example.json # the score-only report shape (no findings/locations)
METHODOLOGY.md             # how every number is computed + the integrity model
VERIFY.md                  # how to reproduce + verify a published score
```

## Links

- Engine & dashboard: https://argosbrain.com
- Methodology: [METHODOLOGY.md](METHODOLOGY.md)
- Verification: [VERIFY.md](VERIFY.md)

---

*ArgosBrain is the code-native memory + audit engine for AI coding
agents, distributed as signed binaries. This registry publishes audit
scores only; the engine and all source remain proprietary.*
