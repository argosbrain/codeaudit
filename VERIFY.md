# Verifying a published audit

An ArgosBrain Code Audit score is **reproducible**: re-running the engine
on the same commit produces a byte-identical, ed25519-signed report. That
lets you confirm a published score **yourself**, without ArgosBrain ever
seeing the code.

There are two checks, from cheapest to strongest.

## 1. Signature check (anyone, no code needed)
Every published report carries an ed25519 signature over its deterministic
body. Verify it against ArgosBrain's published public key to confirm the
entry was produced by a genuine ArgosBrain engine and not altered.

This proves **authenticity** (real engine, untampered) — it does not, by
itself, prove the score matches the code. For that, do check #2.

## 2. Reproduce on the code (anyone with legitimate code access)
This is the strong check, and the one that matters for an acquirer or a
downstream maintainer.

1. Get the engine: install ArgosBrain (https://argosbrain.com). The audit
   feature requires an active subscription — one plan, unlimited projects,
   so you can verify as many codebases as you need.
2. Check out the exact commit named in the published report.
3. Run `argosbrain audit`.
4. Compare the signature in *your* report to the signature in the
   published report.

- **Signatures match** → the published score is honest, proven
  cryptographically. The publisher could not have fabricated it.
- **Signatures differ** → the code at that commit does not produce the
  published score. Treat the badge as unverified.

### Who runs check #2, and when
- **Open-source / public repos:** the code is public — anyone can do it.
- **Private repos:** the party that legitimately receives the code (e.g.,
  an acquirer during due diligence, a client during handover) runs it on
  the code they were given. **The owner never sends the source to
  ArgosBrain** — verification happens entirely on the verifier's side.

This is the design point: a trustworthy audit that **never requires
surrendering your source** to a third-party cloud.

## Freshness
A matching signature confirms the score for **that commit**. If the
project has since moved on, the published entry shows 🟠 (aging) — re-run
on the current commit for a current score.

---

*Reproducibility + signing replace "trust us" with "verify it yourself."*
