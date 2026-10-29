# 09 · Polygon — A Missing Balance Check Worth $9.27B

> 🎫 **Credit**
> **Researchers:** Leon Spacewalker (primary); Whitehat2 (secondary, awarded 500,000 MATIC)
> **Target:** Polygon MRC20 contract (gasless MATIC transfers)
> **Platform:** Immunefi · **Year:** 2021 (December)
> **Payout:** **$2,200,000** (exceeded Polygon's max critical bounty)
> **Original writeup:** [Polygon Lack-of-Balance-Check Bugfix Postmortem ($2.2M) — Immunefi](https://medium.com/immunefi/polygon-lack-of-balance-check-bugfix-postmortem-2-2m-bounty-64ec66c24c7d)

---

## 🎯 TL;DR

Polygon's `transferWithSig()` — the function powering *gasless* MATIC transfers — was **missing a balance/allowance check**. That single absent `require()` meant an attacker could move MATIC they didn't have, draining the ~9.27 **billion** MATIC held by the contract.

---

## ⚙️ How it works

Gasless transfers let a user sign an authorization bundle (operator, amount, nonce, expiration) and have an operator submit it and pay gas. The transfer then executes on the signer's behalf — but the implementation never verified the signer actually *had* the balance:

```solidity
function transferWithSig(
    address operator,
    uint256 amount,
    uint256 nonce,
    uint256 expiration,
    bytes   signature
) {
    // ❌ MISSING:  require(balanceOf[from] >= amount);
    // With no balance check, an attacker transfers MATIC they don't own.
    // Repeat → drain the entire contract.
}
```

- **At risk:** $736M+ at submission time; up to **9,276,584,332 MATIC** drainable.
- **Real-world sting:** before the patch fully deployed, a malicious actor exploited the bug to steal ~801,601 MATIC (~$2M). Polygon shipped a **hardfork** on Dec 5, 2021 to close it.

---

## 💡 Why it's brilliant

- **The ultimate "boring bug, giant blast radius."** There's no clever primitive here — just one validation that should exist and doesn't. The skill is *reading the right contract closely enough to notice the absence.*
- **Signature-based flows hide missing checks.** `transferWithSig` *looks* safe because it verifies a signature — and a hunter's eye slides right past the fact that a valid signature says nothing about sufficient balance. Elite review means auditing what *isn't* in the code.
- **Payout above the cap.** Polygon paid **$2.2M — more than its stated maximum** — because the severity demanded it. A reminder that caps bend for true criticals.

---

## 💰 Payout & impact

- **$2,200,000** to Leon Spacewalker; **500,000 MATIC** (~$1.26M at the time) to Whitehat2.
- Up to 9.27B MATIC / $736M+ at risk; a live partial exploit forced an emergency hardfork.

---

## 🔗 Sources

- [Immunefi — Polygon Lack-of-Balance-Check Postmortem ($2.2M)](https://medium.com/immunefi/polygon-lack-of-balance-check-bugfix-postmortem-2-2m-bounty-64ec66c24c7d)
- [CoinDesk — Polygon discloses patched exploit that put $9B MATIC at risk](https://www.coindesk.com/tech/2021/12/29/polygon-discloses-patched-exploit-that-put-9b-matic-at-risk)
- [Tom's Hardware — Polygon hard fork fixed $2M bug](https://www.tomshardware.com/news/polygon-hard-fork-bug-endangered-2-million)

> All credit to **Leon Spacewalker** (and Whitehat2). This page is a study summary — read the original.
