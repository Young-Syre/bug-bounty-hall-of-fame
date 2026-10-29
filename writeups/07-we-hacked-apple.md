# 07 · We Hacked Apple for 3 Months

> 🎫 **Credit**
> **Researchers:** Sam Curry, Brett Buerhaus, Ben Sadeghipour, Samuel Erb, Tanner Barnes
> **Target:** Apple's infrastructure (not iOS — the web/corporate attack surface)
> **Platform:** Apple Security Bounty · **Dates:** July 6 – October 6, 2020
> **Payout:** **$288,500** (across 32 paid reports of 55 found)
> **Original writeup:** [*We Hacked Apple for 3 Months* — samcurry.net](https://samcurry.net/hacking-apple)

---

## 🎯 TL;DR

Five hackers spent three months systematically mapping and testing Apple's *web* infrastructure and found **55 vulnerabilities** — 11 critical — including a wormable iCloud account takeover, full compromise of customer and internal apps, Apple source-code access, and employee session hijacking.

---

## ⚙️ How it works

This isn't one bug — it's a *campaign*, and the writeup's value is the methodology:

- **Map the real attack surface.** Apple's perimeter is enormous (acquisitions, forgotten subdomains, internal tools exposed to the internet). The team enumerated relentlessly before touching a single exploit.
- **Headline finding — wormable iCloud ATO.** A vulnerability in iCloud's handling of content could take over a victim's account and **spread account-to-account like a worm** — zero-click, self-propagating.
- **Chaining mediums into criticals.** IDORs + auth bypasses yielded internal Apple source code; memory leaks exposed customer PII and payment data; more IDORs allowed hijacking Apple *employee* sessions and management tooling.
- **Industrial control reach.** Some findings touched warehouse/management software — i.e. physical-world supply-chain impact from a web bug.

```
Representative paid findings:
 • Memory leak exposing customer data ............ $34,000
 • Internal Apple source code access (IDOR/authz) . $6,500
 • Apple employee session hijacking (IDOR) ........ $6,000
 • iCloud full-name disclosure .................... $5,000
 • Wormable iCloud account takeover ............... (critical)
```

---

## 💡 Why it's brilliant

- **It's the gold-standard methodology writeup.** More people have learned "how to approach a huge target" from this one post than from most paid courses. Enumerate → understand → chain → escalate.
- **Breadth as a weapon.** They didn't look for *the* bug; they built a map and pulled 55 threads. Elite programs reward *coverage*, and this proves it.
- **It forced a payout correction.** Apple initially paid $55,100; after the writeup went public, they paid an additional $233,400 — a candid, documented look at how bounty economics actually play out.

---

## 💰 Payout & impact

- **$288,500** total ($55,100 initially, +$233,400 after disclosure).
- **55 vulnerabilities** (11 critical, 29 high); 32 paid.
- Apple's turnaround on criticals: as fast as **4–6 hours**.

---

## 🔗 Sources

- [Sam Curry — We Hacked Apple for 3 Months](https://samcurry.net/hacking-apple)
- [Vice — Five hackers found 55 bugs in Apple products](https://www.vice.com/en/article/hackers-found-55-bugs-in-apple-productsmade-dollar51500/)
- [AppleInsider — Researchers spent months hacking Apple](https://appleinsider.com/articles/20/10/08/security-researchers-spent-months-hacking-apple----heres-what-they-found)

> All credit to **Sam Curry, Brett Buerhaus, Ben Sadeghipour, Samuel Erb, and Tanner Barnes**. This page is a study summary — read the original.
