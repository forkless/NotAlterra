# NotAlterra — planned transition

## Direction

- TUI archived at v0.4.3 — critical fixes only.
- GUI becomes the future in a **separate repo** (fork of the TUI, new identity).
- Tagline: *"NotAlterra: We are no longer terminal. You survived 4546B. You can handle a window."*

## Why fork, not workspace

- TUI is complete and stable — no need to carry its crate in the GUI repo.
- GUI gets its own name, identity, and community.
- Different release cadence, different issues, different dependencies.
- Shared library code (`src/lib.rs`) is snapshot-copied at fork time; cherry-pick backend fixes from the TUI repo as needed.

## Repo structure

| Repo | Purpose | Status |
|---|---|---|
| `github.com/forkless/NotAlterra` | TUI — archived, critical fixes only | Current repo |
| `github.com/forkless/<new-name>` | GUI — Windows native (WinUI 3) | To be created |

## Agent rules

- Linux agent works on the TUI repo for any critical fixes.
- Windows agent works on the GUI repo for all new development.
- No shared branch coordination — repos are independent.

## When GUI ships

- v0.5.0 — GUI alpha, TUI v0.4.3 still available for download.
- Release draft → test GUI binaries → publish.
