# 05 · GitHub Enterprise — Chaining 4 Bugs into RCE

> 🎫 **Credit**
> **Researcher:** Orange Tsai (Cheng-Da Tsai, [@orange_8361](https://twitter.com/orange_8361)) — DEVCORE
> **Target:** GitHub Enterprise
> **Platform:** GitHub Security Bug Bounty · **Year:** 2017
> **Payout:** **$7,500** + "Best Report" (GitHub 3rd Bug Bounty Anniversary)
> **Original writeup:** [*How I Chained 4 Vulnerabilities on GitHub Enterprise* — blog.orange.tw](https://blog.orange.tw/posts/2017-07-how-i-chained-4-vulnerabilities-on/)

---

## 🎯 TL;DR

Orange took a Ruby/Rails appliance with no obvious critical, and **chained four lower-severity bugs** — starting from an SSRF born of URL-parser inconsistency — into full **remote code execution** on GitHub Enterprise. It's the writeup that made "parser differential" a household phrase in appsec.

---

## ⚙️ How it works

The genius is the *chain*, not any single link:

```
1. SSRF via URL parser inconsistency
   The validator and the requester disagree on what a URL means
   (e.g. http://evil.com@victim.com, fragment/host confusion).
   → reach internal-only services.
        ↓
2. GraphQL injection
   Pivot the internal access into an injectable internal API.
        ↓
3. Internal API access
   Reach privileged functionality never meant to face the network.
        ↓
4. Remote Code Execution
   Convert the internal foothold into code execution on the appliance.
```

The entry point — **URL parser inconsistency** — is Orange's signature. Different libraries (and different *languages*) disagree about where the host ends and the path/fragment begins, so a string that passes an allowlist check resolves to a totally different host when actually fetched.

---

## 💡 Why it's brilliant

- **It's the canonical "chain" writeup.** Four bugs that are individually "meh" become a critical when sequenced. This is the mental model every serious bounty hunter internalizes.
- **It launched a research era.** Orange's broader work — *A New Era of SSRF: Exploiting URL Parsers* (Black Hat USA 2017) — found parser 0-days in Python, PHP, Perl, Ruby, Java, Node.js, `wget` and `curl`. This GitHub chain is that theory applied to a real, hardened target.
- **$7,500 that's worth more than most criticals.** The payout is small; the *influence* is enormous. "Best Report" was the real prize, and the technique has earned others millions since.

---

## 💰 Payout & impact

- **$7,500** + Best Report recognition.
- Full RCE on GitHub Enterprise appliances.
- Popularized URL-parser-differential SSRF as a first-class attack technique across the industry.

---

## 🔗 Sources

- [Orange Tsai — How I Chained 4 Vulnerabilities on GitHub Enterprise](https://blog.orange.tw/posts/2017-07-how-i-chained-4-vulnerabilities-on/)
- [Black Hat USA 2017 — A New Era of SSRF: Exploiting URL Parsers (PDF)](https://blackhat.com/docs/us-17/thursday/us-17-Tsai-A-New-Era-Of-SSRF-Exploiting-URL-Parser-In-Trending-Programming-Languages.pdf)
- [GitHub Bug Bounty — Orange Tsai researcher profile](https://bounty.github.com/researchers/orangetw.html)

> All credit to **Orange Tsai**. This page is a study summary — read the original.
