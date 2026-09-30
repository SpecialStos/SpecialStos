# Chris - CISOKO

**I build FiveM resource engineering.** CIsoko is my studio — I design, build, and
support the systems it sells.

The work is mostly the part of a FiveM server that every owner rewrites for themselves:
shared libraries, framework abstraction, state management, contracts, conformance
testing, and the unglamorous plumbing that decides whether a server survives 200 players.

Founded 2021, Cyprus. Framework-agnostic across **ESX, QBCore, Qbox, and standalone.**

[Store](https://fivem.cisoko.net) · [Docs](https://docs.cisoko.net) · [Discord](https://discord.gg/cisoko)

---

## Why this is public

Everything Cisoko sells is escrow-protected. That is a real and necessary protection, and
it has an unavoidable consequence: **a buyer never reads the code that actually
determines whether the product is any good.**

So the parts I can show are the only evidence there is. That is what this account is for —
not a portfolio, but the engineering, written down where anyone can check it.

If I claim a resource is cheap to run, the measurement should be visible. If I claim an
API is stable, the contract should be machine-readable. If I claim a library is
well-behaved, the tests should run in CI where anyone can read them.

---

## How I work

- **Server-authoritative, always.** The client requests; the server decides. Money, items,
  progression, positions, permissions — all re-derived server-side.
- **Measure, then publish.** Idle cost per feature, with the hardware, player count, and
  server build attached. CI fails a regression past tolerance. A number without its
  conditions is not evidence.
- **One contract, several consumers.** Docs, types, changelogs, the storefront, and the
  support bot are all generated from the same source.
- **A bug gets a reproduction, not a workaround.** Every fix ships with a test I watched
  fail first. A green suite that was never observed red proves nothing.
- **Deprecate loudly.** Never silently change what an existing call means.
- **Own the dependency graph.** No framework forks, no obfuscation, no remote code
  loading.

---

## Licensing

CIsoko uses the official FiveM Asset Escrow system, so core backend logic is encrypted.
What stays open is deliberate, and it is the part most vendors lock down:

- **All configuration files**
- **All locale files**
- **All framework bridge files**
- **The entire NUI** — escrow does not cover it, so there is no reason for me to lock it
  either. Theme the interface yourself.
- **Every documented export and event**

If you want to change how something looks or which framework it talks to, you never need
to open a support ticket. That is the design, not a concession.

[`cis_libs` is LGPL-3.0 and safe to depend on](https://github.com/overextended/ox_lib) —
I depend on it and never vendor or modify it. Any GPL-licensed integration sits behind a
single adapter file so it can be removed cleanly.
