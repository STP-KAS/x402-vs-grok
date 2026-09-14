# GROK REASONING

## The X post is the right ask

Luke asked for agents to go through it **before** final v1. That sentence is more honest than the tag `v1.0.0-rc.1` sitting next to `spec/kaspa-x402-v1.md` still marked **draft**.

## What is not delusional

- Distinguishing HTTP 402 (1997 status code) from x402 (a specific envelope).
- Native KAS as the asset, not USDC-on-Base cosplay.
- Optional facilitator. UTXO servers can verify without Coinbase CDP.
- Claim successor value lock. They read the Toccata hole and did not copy the broken example pattern on the continuation.
- Mainnet gates written down: audit, independent evidence, durable store, distributed admission, live proof. They did not declare victory.
- Hosted `/supported` advertises `kaspa:testnet-10` only.

## What is delusional if you say it out loud

- “Kaspa is in x402.” Upstream README lists EVM/SVM/…/Keeta. Not Kaspa.
- “CAIP `kaspa:` is registered.” PR 193 is open.
- “v1.” RC + draft spec + open gates.
- “Accepted means paid forever.” GHOSTDAG accept is not a max-depth finality claim.
- “500 sompi per call is an L1 micropayment UTXO.” It is a voucher against an escrow that keeps a 10M sompi-class reserve.
- “Stables will work the same.” Luke already said no. Code says no.
- “k402 is x402.” Custom `kaspa-channel`. kccs#4 open, dirty, not adopted.
- “Windows `npm test` matches CI.” It does not.

## Trust

Direct mode removes **facilitator-as-money**. It does not remove:

- a lying or lagged RPC
- a crashed in-memory store
- a server that takes T and never delivers remaining calls (buyer signed the ceiling)
- a timeout race (claim vs refund)
- a Worker operator

L402/Lightning has years of production and preimage-tied delivery. This has a TN10 harness and mock examples. Stronger settlement *shape* (L1 UTXO) is not the same as stronger *operations*.

Coinbase x402 is weaker on sovereignty (USDC, often CDP) and stronger on “it runs on mainnet today.” Do not copy their asset. Do not copy their “we shipped” as your status.

## Stables, firmly

Master file: skip centralised stablecoins for dapps. Fill is not a business. The jar is not Nakamoto.

x402-the-protocol is chain-agnostic. That does **not** mean Kaspa x402 should speak USDT. A till that quotes EUR and settles KAS is a till. A binding that settles USDC is Coinbase’s binding. A reserved kUSD with no capital is a seat for a dollar that does not exist.

If Kaspa ever has a native unit that is not KAS, it is a **new `asset` + new settlement path + new audit**. Not a flag on `kaspa-exact-v2`.

## Compiler pin

They compiled with v1.0.0 and left `pragma silverscript ^0.1.0`. The master file already caught this class of error on STP desks. Same class here. The fixture JSON pins compiler commit `3ed9733` and bytecode hashes — that is the real pin. The pragma is a footgun for the next person who compiles “whatever ^0.1.0 means today.”
