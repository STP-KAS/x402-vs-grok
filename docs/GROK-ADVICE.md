# GROK ADVICE

## Do

1. Treat [elldeeone/kaspa-x402](https://github.com/elldeeone/kaspa-x402) `v1.0.0-rc.1` as the Kaspa **x402 v2 envelope**.
2. Build against `kaspa:testnet-10` only.
3. Prefer `exact` / `standard-native` until you have a reason to lock capital in escrow.
4. Verify successor `value` on every covenant path you copy. They did it on claim. Copy **that**, not the pragma.
5. File Windows bugs: `.gitattributes` `*.sil text eol=lf`, skip Unix modes on win32, don’t `fsync` in a way that EPERMs the whole proof.
6. Update [kaspa-master-file](https://github.com/STP-KAS/kaspa-master-file) R-X402 from “TN10 alpha” to **`v1.0.0-rc.1`, mainnet blocked**.

## Do not

1. Invent a fourth 402 dialect.
2. Call k402 / KCC-0402 / kns HTTP 402 / kascade “x402.”
3. Enable `allowMainnet`.
4. Put USDC, USDT, tPEG, or kUSD in this `asset` field.
5. Pre-sign batch **T** beyond work you are willing to lose to a claim.
6. Release mainnet secrets to a Worker that trusts one RPC.
7. Quote `accepted` as irreversible.

## v1 exit criteria (theirs, restated as fail-closed)

From `docs/mainnet-readiness.md`, plus this pass:

| Gate | Status 2026-09-14 |
| --- | --- |
| Independent audit | open |
| Consensus cross-check vs rusty-kaspa | CI does this on Ubuntu; not re-run here |
| Independent chain evidence | open (they admit one TN10 source) |
| Durable store + lock manager | open (in-memory is a fixture) |
| Distributed admission | open (Worker leases un-load-tested) |
| Operational recovery runbooks | claimed in docs; not independently drilled |
| Fresh funded TN10 18-flow | they published 13 Sep; this agent did not repeat |
| Hosted paid canary after each Worker change | `/health` live; this agent did not pay |
| CAIP merged or labelled unregistered everywhere | PR open |
| Windows-reproducible `npm test` | **fail** on default checkout |
| `pragma` matches compiler tag | **fail** (`^0.1.0` vs v1.0.0) |

## Message back to Luke

The binding is the one to review. The Windows checkout cannot reproduce launch-identity hashes. CI is Linux-only. `allowMainnet` is a boolean. Batch expiry is a race. Native KAS is the right asset. Do not take “v1” until the gates you already wrote are closed — and add `.gitattributes` so the next agent does not fail the first test for a `\r`.
