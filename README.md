> **Experimental only. Not a product.** There is no spendable L1 stable on Kaspa, and no credible alternative on the horizon. Until the unit of account and the sequencing path are settled, production dapps are not a useful allocation of time or capital. [KASPAglobal](https://x.com/kaspaglobal/status/2100536064683176270) · [DISCLAIMER.md](DISCLAIMER.md)

# x402 vs grok

**Not Kaspa core. Not a KIP. Not an audit. Not a v1 rubber stamp.**

Independent Grok pass of [elldeeone/kaspa-x402](https://github.com/elldeeone/kaspa-x402), answering Luke Dunshea’s request that humans **and agents** read the code, build against it, test the assumptions, and try to break it before final v1.

Source post: [x.com/elldeeone/status/2099316438704312512](https://x.com/elldeeone/status/2099316438704312512)

Intel used: [STP-KAS/kaspa-master-file](https://github.com/STP-KAS/kaspa-master-file) freeze **13 Sep 2026**, plus live GitHub / X / TN10 API / this machine’s test run. PegLab, Gramlane, and Ishum were **not** used as sources of truth here.

| Pin | Value |
| --- | --- |
| Review date | 2026-09-14 |
| Tag | `v1.0.0-rc.1` @ `040b1ec8335abadbb3c69cf1ea720ae45816b0f7` |
| Hosted gateway | `1.0.0-rc.1` on [demo.kaspa-x402.org](https://demo.kaspa-x402.org) |
| Compiler they compiled with | SilverScript **v1.0.0** `3ed973335b59269293564805cc2c58a14595ec03` |
| CAIP | [ChainAgnostic/namespaces#193](https://github.com/ChainAgnostic/namespaces/pull/193) **open, not merged** |
| Master-file pin | updated this pass to **v1.0.0-rc.1, TN10, mainnet blocked** ([kaspa-master-file@662c77a](https://github.com/STP-KAS/kaspa-master-file/commit/662c77a)) |

**Verdict in one line:** this is a real x402 **v2 binding** for native KAS, and it is **not** v1, **not** mainnet, **not** in the upstream x402 registry, **not** a registered CAIP namespace. Bind this envelope. Do not call RC1 “Kaspa has x402.”

**Windows clone-and-test (14 Sep evening):** the RC.1 breaks below were filed as [elldeeone/kaspa-x402#11](https://github.com/elldeeone/kaspa-x402/issues/11) and merged as [PR #12](https://github.com/elldeeone/kaspa-x402/pull/12) (`216ad77`). Luke’s narrower disk-sync split is the one that landed (directory `fsync` skipped on Windows; file `fsync` fail-closed). Fresh clone of merged `main` on the original Windows box: `npm test` green, `proof:offline` 24/24. Public verdict: [https://sixpack.wtf](https://sixpack.wtf). Long form: [STP-KAS/grok-heavy-test](https://github.com/STP-KAS/grok-heavy-test). The test log below is the RC.1 tag, which still fails.

Longer notes: [GROK-TEST](docs/GROK-TEST.md) · [GROK-ANALYSE](docs/GROK-ANALYSE.md) · [GROK-REASONING](docs/GROK-REASONING.md) · [GROK-ADVICE](docs/GROK-ADVICE.md) · [EVIDENCE](docs/EVIDENCE.md) · [ECOSYSTEM](ECOSYSTEM.md)

---

## grok test

Ran on Windows (PowerShell), Node **v24.19.0**, clone of tag `v1.0.0-rc.1`.

### What actually broke

**1. Default Windows checkout fails `npm test`.**  
`core.autocrlf=true`. No `.gitattributes`. `contracts/kaspa-x402-escrow-v4.sil` checks out as CRLF. Launch-identity SHA-256 of the source is pinned to LF:

| | SHA-256 of `.sil` |
| --- | --- |
| On-disk CRLF (this machine) | `ab88ec5da53d1e716bb1f1322182d48bbb2c9fed9b0078f9d718a022282f48c6` |
| LF-normalized (the pin) | `065dff5d0d02f3a09f56bab977a33d4e047f2ccec64c0d066a318d342797fcb0` |

`packages/covenant/test/covenant.test.ts` failed 2/13 until the file was rewritten without `\r`. After LF-normalize, the 27 covenant tests passed. CI cannot see this: `.github/workflows/ci.yml` is **`ubuntu-latest` only**.

**2. Unix file modes are asserted on Windows.**  
`scripts/proof-output-security.test.mjs` requires `fs.statSync(reportFile).mode & 0o777 === 0o600` (384). This machine got **438** (`0o666`). Windows does not keep Unix 0600. The rest of that file’s secret-scrub checks are a good idea; the mode assert is not portable.

**3. Offline proof then dies on `fsync`.**  
`npm run proof:offline` ran **22/22 named checks `ok: true`** (exact create/verify/settle, payment-id idempotency, exact replay `409 invalid_transaction_state`, batch genesis/voucher/claim/top-up/refund construction, fixture reproducibility against compiler `3ed9733`). Summary then: `"ok": false, "error": "EPERM: operation not permitted, fsync"`. The protocol checks passed. The Windows write-out did not.

That is a real break of the **reproducibility claim** (“locally: `npm ci && npm test`”). It is not a break of the covenant math.

### What held

| Check | Result |
| --- | --- |
| `@kaspa-x402/core` | 163 passed |
| `@kaspa-x402/covenant` after LF | 27 passed |
| `@kaspa-x402/server` | 214 passed |
| `@kaspa-x402/client` | 124 passed |
| `@kaspa-x402/facilitator` | 36 passed |
| `@kaspa-x402/cli` + mock examples | 6 passed; paid-http / paid-mcp / facilitator / recovery examples ran |
| `@kaspa-x402/demo-gateway` | 107 passed |
| `demo.kaspa-x402.org/health` | `ok`, `releaseVersion: 1.0.0-rc.1`, `exactProfile: standard-native`, `chainBroadcastMode: pnn` |
| `demo.kaspa-x402.org/supported` | `x402Version: 2`, `exact` + `batch-settlement`, **`kaspa:testnet-10` only**, asset `KAS` |
| Live report tiny exact txid `b46de1e1…` | TN10 API: `is_accepted: true`, version 0 |
| CAIP #193 | **open** |
| silverscript #234 / #243 / #249 / #250 | still the master-file holes |
| `npm audit` | 6 vulns (4 high). Production omit-dev: **fast-uri** high (SSRF / host confusion via ajv) |

### What this machine did not do

- No funded live `proof:live:check -- --live` (needs a TN10 wallet and RPC secrets).
- Did not recompile `.sil` with silverc here.
- Did not run `validate:tx-v1-consensus` against a rusty-kaspa checkout (CI pins `c338d495…`).
- Did not pay the hosted Worker with a real `PAYMENT-SIGNATURE`.

Their own 13 Sep funded harness is evidence **they** ran 18 flows. It is not evidence **this** agent did.

---

## grok analyse

**What it is.** A proposed **network binding** for [x402 v2](https://www.x402.org). HTTP 402 plus `PAYMENT-REQUIRED` / `PAYMENT-SIGNATURE` / `PAYMENT-RESPONSE`. MCP `_meta["x402/payment"]`. `x402Version: 2`. Asset **`"KAS"`** in sompi decimal strings. Two schemes:

| Scheme | Binding | On-chain |
| --- | --- | --- |
| `exact` | `kaspa-exact-v2` | Default `standard-native`: one merchant output of the advertised sompi. Optional `additive`: KIP-10 head successor delta **is** the payment; no second merchant output. |
| `batch-settlement` | `kaspa-escrow-v3` + template `kaspa-x402-escrow-v4` | Fund a KIP-20 singleton escrow. Buyer signs lifetime ceiling **T**. Provider claims. Top-up co-signed. Refund after absolute DAA. |

Facilitator is **optional**. Direct verification against a node is the intended path. Hosted `demo.kaspa-x402.org` is an operator gateway, not consensus.

**What it is not.**

- Not “anything that returns HTTP 402.” Luke is right. k402, KCC-0402, KASPACOM/x402-KAS, kaspahttp402/*, STP `kns` 402 routes — those are other objects. See [ECOSYSTEM.md](ECOSYSTEM.md).
- Not in `x402-foundation/x402` as a first-party Kaspa package.
- Not a registered `kaspa` CAIP namespace. Syntax `kaspa:testnet-10` is a **proposal**.
- Not mainnet. `kaspa:mainnet` is a reserved name. Gates in `docs/mainnet-readiness.md` are open.
- Not a stablecoin rail. Luke on the same thread: a future Kaspa stable would need **its own asset binding**. Do not bolt USDC/USDT/kUSD onto this and call it Kaspa x402.
- Not instant irreversible cash. Hosted exact finality is **`accepted`**. Batch TN10 policy is **30 selected-chain confirmations**. That is deployment policy, not GHOSTDAG finality.

The escrow **does** lock successor value on claim (`tx.outputs[1].value == inputValue - claimAmount`). It does **not** use `validateOutputState` / `readInputState`. It does **not** use `State[].split()`. Those master-file holes are avoided here, not fixed in the compiler.

`pragma silverscript ^0.1.0` on a contract compiled with **v1.0.0** is sloppy pin hygiene. The master file already warns that example pragmas lie.

---

## grok reasoning

### Delusion checks

**“Native Kaspa payments in the shared x402 standard.”** Envelope-compatible. **Not in the standard.** npm `@kaspa-x402/*@1.0.0-rc.1` plus a personal repo is a forked ecosystem until upstream lists Kaspa or rejects it in writing.

**“v1 release candidate.”** The tag exists. `spec/kaspa-x402-v1.md` still says **Status: draft** and talks about “current alpha.” RC ≠ v1. The X post asking agents to break it is the honest posture; the version number is ahead of the gates.

**“HTTP 402 = x402.”** False. This repo does not make that mistake. The field around it does. Naming a till or a CDN `x402-*` is how you mint a fourth envelope without writing one.

**Stables.** Master-file principle: skip centralised stablecoins for dapps. This binding’s `asset` is hardcoded `KAS`. That is the correct product. Coinbase x402 defaulting to USDC is **their** chain’s culture, not a requirement of HTTP 402. A Kaspa dollar with no reserves is PegLab without the depeg lesson. USDT-in does not vote in GHOSTDAG and does not make Tether into Kaspa money.

### Flaws that survive the tests

1. **Windows is not a first-class verifier.** Launch identity is a SHA-256 of source bytes. CRLF changes the bytes. An agent on Windows cannot reproduce the fixture until it knows the LF trick. That is a break of “read the code, run the tests.”
2. **`allowMainnet` is a boolean, not a gate.** Docs forbid mainnet. The type system does not check that audits, durable stores, or independent evidence exist.
3. **One RPC is not Byzantine-safe.** They say this. The hosted Worker still trusts one TN10 evidence source. Failover ≠ corroboration.
4. **Replay protection lives in the store.** Lose the store, replay reopens. In-memory stores are fixtures. Production store is an unclosed gate.
5. **Claim has no DAA cap; refund does.** After timeout, buyer refund and server claim **race**. They refused to ship upstream `upto` for this reason and shipped it for batch anyway.
6. **`accepted` is not cash-final.** Releasing protected API bytes at accept is a product choice. Do not sell it as irreversible.
7. **Compute budget is still guessed.** silverscript **#243** open. Builder uses `9999` free units / `10000` per budget. Live additive used `compute_budget: 10`. Formula drift vs consensus is a mainnet kill-if.
8. **Batch “micropayments” lock capital.** Vouchers can price at 500 sompi. On-chain outputs still face a conservative **10_000_000 sompi** policy. Paying 500 sompi per call is not a 500-sompi UTXO. Anyone who omits the escrow lockup is lying.
9. **Payment identifier is “should,” not must.** Exact still has txid+output identity. Clusters without a shared store can double-run protected work.
10. **Refund fee-from-principal.** `outputs[0].value <= input.value`. Successor claim path **does** lock value. Refund does not lock an exact remainder. That is the fee-from-principal pattern the master file warns about, used on purpose for miner fees.

None of that makes the repo fake. It makes “v1” premature.

---

## grok advice

**Bind this envelope. Do not invent a fourth 402.** Steal lock/voucher *ideas* from k402 if you need a channel primitive. Credit Kali. Do **not** call k402 “Kaspa’s x402 v2.” Do **not** call [kccs#4](https://github.com/kaspanet/kccs/pull/4) adopted. KCC-0402 is an open, dirty PR.

### Kill-ifs (stop the line)

- `allowMainnet: true` before every gate in `docs/mainnet-readiness.md` is closed
- Foreign `readInputState` in the escrow
- `State[].split()` tuple syntax on silverc v1.0.0 (`#250` still open)
- Compiler pin floats off **v1.0.0 `3ed9733`**
- Claim/top-up successor without `tx.outputs[i].value == …`
- USDC / USDT / “kaspa dollar” / tPEG as `asset` on this binding
- Treating `accepted` as irreversible, or 10 BPS as 100 BPS
- Calling k402 / KCC-0402 / a till / a CDN “x402”
- Shipping in-memory stores or one RPC as Byzantine-safe
- Shipping a Windows-using implementer `npm test` without `.gitattributes` `*.sil text eol=lf`

### What v1 must prove, not assert

1. Independent covenant + replay audit (exact **and** batch), including the claim/refund race and store crash.
2. Independently corroborated chain evidence (their own mainnet gate).
3. Durable transactional stores + distributed admission under concurrent load.
4. Fresh funded TN10 run **and** hosted-Worker paid canary, explorer-checkable.
5. CAIP #193 merged **or** every public sentence says “proposed identifier, unregistered.”
6. Upstream: a real Kaspa mechanism package, or a rejected-with-reasons issue. Personal npm org forever is not “in the standard.”
7. Mainnet confirmation policy chosen and tested. `accepted`-only content release documented as **reorg risk**.
8. `pragma silverscript` pinned to the compiler you actually used.
9. CI on Windows, or `.gitattributes` + permission tests that skip Unix modes. **Done on merged `main` (`216ad77`).** Still required on any future tag.

### For agents building now

TN10 only. Native KAS. Prefer `standard-native` exact. Treat batch as **capital lock + signed ceiling**, not Lightning-grade finality. Do not pre-sign **T** beyond delivered work. Do not put mainnet KAS in this.

Master-file R-X402 now reads **v1.0.0-rc.1, TN10, mainnet blocked** (was alpha.10).

---

## Related reports

- Public verdict: [https://sixpack.wtf](https://sixpack.wtf)
- Send-this report: [STP-KAS/grok-heavy-test](https://github.com/STP-KAS/grok-heavy-test)
- Field (Parker, kaspanet, Kali, KASPACOM, kaspahttp402, STP-KAS): [ECOSYSTEM.md](ECOSYSTEM.md)
- Ishum is a till, not this protocol: [STP-KAS/x402-ishum](https://github.com/STP-KAS/x402-ishum)
- HTTP 402 costumes vs this binding: [STP-KAS/402-is-not-x402](https://github.com/STP-KAS/402-is-not-x402)
