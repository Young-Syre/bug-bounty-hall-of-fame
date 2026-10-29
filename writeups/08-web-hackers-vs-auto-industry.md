# 08 · Web Hackers vs. The Auto Industry

> 🎫 **Credit**
> **Researchers:** Sam Curry and team
> **Targets:** Mercedes-Benz, BMW, Rolls-Royce, Ferrari, Porsche + 15 more manufacturers
> **Disclosure:** Responsible disclosure (research, not a single bounty figure) · **Year:** 2022–2023
> **Original writeup:** [*Web Hackers vs. The Auto Industry* — samcurry.net](https://samcurry.net/web-hackers-vs-the-auto-industry)

---

## 🎯 TL;DR

The same crew turned their methodology on connected cars and automaker infrastructure. The result: misconfigured SSO exposing internal source and tooling, OTP endpoints that handed over *any* employee/contractor account, and connected-car APIs that let them **locate, unlock, and start vehicles remotely** across ~20 manufacturers.

---

## ⚙️ How it works

Cars are now web apps with wheels — and the web half was wide open:

- **Mercedes-Benz — SSO compromise.** A misconfigured single sign-on gave access to internal assets: private GitHub repos, internal comms, build/CI tooling. Classic "the identity layer is the whole castle."
- **BMW & Rolls-Royce — OTP generation flaw.** A poorly configured one-time-password API allowed **account takeover for any employee or contractor**, reaching sensitive customer and vehicle data.
- **~20 manufacturers — remote vehicle control.** Flaws in connected-car API platforms let the team **track location, unlock doors, and start engines** on victim vehicles — mapping a VIN or account to physical control of the car.

```
Pattern across targets:
  Identity/API misconfig  →  account takeover  →  telematics API  →  physical control
  (unlock / start / locate a real car from a browser)
```

Every affected company fixed within 1–2 days of notification.

---

## 💡 Why it's brilliant

- **It collapses the cyber/physical gap viscerally.** "I found an IDOR" is abstract. "I can unlock and start your car from my laptop" is not. The writeup is the definitive proof that automotive = appsec now.
- **One playbook, many brands.** The team showed that connected-car platforms share the same architectural weak points (telematics APIs, dealer/employee SSO, VIN-keyed lookups), so a single methodology scales across the whole industry.
- **Masterful disclosure storytelling.** Clear scope, clear impact, coordinated fixes, no sensationalism — the template for high-stakes responsible disclosure.

---

## 💰 Payout & impact

- Responsible-disclosure research (no single public bounty figure).
- Remote tracking/unlock/start across ~20 automakers; internal source and tooling exposure at major brands.
- Accelerated serious security investment across automotive telematics.

---

## 🔗 Sources

- [Sam Curry — Web Hackers vs. The Auto Industry](https://samcurry.net/web-hackers-vs-the-auto-industry)
- [PortSwigger — Car companies massively exposed to web vulnerabilities](https://portswigger.net/daily-swig/car-companies-massively-exposed-to-web-vulnerabilities)
- [The Record — Bugs allowed remote control of cars](https://therecord.media/researchers-find-bugs-allowing-access-remote-control-of-cars)

> All credit to **Sam Curry and team**. This page is a study summary — read the original.
