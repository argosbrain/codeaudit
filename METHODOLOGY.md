# Methodology

How every number in an ArgosBrain Code Audit is computed, and why you can
trust a published score without trusting the publisher.

This document is the public, citable specification of the audit. It is
written so that a reviewer, an acquirer, or an academic partner can
understand exactly what each grade means and what it does **not** mean.

---

## 1. Principles

1. **Deterministic.** The same code at the same commit always produces
   the same report, byte for byte. No randomness, no LLM at audit time,
   no network call. This is what makes a score *reproducible* and
   therefore *verifiable by a third party* (§6).
2. **Reachability over pattern-counting.** A dangerous pattern is only
   counted as Risk if an **untrusted input** can structurally reach it.
   A `curl | sh` in a comment, a deserialization of a trusted local
   file, or a scanner's own pattern literals are surfaced for awareness
   but do **not** inflate Risk.
3. **Exposure is honest; Risk is weighted.** The report lists the full
   set of candidate findings (we hide nothing), and separately reports
   the severity-weighted, reachable Risk. A reader sees both.
4. **Truth in labeling.** A clean result is reported as *"no findings"*,
   never as *"pass"* or *"certified"*. Each compliance line states the
   exact patterns screened, so absence of findings is read as
   "these checks didn't fire", not "compliant".
5. **Not a certification.** Structural, static evidence is necessary but
   not sufficient. The report says so, on its face.

---

## 2. The two headline numbers

### AI Slop %
`(dead symbols + reachable half-finished stubs) / total symbols`, rounded.
"Dead" = a symbol with no inbound caller after framework-dispatch is
accounted for (route handlers passed to a framework, components rendered,
callbacks registered — these are *alive* even with no direct call edge).
"Stub" = `todo!()`, `unimplemented!()`, `NotImplementedError`, a
`throw new Error("not implemented")`, a placeholder key, etc., on a live
call path.

### Risk % + band (Low / Medium / High)
Severity-weighted count of findings that are **reachable from an untrusted
source** (an HTTP handler / request entry point), after dropping:
- findings in client-side files that aren't a server attack surface,
- host-pinned outbound requests (a literal external host, not attacker-
  redirectable),
- any finding with no untrusted-input path.

Severity ceiling per kind (Critical → High → Medium) is taken from a
fixed `finding_meta` table, so a genuinely critical reachable finding can
never be silently capped down.

---

## 3. Codebase maturity grades

Per category, one of **Strong / Satisfactory / Moderate / Weak** — *not*
a single pass/fail (the audit-industry convention). Each non-Strong row
carries a one-line reason. Categories:

| Category | What it checks |
|---|---|
| Authentication & Access Control | auth-bypass reachability on state-changing paths |
| Injection & Input Handling | SQLi / cmd / LDAP / XXE reachable from input |
| Cryptography & Key Management | weak algorithms, IV reuse, disabled TLS verify |
| Secrets & Credentials | hardcoded secrets, cloud keys, private-key blocks |
| Memory Safety | unsafe blocks / overflow surfaces (severity-weighted) |
| Supply Chain | build-time fetch-and-execute (`curl \| sh`), obfuscated build scripts |
| Configuration | insecure flags, permissive CORS, cookie flags |
| Data Privacy & PII Handling | PII reaching a log/print sink (CWE-532) |
| AI-Generated Code Integrity | reachable "fake-done" stubs left by a generator |
| Prompt-Injection Surface | user input → LLM-call paths from a remote source |
| LLM Cost Efficiency | LLM calls nested in loops (cost amplification) |
| Dead Code & Maintainability | dead/unreachable symbol ratio |

## 4. Reachability

For each candidate sink, the engine walks the call graph backward up to a
fixed depth looking for a **source marker** (HTTP handler / request
entry). "Reachable" means a call-graph path exists from a source to the
sink — it does **not** prove tainted data flows along it (a sanitiser may
neutralise it). "Unreachable within the horizon" is structural evidence
the sink can't be driven from a known external source. For field-level
taint, pair with a dedicated taint tool. Limits stated, not hidden.

## 5. Language coverage

The structural analysis is universal across the supported languages
(Rust, TypeScript/JavaScript, Python, Go, Java, C, C++, C#, Kotlin,
Scala, Ruby, Swift, PHP, Solidity). Per-language details (keywords, test-
file conventions, parser fidelity) are continuously hardened; coverage
gaps for a given language degrade gracefully (that language's symbols are
simply absent) rather than producing wrong numbers.

---

## 6. Integrity & verification — why a score is trustworthy

### Signing
Every report body is **deterministic** and **ed25519-signed**. The
signature covers the score-level content (the same content published
here). A reader can verify the signature against ArgosBrain's published
public key.

### Reproducibility (the key property)
Because the report is byte-reproducible, a third party with legitimate
access to the code can **re-run the audit on the same commit and obtain
the identical signed report.** Two independent runs producing the same
signature is cryptographic evidence that the published score was not
fabricated — *without ArgosBrain ever seeing the code.*

### What the registry guarantees
- **Authenticity:** the entry was produced by a genuine ArgosBrain
  engine (signature) — not hand-typed.
- **Provenance:** the entry is bound to a specific commit + date +
  publisher.
- **Append-only history:** prior entries are retained.

### What the registry does NOT guarantee on its own
- That the score matches the *current* code (see freshness 🟢/🟠).
- That the publisher's claimed project ownership is independently proven
  for the free, self-published tier (the directory shows the publisher
  and date; a viewer judges accordingly). Higher-assurance verification
  is performed by whoever re-runs the audit on the code (§6
  Reproducibility).

### The registry is written only by the ArgosBrain service
Reports reach this repository exclusively through the ArgosBrain
publishing service, which validates the signature and the publisher
before writing. The repository is not open to arbitrary pushes; a
human-authored file in `reports/` without a valid signature is not a
valid audit.

---

## 7. Governance & standard

ArgosBrain Code Audit is intended to be a **citable, stable standard** —
the grades, the categories, and this methodology are versioned, so a
report from any date can be interpreted against the spec that produced
it. Institutional partners (universities, standards bodies, regional
representatives) referencing the standard should cite this document by
its commit.

---

*Specification maintained by ArgosBrain (ManageVendors Inc.). The audit
engine is proprietary and distributed as signed binaries; this document
specifies the public, score-level output only.*
