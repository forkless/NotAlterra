# Known Issues

## `game_running()` returns hardcoded `false`

The process-detection guard in `guard.rs` intentionally returns `false` on
both platforms to avoid Windows Defender false positives
(Trojan:Win32/Wacatac.C!ml). The startup reminder modal ("Please close
Subnautica 2 before using NotAlterra") is the real safety mechanism, but
the function name implies active detection.

The dormant `_game_running_linux()` function (kept for future use) contains
a working code path that is unreachable behind the hardcoded `false` return.

## GVAS parser is heuristic, not a structural walker

`src/gvas.rs` extracts GVAS properties by scanning for property names as
raw byte sequences and validating preceding length fields — it does not
walk the full GVAS schema tree. It works on known Subnautica 2 save files
but:

> The GVAS format is defined by the public Unreal Engine 5 source code —
> the SaveGame system and its binary layout are part of the engine's
> open API.

- Will silently skip properties it wasn't written to look for
- May produce false positives if a matching byte sequence appears in
  unrelated data
- Does not validate overall GVAS structure (header magic, version, etc.)

**Decision**: Won't fix. A structural walker would require reverse-
engineering the full UE5 GVAS property tree without a published schema,
with no significant benefit for the tool's feature set. The six properties
the UI displays (SlotName, DisplayName, bIsMultiplayerSave, playtime, etc.)
are found reliably by the current approach. The three fuzz targets
(`parse_gvas`, `full_metadata`, `backup_roundtrip`) ensure the parser does
not crash on adversarial input. When a property cannot be found, the picker
falls back to the filename — the tool degrades gracefully rather than
erroring out.

## No TUI / menu-flow test coverage

`src/main.rs` contains 1,227 lines of event-loop code (menu dispatch,
picker interactions, confirmation dialogs, .ini submenu) with zero
automated tests. All other modules (ops, gvas, guard, config) have
integration tests, but UI regressions are only caught through manual
testing.

This is common for terminal applications but means menu-flow changes
carry higher risk.

## Discovery module carries vestigial code

`src/discovery.rs` is 442 lines, but the primary save-folder workflow is
manual path entry via **Set save folder**. The module still contains:

- `scan_other_users()` — scans `/home/*` (Linux) or `C:\Users\*` (Windows)
  for other user profiles
- `walk_for_subnautica()` — broad filesystem walk for custom installs
- `discover_save_folders()` — full discovery entry point

These were downgraded from primary to fallback in v0.3.2 for privacy
reasons, and `quick_discover()` (checking only the current user's default
paths) is now the only automated startup check. The heavy scanning code
could be removed if manual path entry remains the sole workflow.

## No CLI flags for scripting

The tool is TUI-only. There is no `--backup`, `--extract <archive>`,
`--inspect <savefile>`, or `--list` flag. This means it cannot be used in
cron jobs, scheduled tasks, or automated backup scripts.

**Planned**: v0.5.0 roadmap includes CLI flags.

## Bus factor mitigation documented but unimplemented

`docs/GOVERNANCE.md` describes a planned emergency signing key stored with
a non-technical trusted person. The envelope and key do not yet exist. The
designated technical contact is not confirmed. If the maintainer becomes
unreachable, the only viable path is a fork.

## Resolved

### Dormant functions removed (v0.4.0)

`_game_running_windows()` and `_backup_root()` were removed as dead code.
Neither was called from any code path. `_game_running_linux()` and
`available_space()` are retained for planned future use — each has its own
`#[allow(dead_code)]` annotation.

**Rationale**: `_game_running_windows()` was never wired up (process
detection was intentionally disabled to avoid AV false positives).
`_backup_root()` pointed at the legacy `NotAlterra_Backups/` directory
which was replaced by `backups/saves/` and `backups/config/` in v0.4.0.
