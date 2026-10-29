# 🏆 Bug Bounty Hall of Fame

> A curated collection of the **10 coolest, highest-level bug bounty writeups** in security history — the findings that drained (or saved) hundreds of millions, broke new vulnerability classes, and set the standard for the entire industry.

Each entry is a **digestible breakdown** of a legendary disclosure: what the bug was, how the exploit chain worked, why it's brilliant, and what it paid — followed by a link to the **original writeup by the researcher who actually found it**.

---

## ⚠️ Read this first — attribution

**I did not find any of these bugs.** This is a *study collection* — my notes on the writeups that taught me the most, assembled so other people can learn from them in one place.

- Every entry **credits the original researcher by name/handle** and **links to their original disclosure**.
- The breakdowns here are **summaries and commentary**, not the primary source. **Go read the originals** — they're linked at the top and bottom of every file.
- All rights to the original research belong to the researchers and programs who published it.

There's one exception, clearly labeled: a [bonus entry](#-bonus-my-own-research) at the bottom is my *own* research, with its real (unglamorous) outcome stated honestly.

---

## 📜 The Collection

| # | Writeup | Researcher(s) | Class | Payout | Original |
|---|---------|---------------|-------|--------|----------|
| 01 | [Aurora — Infinite ETH Mint via `delegatecall`](writeups/01-aurora-infinite-eth-mint.md) | pwning.eth | Bridge accounting flaw | **$6,000,000** | [mirror.xyz](https://pwning.mirror.xyz/CB4XUkbJVwPo7CaRwRmCApaP2DMjPQccW-NOcCwQlAs) |
| 02 | [Wormhole — Uninitialized UUPS Proxy](writeups/02-wormhole-uninitialized-proxy.md) | satya0x | Uninitialized proxy | **$10,000,000** | [Immunefi](https://medium.com/immunefi/wormhole-uninitialized-proxy-bugfix-review-90250c41a43a) |
| 03 | [Optimism — Infinite Money via `SELFDESTRUCT`](writeups/03-optimism-selfdestruct-duplication.md) | Jay Freeman (saurik) | L2 state desync | **$2,000,042** | [Immunefi](https://medium.com/immunefi/optimism-infinite-money-duplication-bugfix-review-daa6597146a0) |
| 04 | [Dependency Confusion — Supply Chain Takeover](writeups/04-dependency-confusion.md) | Alex Birsan | Supply chain | **$130,000+** | [Medium](https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610) |
| 05 | [GitHub Enterprise — 4-Bug RCE Chain](writeups/05-github-enterprise-rce-chain.md) | Orange Tsai | SSRF → RCE chain | **$7,500** + Best Report | [blog.orange.tw](https://blog.orange.tw/posts/2017-07-how-i-chained-4-vulnerabilities-on/) |
| 06 | [HTTP Request Smuggling, Reborn](writeups/06-http-request-smuggling.md) | James Kettle | HTTP desync | **$200,000+** | [PortSwigger](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn) |
| 07 | [We Hacked Apple for 3 Months](writeups/07-we-hacked-apple.md) | Sam Curry & team | Infra / IDOR / ATO | **$288,500** | [samcurry.net](https://samcurry.net/hacking-apple) |
| 08 | [Web Hackers vs. The Auto Industry](writeups/08-web-hackers-vs-auto-industry.md) | Sam Curry & team | API / SSO / remote control | Responsible disclosure | [samcurry.net](https://samcurry.net/web-hackers-vs-the-auto-industry) |
| 09 | [Polygon — $9.27B Missing Balance Check](writeups/09-polygon-missing-balance-check.md) | Leon Spacewalker | Missing validation | **$2,200,000** | [Immunefi](https://medium.com/immunefi/polygon-lack-of-balance-check-bugfix-postmortem-2-2m-bounty-64ec66c24c7d) |
| 10 | [Chrome — ipcz/Mojo Sandbox Escape](writeups/10-chrome-ipcz-sandbox-escape.md) | "Micky" | Sandbox escape / RCE | **$250,000** | [SecurityWeek](https://www.securityweek.com/google-now-offering-up-to-250000-for-chrome-vulnerabilities/) |

**Total across these findings: well over $30,000,000 in bounties.**

---

## 🗺️ How to read each writeup

Every file follows the same shape so you can scan fast:

- **🎫 Credit box** — who found it, where, when, what it paid, link to the source
- **🎯 TL;DR** — the whole bug in three sentences
- **⚙️ How it works** — the exploit chain, with code
- **💡 Why it's brilliant** — my notes on what makes it elite-tier
- **💰 Payout & impact** — the money and the blast radius
- **🔗 Sources** — everything, so you can verify and go deeper

---

## 🧭 What these have in common

Study the list and a few patterns jump out — the lessons I keep coming back to:

1. **The biggest bugs are boring code in critical places.** A missing `require()` (Polygon), an uninitialized proxy (Wormhole), a module left off a denylist — simple flaws, catastrophic placement.
2. **Context beats complexity.** `delegatecall` and `SELFDESTRUCT` are documented EVM behavior. The finding is realizing *where* that behavior becomes a money printer.
3. **Chains beat single bugs.** Orange Tsai's $7.5K GitHub chain and Sam Curry's Apple campaign are famous not for one critical, but for *stitching* medium-severity flaws into catastrophe.
4. **New classes pay forever.** Birsan (dependency confusion) and Kettle (desync) didn't find one bug — they invented a *category*, then collected from everyone vulnerable to it.
5. **Speed is money in Web3.** Several of these were patched within *hours*. In the duplicate game, hours decide who gets paid (see the bonus entry).

---

## 🧪 Bonus: my own research

[**OpenAI Codex — JS REPL Sandbox Escape**](bonus/openai-codex-sandbox-escape.md) — a verified sandbox-escape RCE in OpenAI's Codex CLI (`node:vm` left off the module denylist → full escape → OAuth token theft). Honest outcome: it was closed as a **verified duplicate** because I submitted a few hours behind another researcher. Real bug, real technique, no payout. It's here because near-misses teach more than wins.

---

## 📄 License & use

The **summaries and commentary** in this repo are released under [CC BY 4.0](LICENSE) — share and adapt with credit.

The **original research** (linked in every file) belongs to its respective authors and programs. This repository claims no ownership over it and exists to point people toward it. If you are one of the researchers featured here and want an entry changed or removed, open an issue.

---

*Curated by [Young-Syre](https://github.com/Young-Syre). Built as a learning resource for the bug bounty community.*
