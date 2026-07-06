# Contributing to Rydberg

Rydberg is early. Most of what's needed right now is more useful than a
big pull request: trying it, breaking it, and telling us what happened.

## Ways to contribute

### Build a module
The most direct way to contribute is to build something. Read
`MODULE_CONTRACT.md` for the requirements — in short: your own repo, a
`docker-compose.yml` at the root, join `rydberg-net`, follow the naming
convention, own your own data. If it's genuinely useful and follows the
contract, open an issue or PR here to get it added to `catalog.list` so
others can `rydberg install <your-module>` by name.

### Report hardware/compatibility findings
If you got Rydberg (or any module, especially ones doing local LLM
inference) running on hardware that took real effort — a different GPU
vendor, a different Linux distro, anything that wasn't a clean first try
— write it up. A short doc describing what broke and what fixed it is
genuinely valuable, even if you don't touch a line of the core code. Open
a PR adding it under `docs/hardware/`, or link it from an issue if you'd
rather host it elsewhere.

### Fix or improve the core CLI
`bin/rydberg` is one file, intentionally. Read it before proposing
changes — the goal is to keep it simple enough that anyone can read the
whole thing in one sitting. If a change adds real capability without
adding real complexity, it's welcome. If it solves a problem better
handled inside a module, it probably belongs there instead.

### Report a bug
Open an issue. Include:
- what you ran (`rydberg <command>`)
- what you expected
- what actually happened (full output, not a summary)
- your OS, Docker version, and hardware if it's remotely relevant (it
  usually is more often than people expect)

## Pull requests

- Keep them small and focused — one change, one PR.
- If it changes `MODULE_CONTRACT.md` or anything module authors depend
  on, say so explicitly and explain why the contract needs to change.
- No CI/test suite exists yet for the core CLI. Test your change by hand
  against a real install (`rydberg install`, `rydberg up`, `rydberg down`)
  and describe what you tested in the PR description.

## What this project is not looking for (yet)

- Large architectural rewrites — the project is too young for that kind
  of change to be reviewable in good faith.
- New modules bundled into the core repo — modules are always separate
  repos, see `MODULE_CONTRACT.md` for why.
- Style-only changes without functional substance — not worth the review
  overhead this early on.

## Code of conduct

Be someone people want to collaborate with. If a disagreement gets
heated, take a step back — the project isn't worth being unkind over.