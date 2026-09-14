# Ecosystem — same test, same pushback

Same job as the kaspa-x402 review, pointed at **Parker**, **kaspanet**, **contributor 402**, and **STP-KAS**. Not a line-by-line audit of rusty-kaspa. Honest labels only. Live GitHub 2026-09-14.

PegLab / Gramlane product pitch is listed, not used as law.

---

## grok test (field)

| Probe | Result |
| --- | --- |
| `elldeeone/kaspa-x402` | tag `v1.0.0-rc.1` 13 Sep; `pushed_at` 14 Sep; this machine broke Windows `npm test` (see README) |
| CAIP #193 | **open** |
| `kaspanet/kccs` #4 KCC-0402 | **open**, `mergeable_state: dirty`, updated 27 Aug |
| silverscript #234/#243/#249/#250 | still open or closed-unmerged as in the master file |
| `KASPACOM/x402-KAS` | README superseded; last push 11 Aug |
| `Kali123411/k402` | last push **19 Jul**; no GitHub releases |
| `kaspahttp402/cascade` | **404**. Real name is **kascade**, pushed 14 Sep |
| `parker2017code` | 3 public repos; **no 402 repo** |
| `argent-lang/argent` | no tag, 0 releases |
| STP-KAS | 26 public; 0 stars; experiments + docs |

---

## grok analyse

There are **three different objects** people call “402” on Kaspa:

1. **x402 v2 binding** — elldeeone/kaspa-x402. Envelope + native KAS. TN10 RC.
2. **HTTP 402 + a Kaspa lock** — Kali k402 / proposed KCC-0402. Channel primitive. Not the x402 v2 wire.
3. **HTTP 402 costume** — a 402 status or a route named 402, no x402 headers, no schemes.

If you flatten those, you cannot test anything.

---

## Parker (`parker2017code`)

| Repo | Push | Honest |
| --- | --- | --- |
| [kaspa-explained](https://github.com/parker2017code/kaspa-explained) | 2026-09-09 | Independent guide (kaspaexplained.com). Status lanes. **Not a 402 stack.** |
| [tn12-covenant-vault-demo](https://github.com/parker2017code/tn12-covenant-vault-demo) | 2026-05-15 | TN12 lab with claimed accepted txs. **Stale.** Master file: do not use TN12 for Toccata product work. |
| [remote-mcp-server-authless](https://github.com/parker2017code/remote-mcp-server-authless) | 2026-05-19 | Unrelated MCP. |

No Parker x402. Receipts and education. Unit = 1 sompi in the master-file reading of his V6 local pack — that is **not** this review’s job to re-journal.

---

## kaspanet (core)

| Repo | Honest |
| --- | --- |
| rusty-kaspa | Reference node. Toccata live. Tag **v2.0.1**. **No HTTP 402.** |
| silverscript | Compiler pin **v1.0.0**. Not a payment protocol. Holes #234/#243/#249/#250 still apply to anyone compiling escrows. |
| kips | No KIP-0402. |
| kccs | All **Draft**. KCC-0402 is **PR #4**, not a merged convention. |
| docs | Stale vs Sil v1.0.0. Not a 402 spec. |
| vprogs | Roadmap. Not payments. |

---

## Contributor 402

| Repo | Honest | Pushback |
| --- | --- | --- |
| **elldeeone/kaspa-x402** | Intended x402 v2 binding | TN10 RC. Not mainnet. Windows tests fail. Bind this. |
| Kali123411/k402 | HTTP 402 + `kaspa-channel` | Not x402 v2. Stale since Jul. Author has claimed mainnet open/close — **unverified here**. |
| Kali123411/kaspa-x402-router | USDC on Base → KAS inventory | **Not a bridge.** Operator inventory. Idle since Jul. |
| Kali123411/k402-mcp | Multi-asset MCP pay | Sweeping (BTC/LTC/DOGE/BCH/DASH/ETH/USDC). Last push Jul. |
| KASPACOM/x402-KAS | TN12 facilitator experiment | **Superseded.** Historical. |
| kaspahttp402/kaspa-x402 | archived 12 Sep | Custom 402 JSON. Not elldeeone. |
| kaspahttp402/kascade | “decentralized CDN” | Created ~yesterday. Pitch > proof. **cascade 404.** |
| argent-lang/argent | actor language | **No tag.** Not release-ready. |

---

## STP-KAS (this desk’s public GitHub)

Local experiments. 0 stars. Honest labels on the READMEs are often better than the titles.

| Repo | 402 / money claim | Pushback |
| --- | --- | --- |
| kaspa-master-file | Pins kaspa-x402 | Pin still says **alpha**; RC.1 exists. Fix the pin. |
| kns | HTTP 402 route | Shape, not x402 v2. Name uniqueness is indexer FCFS, not consensus. |
| ishum | kUSD reserved + USDT guest | Till. **Not x402.** See [x402-ishum](https://github.com/STP-KAS/x402-ishum). |
| kaspa-till | “reserved Kaspa L1 stable till” | Reserved ≠ issued. |
| xai-reasoning-3 | EUR invoice + optional KAS QR | Says not a dollar. Keep it that way. |
| kns-spec | KNS implementer kit | No 402 envelope. |
| argent-xai | Argent claim audit | Explicitly not argent-lang. |
| dagknight-test-grok | DK notes | Not consensus. |
| sixpack.wtf / mixer-concept / grok-kaspa-collab / windows-p2p-node-guide / Xai.Kaspa.node | sites, notes, node prompt | Out of 402 path. |
| kaachat-desktop / stp-kachat / kachat-test-with-silver | chat | Out of 402 path. |
| peglab-* / gramlane* | listed only | Not used as truth in the kaspa-x402 review. |

---

## grok reasoning (field)

The field is doing exactly what Luke complained about: hanging a payment story off HTTP 402 and saying “x402.”

Kali’s channel lock is a **primitive**. Steal it, credit it, do not rebrand it.

KASPACOM already pointed at elldeeone. Good. Do not revive TN12.

kaspahttp402’s burst of repos (kascade, spigot, flume, quorum) is **age in days**. Treat as pitch until there are explorer txids and an x402 v2 envelope, or admit they are a different protocol.

STP `kns` 402 and Ishum kUSD/USDT are **this desk’s** temptation to mint a fourth object. Refuse.

Argent without a tag is not a production app language. Master file already says so.

---

## grok advice (field)

1. One envelope: elldeeone/kaspa-x402.
2. One lock to steal: k402 / kccs#4 — as a primitive, not as “adopted KCC-0402.”
3. Kill-if: a fourth 402 dialect, TN12 product work, USDC-router as “Kaspa x402,” kascade as proven CDN, Argent as tagged, kUSD as money.
4. Parker: keep as education + receipt unit. Do not fork kaspaexplained.com.
5. Update the master-file x402 pin to RC.1.
6. What to actually try to break next (if you have TN10 funds): replay a paid exact receipt on a different path; present a k402 `kaspa-channel` offer to an x402 v2 client (must hard-fail); pay STP `kns` 402 and see that settlement is a plain transfer, not a covenant unique-name spend.
