# Module Contract

Anything can be a Rydberg module as long as its repo follows this contract.
The core CLI doesn't parse or understand your code — it only needs these
few things to be true.

## Required

1. **A `docker-compose.yml` at the repo root.** This is what `bin/rydberg up`
   includes. It can define one service or several (e.g. an API plus its own
   database) — whatever your module actually needs.

2. **Every service must join the external `rydberg-net` network:**
   ```yaml
   services:
     your-service:
       # ...
       networks:
         - rydberg-net

   networks:
     rydberg-net:
       external: true
   ```
   This is how your module becomes reachable by Cloudflare Tunnel (or any
   other reverse proxy pointed at the same network) and how it can reach
   other modules by service name, if it ever needs to.

3. **Configuration comes from environment variables, not hardcoded values.**
   Reference `${SOME_VAR}` in your compose file; document what you need in
   your own `.env.example`. The core repo's `.env` is not shared with
   modules automatically — each module manages its own.

## Strongly recommended

- **Own your data.** If your module needs persistence, include your own
  database service in your compose file (Postgres, SQLite, whatever fits).
  Don't assume another module's database exists or is reachable — modules
  should work if installed alone, in any order, or without any other
  module present at all.

- **Fail loudly, not silently.** If your module can't reach a dependency it
  genuinely needs (a paired service, an external API), surface that in your
  own UI or logs rather than failing in a confusing way. The core CLI
  doesn't currently enforce cross-module dependencies — if your module truly
  can't stand alone, say so clearly in your own README.

- **Pin your own base images and dependencies.** The core CLI can pin a
  module to a specific git ref (tag, branch, or commit) via
  `bin/rydberg install <name> <url> <ref>` — use tagged releases rather
  than tracking a moving branch if you want people self-hosting your module
  to get predictable upgrades.

- **One module, one concern.** If you're building something that could
  reasonably be wanted independently of your other work, it's probably two
  modules, not one. If nobody would ever want half of it without the other
  half, keep it one module (see the main README's "why modules" section for
  the actual test).

## What the core will never do

- Modify your compose file
- Share a database or network namespace beyond `rydberg-net`
- Auto-install a module's dependencies on your behalf
- Assume anything about your internals beyond the two points above

If you're building a module and something about this contract doesn't fit
your use case, that's worth raising as an issue on the core repo — the
contract should stay minimal, but it should also stay honest about what it
actually requires.
