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

Everything CIsoko sells is escrow-protected. That is a real and necessary protection, and
it has an unavoidable consequence: **a buyer never reads the code that actually
determines whether the product is any good.**

So the parts I can show are the only evidence there is. That is what this account is for —
not a portfolio, but the engineering, written down where anyone can check it.

If I claim a resource is cheap to run, the measurement should be visible. If I claim an
API is stable, the contract should be machine-readable. If I claim a library is
well-behaved, the tests should run in CI where anyone can read them.

---

## `cis_libs` — a shared library for FiveM servers

A zero-dependency utility library. **No `ox_lib`, no `PolyZone`, no framework coupling.**
It sits beside ESX, QBCore, and Qbox and normalises the differences, so a resource is
written once.

The interesting part is its boundary model. FiveM has no cross-resource shared memory —
`shared_script` *copies* a file into the consuming resource's VM, it does not share it.
Stateless logic can be duplicated freely. **Stateful singletons cannot**, and getting that
wrong multiplies your frame cost by your resource count.

`cis_libs` draws that line explicitly, documents exactly which files sit on it, and gets
the whole library down to **one native call per frame for the entire client** no matter
how many resources consume it.

```lua
shared_script '@cis_libs/init.lua'

Cis.ready(function(ok)
    if not ok then return end
    Cis.zones.box('shop', vector3(1200.0, 800.0, 30.0), vector3(20.0, 20.0, 5.0), {
        onEnter = function(coords) openMenu() end,
        onExit  = function(coords) closeMenu() end,
    })
end)
```

- Pure `shared/` modules that are safe to duplicate — a spatial grid, config, a pending-key
  store
- Callbacks, zones, targeting, entity sync, database, logging
- **One local spatial grid for the whole server**, rather than one per resource
- Fuzz-tested grid properties checked against a brute-force reference

[Documentation](https://docs.cisoko.net)

---

## `Cisobot` — the support and community bot

The Discord bot behind CIsoko's support desk, AI-assisted first-line troubleshooting,
and anti-scam moderation. It also ingests the machine-readable contract layer so support
answers about a product *by version* instead of guessing.

Worth noting because it is the part customers never see:

- **316 behavioural assertions in CI** — including that a customer role can never satisfy
  a staff check even when the roles are deliberately made to collide
- Every role check goes through one module. Nothing else in the codebase tests roles
- Button interactions bind to the initiating user and **re-check permission at press
  time**, so a dialog opened before a demotion is refused after it
- Cached, atomic, coalesced storage with corruption recovery rather than silent overwrite
- Secret scanning in pre-commit and CI

---

## The platform

CIsoko's products run on a shared platform I am building in public:

| Layer | What it is |
|---|---|
| `cis_libs` | Runtime, registry, contracts, observability. Owns no data. |
| `cis_core` | Framework abstraction, state, database, migrations. |
| `cis_keys` | One credential primitive — property doors, rooms, safes, garages, vehicles, businesses. |
| `cis_inventory` | Two drivers behind one API, plus the ownership and provenance layer. |
| `cis_housing` | Property, tenancy, finance, utilities, layered security. |
| `cis_phone` | The App SDK. Versioned, with a written compatibility promise. |

Three commitments that shape all of it:

**Capabilities are declared in the manifest and negotiated at boot.** FiveM's
`dependencies{}` directive has no version-constraint syntax, and the usual ecosystem
workaround is advisory, opt-in, and silently no-ops in production. This resolves the
dependency graph at boot and refuses to start a resource whose requirements are unmet —
loudly, with a diagnostic naming both sides.

**Every contract is machine-readable.** Each resource ships a pure-data `api.lua` readable
*without starting the provider*, carrying `since`, `until`, and a signature per export.
The same file generates the docs, the type annotations, and the changelog. One source,
several consumers.

**Adapters must pass a conformance suite before they ship** — structural, read, write,
and semantic levels, because the one that actually matters in the wild is fork drift. A
server owner can see which level their fork is certified at.

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
