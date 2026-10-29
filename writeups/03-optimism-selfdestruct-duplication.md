# 03 · Optimism — Infinite Money via `SELFDESTRUCT`

> 🎫 **Credit**
> **Researcher:** Jay Freeman (**saurik**) — creator of Cydia, the iOS jailbreak app store
> **Target:** Optimism (OVM 2.0 Layer-2 rollup)
> **Platform:** Immunefi · **Year:** 2022 (February)
> **Payout:** **$2,000,042**
> **Original writeup:** [Optimism Infinite Money Duplication Bugfix Review — Immunefi](https://medium.com/immunefi/optimism-infinite-money-duplication-bugfix-review-daa6597146a0)

---

## 🎯 TL;DR

Optimism's L2 (OVM 2.0) mishandled the `SELFDESTRUCT` opcode: when a contract self-destructed and sent its balance to itself, the state didn't sync correctly and the ETH could be **duplicated** on the next transaction. Repeat → infinite ETH on Optimism.

---

## ⚙️ How it works

On L1 Ethereum, `SELFDESTRUCT` sends a contract's balance to a target address and removes the contract. Optimism's rollup reimplemented EVM execution, and its handling of `SELFDESTRUCT` didn't properly reconcile the resulting state change.

```solidity
contract Exploit {
    function duplicate() external {
        // 1. Contract holds X ETH
        // 2. SELFDESTRUCT, sending the balance to itself
        // 3. ETH is "sent" but the state change isn't synced
        // 4. On the next transaction, the balance reappears
        // 5. Repeat → mint ETH out of nothing
    }
}
```

An attacker could manufacture arbitrary ETH on Optimism and bridge value out, collapsing the L2 economy.

**The fix:** Optimism implemented a correction and upgraded infrastructure **~3 hours** after the report — one of the fastest critical responses in crypto history.

---

## 💡 Why it's brilliant

- **Reimplemented VMs are goldmines.** Any team that re-writes the EVM inherits the obligation to match *every* opcode edge case. `SELFDESTRUCT` is one of the gnarliest. saurik went straight for the seam between "real EVM" and "Optimism's EVM."
- **Pedigree meets ethics.** saurik literally could have printed "infinite capital" and chose disclosure. The writeup is a masterclass in articulating worst-case impact without exploiting it.
- **The $0.42.** The payout was $2,000,042 — the `42` is a wink. Even the number is iconic.

---

## 💰 Payout & impact

- **$2,000,042** bounty.
- Infinite ETH generation on Optimism → total economic collapse of the L2 and bridge risk.
- **~3-hour** report-to-production-fix turnaround.

---

## 🔗 Sources

- [Immunefi — Optimism Infinite Money Duplication Bugfix Review](https://medium.com/immunefi/optimism-infinite-money-duplication-bugfix-review-daa6597146a0)
- [Cointelegraph — iOS jailbreak dev wins $2M for Optimism bug](https://cointelegraph.com/news/ios-jailbreak-dev-wins-2m-bounty-for-finding-critical-optimism-bug)
- [Optimism — official postmortem (GitHub)](https://github.com/ethereum-optimism/optimism/blob/master/technical-documents/postmortems/2022-02-02-inflation-vuln.md)

> All credit to **Jay Freeman (saurik)**. This page is a study summary — read the original.
