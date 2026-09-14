# GROK TEST

Machine: Windows, PowerShell, Node v24.19.0, npm 11.17.0.  
Subject: [elldeeone/kaspa-x402](https://github.com/elldeeone/kaspa-x402) tag `v1.0.0-rc.1` (`040b1ec8335abadbb3c69cf1ea720ae45816b0f7`).  
Date: 2026-09-14.

## Commands

```
git clone --depth 1 --branch v1.0.0-rc.1 https://github.com/elldeeone/kaspa-x402.git
npm ci
git config --get core.autocrlf   # true
git ls-files --eol contracts/kaspa-x402-escrow-v4.sil   # i/lf  w/crlf
npm test                         # FAIL: covenant source SHA-256
# rewrite .sil without CR
npm --workspace @kaspa-x402/covenant test   # PASS 27
npm test                         # FAIL: proof-output-security 0o600 vs 0o666
npm run proof:offline            # 22 checks ok; summary EPERM fsync
```

## Break 1 — CRLF vs launch identity

The covenant launch identity is a SHA-256 of the `.sil` **bytes**. Git on this machine converted LF → CRLF. There is **no** `.gitattributes`.

| Bytes | SHA-256 |
| --- | --- |
| CRLF checkout | `ab88ec5da53d1e716bb1f1322182d48bbb2c9fed9b0078f9d718a022282f48c6` |
| LF (pinned `item.sourceSha256` / `ESCROW_V4_SOURCE_SHA256`) | `065dff5d0d02f3a09f56bab977a33d4e047f2ccec64c0d066a318d342797fcb0` |

Failing tests: `packages/covenant/test/covenant.test.ts`

- `reproduces current silverc genesis and successor bytecode`
- `binds the launch identity to the checked source and compiled base`

After deleting `\r` from the working tree (not committed), both passed. Bytecode pins themselves were fine; only the **source-file hash** moved.

CI (`.github/workflows/ci.yml`) runs `ubuntu-latest` only. Linux checkouts keep LF. Windows agents and Windows implementers are invisible to CI.

## Break 2 — Unix 0600 on Windows

`scripts/proof-output-security.test.mjs` line 116:

```js
assert.equal(fs.statSync(reportFile).mode & 0o777, 0o600);
```

Actual `mode & 0o777` = **438** (`0o666`). Expected **384** (`0o600`). Secret-scrub assertions in the same test are the real requirement; the mode check is POSIX-only.

## Break 3 — proof:offline fsync

`npm run proof:offline` printed 22 checks, all `"ok": true`, then:

```json
"summary": { "ok": false, "checkCount": 22, "error": "EPERM: operation not permitted, fsync" }
```

Protocol path: pass. Report write-out: fail closed on this OS.

## Workspace tests after LF-normalize

| Workspace | Tests |
| --- | --- |
| core | 163 pass |
| covenant | 27 pass |
| server | 214 pass |
| client | 124 pass |
| facilitator | 36 pass |
| cli + mock examples | 6 pass; examples emitted 200 / batch / facilitator settle / recovery |
| demo-gateway | 107 pass |
| scripts/* (node:test) | 17 pass, 1 fail (0600) |

Mock examples (no wallet):

- `examples/paid-http-api` — exact 200 + batch two charges `"50000"`
- `examples/paid-mcp-tool` — `scheme: batch-settlement`, `chargedAmount: "250000"`
- `examples/self-hosted-facilitator` — verify/settle mock tx `1b8a2e7d…`
- `examples/recovery` — exact replay `invalid_payload`; refund preview `4000000`

## Live network (no wallet)

| URL | Result |
| --- | --- |
| `https://demo.kaspa-x402.org/health` | `ok`, `releaseVersion: 1.0.0-rc.1`, `hostedExactSettlementEnabled: true`, `exactProfile: standard-native`, `chainBroadcastMode: pnn` |
| `https://demo.kaspa-x402.org/supported` | two kinds, `x402Version: 2`, networks **`kaspa:testnet-10` only**, asset `KAS`, bindings `kaspa-exact-v2` / `kaspa-escrow-v3` template `kaspa-x402-escrow-v4` |
| TN10 API `b46de1e1ffc502c69003043e81f7f3c98e49b402a836eb0012300487b2b77b7c` | `is_accepted: true`, `version: 0` (tiny exact from their 13 Sep report) |

Did **not** send a paid `PAYMENT-SIGNATURE` to the Worker.

## npm audit

`npm audit --omit=dev`: **fast-uri** high (SSRF / host confusion). Likely via **ajv** schema `$ref`. Not demonstrated reachable as an exploit here. Still a supply-chain fact on the RC tag.

Full tree: 6 vulns (4 high: fast-uri, miniflare, sharp, wrangler; 2 moderate: vitest / @vitest/mocker). Dev-tool vulns are not a protocol hole. fast-uri on the validator path is.

## Not run

- `proof:live:check -- --live` (needs funded TN10)
- `validate:tx-v1-consensus` (needs rusty-kaspa at `c338d495bec29e4dc8b5149f99e8db6fa916ed4a`)
- silverc recompile of `kaspa-x402-escrow-v4.sil`
