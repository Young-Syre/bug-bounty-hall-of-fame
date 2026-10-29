# 01 · Aurora — Infinite ETH Mint via `delegatecall`

> 🎫 **Credit**
> **Researcher:** pwning.eth
> **Target:** Aurora Engine (EVM on NEAR Protocol)
> **Platform:** Immunefi · **Year:** 2022 (April)
> **Payout:** **$6,000,000**
> **Original writeup:** [*How did I Save 70000 ETH and Win 6 Million Bug Bounty* — pwning.mirror.xyz](https://pwning.mirror.xyz/CB4XUkbJVwPo7CaRwRmCApaP2DMjPQccW-NOcCwQlAs)

---

## 🎯 TL;DR

Aurora's bridge emitted a withdrawal event based on `msg.value`, assuming the value was actually delivered to the contract. But when the contract was reached via `delegatecall`, `msg.value` was *never transferred* — yet the log fired anyway. You could withdraw ETH on the NEAR side for deposits that never happened, re-deposit, and **double your balance every loop** until the 70,000+ ETH bridge was drained.

---

## ⚙️ How it works

The vulnerable bridging contract trusted that if a bridge event carried a value, the value had moved. The fix in the code assumed the contract itself would collect `msg.value`. `delegatecall` breaks that assumption: it runs the callee's code in the *caller's* context, so `msg.value` semantics don't deliver funds to the bridge contract — but the event still emits.

```
Exploit loop:
1. Invoke the bridging contract via delegatecall()
2. Contract emits a withdrawal event carrying msg.value
3. msg.value is NEVER actually transferred (delegatecall context)
4. Withdraw nETH on the NEAR side based on the fake event
5. Re-deposit the withdrawn ETH back into Aurora
6. GOTO 1

Balance growth:  X → 2X → 4X → ... → 2^N · X
```

After enough iterations an attacker owns more ETH than the bridge holds — all 71,000 ETH (~$200M+ at the time) is drainable, and every token on Aurora can be bought with free money.

**The fix:** reject the exit when the supplied address doesn't match the input's address, disabling the `delegatecall` path entirely.

---

## 💡 Why it's brilliant

- **It weaponizes documented EVM behavior.** `delegatecall` not forwarding `msg.value` to the callee is *correct* Ethereum semantics. The bug is the bridge *assuming* otherwise. Elite findings live exactly here — in the gap between "how the VM works" and "what the developer assumed."
- **Exponential, not linear.** Doubling per loop means the drain is near-instant once the primitive exists. The researcher didn't just find a leak; they found a geometric one.
- **Save-not-steal framing.** The title says it all — the writeup reads as "I saved 70,000 ETH," which is exactly how a $6M whitehat disclosure should be told.

---

## 💰 Payout & impact

- **$6,000,000** — one of the largest single whitehat payouts in history.
- **~71,000 ETH (~$200M+)** at immediate risk; the entire Aurora ecosystem could have been bought out with minted ETH.

---

## 🔗 Sources

- [pwning.eth — technical writeup (mirror.xyz)](https://pwning.mirror.xyz/CB4XUkbJVwPo7CaRwRmCApaP2DMjPQccW-NOcCwQlAs)
- [Immunefi — Aurora Infinite Spend Bugfix Review ($6M payout)](https://medium.com/immunefi/aurora-infinite-spend-bugfix-review-6m-payout-e635d24273d)
- [The Block — Aurora pays $6M reward](https://www.theblock.co/post/150500/aurora-labs-pays-6-million-reward-to-hacker-that-saved-70000-eth-from-getting-stolen)

> All credit to **pwning.eth**. This page is a study summary — read the original.
