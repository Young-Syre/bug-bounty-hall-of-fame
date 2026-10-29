# 10 · Chrome — ipcz/Mojo Sandbox Escape ($250K Record)

> 🎫 **Credit**
> **Researcher:** "Micky"
> **Target:** Google Chrome — the `ipcz` library in Mojo (Chrome's inter-process communication)
> **Platform:** Chrome Vulnerability Reward Program (VRP) · **Year:** 2025
> **CVE:** CVE-2025-4609 · **Payout:** **$250,000** (a record Chrome VRP award)
> **Original coverage:** [SecurityWeek — Google now offers up to $250K for Chrome vulnerabilities](https://www.securityweek.com/google-now-offering-up-to-250000-for-chrome-vulnerabilities/)

---

## 🎯 TL;DR

Chrome isolates untrusted web content in a sandboxed renderer; escaping to a non-sandboxed process is the crown-jewel bug. Micky found a flaw in **ipcz** (the IPC layer in Mojo) that allowed **duplicating Chrome's own parent process** and running code in the duplicate — outside the sandbox — earning a record $250,000.

---

## ⚙️ How it works

Chrome's security model is **multi-process + sandbox**: the renderer handles hostile HTML/JS but has almost no OS privileges, and must ask a higher-privileged (non-sandboxed) process to do anything real, via **Mojo** IPC. `ipcz` is the plumbing underneath Mojo.

```
1. Compromise / drive the sandboxed renderer process
2. Abuse the ipcz/Mojo IPC primitive to manipulate internal Chrome processes
3. Duplicate the browser's own (non-sandboxed) parent process
4. Run attacker code inside that duplicated, non-sandboxed context
   → sandbox escape → code execution outside the renderer
```

The novelty is step 3: rather than attacking the OS or a classic memory-corruption-in-the-broker, the escape **turns Chrome's own process-management IPC against itself** to spawn an un-sandboxed executor.

Chrome VRP reward tiers for context:

```
 • Demonstrated RCE in a non-sandboxed process ... $250,000   ← this finding
 • Controlled write in a non-sandboxed process ... $90,000
 • Memory-corruption demonstration ............... $35,000
```

---

## 💡 Why it's brilliant

- **It attacks the *trust boundary*, not just memory.** The entire sandbox model assumes the renderer can't become the broker. Micky found a way to manufacture a non-sandboxed process from inside the sandbox — the cleanest possible violation of the model.
- **IPC is the modern frontier.** As browsers harden memory with MiraclePtr, PartitionAlloc, and CFI, the richest remaining surface is the *logic* of inter-process messaging. This finding is a flagship example of where elite browser research is heading.
- **Record-setting, by design.** Google *raised* the top Chrome reward to $250K specifically for RCE in a non-sandboxed process — and this is the finding that showcases why that tier exists.

---

## 💰 Payout & impact

- **$250,000** — a record single Chrome VRP payout.
- Sandbox escape to a non-sandboxed process → path to full RCE from web content.
- Tracked as **CVE-2025-4609**; patched by Google.

> **Attribution note:** unlike the other entries, there isn't a long-form first-person writeup publicly published for this one (browser-vendor VRP details are often withheld pending broad patch rollout). The breakdown above is assembled from public reporting — see sources — and credited to the researcher as named in that reporting ("Micky").

---

## 🔗 Sources

- [SecurityWeek — Google now offering up to $250K for Chrome vulnerabilities](https://www.securityweek.com/google-now-offering-up-to-250000-for-chrome-vulnerabilities/)
- [Risky Business — Researcher scores $250,000 for Chrome bug](https://news.risky.biz/risky-bulletin-researcher-scores-250-000-for-chrome-bug/)
- [GBHackers — Record $250K bug bounty awarded](https://gbhackers.com/record-250k-bug-bounty-awarded/)

> Credit to the researcher **"Micky"** as named in public reporting. This page is a study summary — read the sources.
