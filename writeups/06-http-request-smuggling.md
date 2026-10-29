# 06 · HTTP Request Smuggling, Reborn (HTTP Desync)

> 🎫 **Credit**
> **Researcher:** James Kettle — Director of Research, PortSwigger (Burp Suite)
> **Target:** The web itself — CDNs, WAFs, load balancers, reverse proxies (tens of millions of sites)
> **Platform:** PortSwigger Research + live bug bounty programs · **Year:** 2019 → 2025
> **Payout:** **$200,000+** (incl. a documented $20,000 from Netflix for an HTTP/2 downgrade desync; $200K+ in two weeks during the 2024/25 research)
> **Original writeup:** [*HTTP Desync Attacks: Request Smuggling Reborn* — PortSwigger Research](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn)

---

## 🎯 TL;DR

When a front-end proxy and a back-end server **disagree about where one HTTP request ends and the next begins**, you can smuggle a partial request that gets glued onto the *next* visitor's request. Kettle revived this dormant class in 2019, weaponized it, shipped free tooling, and kept escalating it through HTTP/2 desync and the 2024 "HTTP/1.1 Must Die" research.

---

## ⚙️ How it works

The classic primitive abuses two ways to signal body length — `Content-Length` and `Transfer-Encoding: chunked` — and makes the two servers pick *different* ones (CL.TE / TE.CL / TE.TE):

```http
POST / HTTP/1.1
Host: vulnerable.com
Content-Length: 6
Transfer-Encoding: chunked

0

G
```

```
Front-end honors Transfer-Encoding: sees the request end at "0\r\n\r\n".
Back-end honors Content-Length: 6: keeps reading, so the leftover "G"
becomes the PREFIX of the next request on that connection.

→ The next victim's request becomes:  G + (their request)
→ Attacker controls part of someone else's request: cache poisoning,
  credential capture, security-control bypass, request hijacking.
```

Later research (*HTTP/2: The Sequel Is Always Worse*, 2021) showed that downgrading HTTP/2 to HTTP/1.1 at the edge reintroduces desync on "modern" stacks — and *HTTP/1.1 Must Die* (2024/25) showed the class is still devastating against major CDNs.

---

## 💡 Why it's brilliant

- **He resurrected a "dead" class and industrialized it.** Request smuggling was known but considered impractical. Kettle made it reliable, detectable, and exploitable at scale — then gave everyone the [HTTP Request Smuggler](https://portswigger.net/bappstore) Burp extension for free.
- **Research that keeps paying.** The same mental model earned **$200K+ in two weeks** years later — because every new protocol layer (HTTP/2, HTTP/3) sits on top of back-ends still speaking HTTP/1.1.
- **It's methodology, not a one-off.** The writeups teach *how to find desync*, which is why they produced a generation of smuggling hunters.

---

## 💰 Payout & impact

- **$200,000+** earned applying the techniques (plus a public $20K Netflix example).
- Tens of millions of websites exposed; customers of multiple major CDNs affected.
- Permanently changed how proxies, WAFs, and CDNs handle request parsing.

---

## 🔗 Sources

- [HTTP Desync Attacks: Request Smuggling Reborn](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn)
- [HTTP/1.1 Must Die — what it means for bug bounty hunters](https://portswigger.net/blog/http-1-1-must-die-what-this-means-for-bug-bounty-hunters)
- [Request smuggling explained — with NahamSec & James Kettle](https://portswigger.net/blog/http-request-smuggling-explained-with-seasoned-bug-bounty-hunter-nahamsec-and-world-class-researcher-james-kettle)

> All credit to **James Kettle**. This page is a study summary — read the original.
