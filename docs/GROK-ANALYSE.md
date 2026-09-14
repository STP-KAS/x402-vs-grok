# GROK ANALYSE

## Object

[elldeeone/kaspa-x402](https://github.com/elldeeone/kaspa-x402) is a **proposed Kaspa network binding** for **x402 v2**. It is not a new L1, not a KIP, not a stablecoin, and not “HTTP 402 plus a Kaspa tx.”

x402 (the protocol) standardises:

1. how a server **requests** payment (`PaymentRequired`)
2. how a client **authorises** it (`PaymentPayload`)
3. how the server **verifies and settles** (`SettlementResponse`)

Transports: HTTP headers `PAYMENT-REQUIRED` / `PAYMENT-SIGNATURE` / `PAYMENT-RESPONSE` (base64 JSON), and MCP `_meta` keys. Historical `X-PAYMENT` is out.

Kaspa x402 plugs **native KAS** into that envelope.

## Schemes shipped

| Scheme | Extra.binding | Extra.profile / template | Settlement shape |
| --- | --- | --- | --- |
| `exact` | `kaspa-exact-v2` | default `standard-native`; optional `additive` | One-shot. Ordinary P2PK output, or KIP-10 successor delta. |
| `batch-settlement` | `kaspa-escrow-v3` | `kaspa-x402-escrow-v4` | Fund once. Sign lifetime ceiling T. Partial claims. Top-up. DAA refund. |

`upto` and `auth-capture` exist upstream. This repo **refuses** to ship them under those names because Kaspa scripts cannot make a once-valid tx become invalid at an expiry instant. Batch refund is a **race** after DAA, which they admit in `docs/native-profile-boundary.md`.

## Asset

`asset` is `"KAS"`. Amounts are canonical decimal sompi strings. Display layers may say tKAS. Wire must not use floating KAS.

Luke (same thread as the v1-ask): a future Kaspa stablecoin needs **its own asset binding**. That is the only non-delusional stables sentence.

## Networks

CAIP-2 **syntax**: `kaspa:testnet-10`, reserved `kaspa:mainnet`.  
CAIP **registry**: [namespaces#193](https://github.com/ChainAgnostic/namespaces/pull/193) open since 27 Jul 2026. Not merged.

v1 RC1 runtimes require `allowMainnet` for the reserved name. Docs: mainnet blocked.

## Covenant (batch)

`contracts/kaspa-x402-escrow-v4.sil`

- `pragma silverscript ^0.1.0` — language id in the source. Compiled with **v1.0.0** `3ed9733`. Pin hygiene is wrong; bytecode pin is explicit in fixtures.
- No `validateOutputState`. No `readInputState`. No `State[].split()`.
- Claim: singleton 1→1, voucher = SHA-256(domain ‖ network ‖ covenantId ‖ T_le64), `outputs[1].value == inputValue - claimAmount`, payout `<= claimAmount` (fee from **server** output).
- Top-up: both parties SIGHASH_ALL, successor value **>** input.
- Refund: `tx.daa >= timeout`, 1→0 same-ID, refund output `<=` input (fee from buyer remainder).

Application-layer A/S/T/V/R (`0 <= S <= A <= T`, `(T-S)+R <= V`) is **not** all on-chain. The script sees claimed cumulative and voucher T. **A** is a server-store invariant.

## Facilitator

Optional. Direct mode is the point: a server with a node can verify. Hosted demo is a Cloudflare Worker (`chainBroadcastMode: pnn`). A facilitator that widens capability is supposed to be intersected with server kinds. Hosted operators still need auth, rate limits, tenant isolation — they say this.

## Relation to Kaspa L1

| L1 fact (master file) | This binding |
| --- | --- |
| Toccata live (KIP-16/17/20/21) | Uses KIP-10 additive, KIP-20 covenant id, KIP-17 spend rules |
| SilverScript v1.0.0 pin | Fixture compiler commit `3ed9733` |
| #234 foreign state | Unused (good) |
| #243 no compute budget in artifact | Builder guesses 9999/10000 |
| #249/#250 split tuples | Unused (good) |
| Amount not locked by validateOutputState | Claim successor **does** `require(value == …)` |
| 10 BPS, not 100 | Not claimed |
| DAGKnight not shipped | Not used |
| Skip centralised stables | `asset: KAS` only |
