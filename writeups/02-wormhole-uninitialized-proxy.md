# 02 · Wormhole — Uninitialized UUPS Proxy

> 🎫 **Credit**
> **Researcher:** satya0x
> **Target:** Wormhole cross-chain bridge (Ethereum)
> **Platform:** Immunefi · **Year:** 2022 (February)
> **Payout:** **$10,000,000** — the largest single bug bounty payout ever recorded
> **Original writeup:** [Wormhole Uninitialized Proxy Bugfix Review — Immunefi](https://medium.com/immunefi/wormhole-uninitialized-proxy-bugfix-review-90250c41a43a)

---

## 🎯 TL;DR

Wormhole's bridge used a UUPS upgradeable proxy. A previous bugfix had reverted the original initialization, leaving the **implementation contract uninitialized**. Anyone could call `initialize()` on it, install their own Guardian set, and use that fake Guardian set to upgrade the logic — then `SELFDESTRUCT` the implementation and **permanently brick a bridge holding $736M**.

---

## ⚙️ How it works

Upgradeable proxies separate *storage* (the proxy) from *logic* (the implementation). The implementation is supposed to be initialized so nobody else can seize control of it. Here, the init had been reverted in an earlier fix and never re-applied.

```
1. Call initialize() on the uninitialized implementation contract
2. Supply attacker-controlled Guardian addresses (the trust anchor)
3. Submit an "upgrade" authorized by the fake Guardian signatures
4. Execute SELFDESTRUCT on the implementation
5. Proxy now delegates to a destroyed address → bridge permanently bricked
```

Because the Guardian set is the root of trust for the whole bridge, controlling it means controlling everything — ransom, destruction, or worse.

**The fix:** re-initialize the implementation and lock it so it can never be hijacked. Wormhole verified and patched within hours of the report.

---

## 💡 Why it's brilliant

- **It's a *regression* hunt.** The bug was introduced by a *previous fix* that reverted initialization. The researcher understood that security-relevant state can silently disappear across commits — and went looking for exactly that.
- **Uninitialized proxies are a repeatable class.** This exact pattern (UUPS implementation left initializable) became a canonical audit check *because* of findings like this. One disclosure educated an entire industry.
- **Highest payout in history.** $10M, and arguably cheap for the protocol given $736M was on the line.

---

## 💰 Payout & impact

- **$10,000,000** — record single whitehat payout.
- **$736M** of bridge assets at risk; attacker could brick the bridge or hold the protocol ransom.
- **Response time: hours** from report to fix.

---

## 🔗 Sources

- [Immunefi — Wormhole Uninitialized Proxy Bugfix Review](https://medium.com/immunefi/wormhole-uninitialized-proxy-bugfix-review-90250c41a43a)
- [The Block — Wormhole $10M payout](https://www.theblock.co/post/148085/wormhole-announces-10-million-bug-bounty-payout)
- [PortSwigger — Wormhole pays record $10M bug bounty](https://portswigger.net/daily-swig/blockchain-bridge-wormhole-pays-record-10m-bug-bounty-reward)

> All credit to **satya0x**. This page is a study summary — read the original.
