# Rydberg

Your own home server, without the usual pain of setting one up.

## Why this exists

Self-hosting used to mean a NAS and a couple of apps. Now it means Docker
Compose files, reverse proxies, TLS certificates, subdomain routing, and a
different setup guide for every app you want to run — most of which assume
you already know how the last three fit together. It's gotten harder to run
your own server at exactly the moment more people want to, because they're
tired of renting their own data back from someone else's cloud.

Rydberg is an attempt to make that easier, without pretending it's a
one-click black box. It's a **thin core** — routing, docs, and a small CLI —
plus a growing set of **independent modules** you opt into one at a time.
Want a personal dashboard? Install it. Want file storage later? Install
that too, whenever you want it, without touching anything else already
running.

This isn't a single monolithic app. It's closer to how Homebrew or Helm
work: one small tool that knows how to fetch and run things, and a set of
things it can fetch — each maintained, versioned, and usable on its own.

## Why modules instead of one big repo

Two rules decide what's a separate module versus part of an existing one:

1. **Would anyone reasonably want A without B?** If the answer is no —
   like a dashboard and the API it talks to — they stay one module. Splitting
   things that are never deployed apart just creates ways to end up in a
   broken half-installed state for no real benefit.
2. **Does it own its own data?** A module that needs a database brings its
   own — it never reaches into another module's database or schema. Rydberg
   Cloud doesn't know Rydberg Dashboard's job-tracker data exists, and it
   shouldn't need to.

The core repo (this one) never contains product logic. It doesn't know what
a "dashboard" or "job tracker" is. It just knows how to clone a module,
include its Docker Compose file, and route a subdomain to it. That's
deliberate: it keeps the core small and stable while modules evolve
independently, and it means installing one module never requires
understanding — or even downloading — the code for any other.

## How it works

Every module is its own git repository containing a `docker-compose.yml`
that defines its own service(s) and joins the shared `rydberg-net` Docker
network. The core doesn't inspect or modify that file — it just tells
Docker Compose to include it alongside whatever else is installed.

```
rydberg/                    (this repo — the core)
├── bin/rydberg               the CLI
├── compose/
│   └── cloudflared.yml       shared routing, always present
├── apps/                     installed modules land here (gitignored)
├── modules.list               registry of what's installed
└── docker-compose.generated.yml   written by `rydberg up`, don't edit by hand
```

See `MODULE_CONTRACT.md` for exactly what a module repo needs to provide.

## Quickstart

```bash
git clone https://github.com/<you>/rydberg.git
cd rydberg
cp .env.example .env        # fill in BASE_DOMAIN and your Cloudflare tunnel token

# install whichever modules you want
bin/rydberg install dashboard https://github.com/<you>/rydberg-dashboard.git main

# see what's installed
bin/rydberg list

# start everything
bin/rydberg up
```

To pull the latest version of a module:
```bash
bin/rydberg update dashboard
```

To remove one entirely:
```bash
bin/rydberg remove dashboard
```

## Routing and TLS

Rydberg uses [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
for exposing modules under subdomains (`dashboard.yourdomain.com`, etc.),
handled entirely by Cloudflare's edge — no open ports, no certificate
management on your end. If you're not using Cloudflare, any reverse proxy
(Caddy, nginx) can serve the same role; the module contract doesn't care
how traffic reaches it, only that it's reachable on `rydberg-net`.

## What Rydberg is not

It's not a multi-tenant SaaS — each deployment is one person's instance,
whether that's `rydberg.app` or a self-hosted copy on someone's own
hardware and domain. It's not trying to hide Docker or Linux from you
either; if you're allergic to a terminal, this probably isn't for you yet.
It's for people who want their own server and are willing to run a few
commands to get one, without also wanting to become a full-time systems
administrator just to add a second app.
