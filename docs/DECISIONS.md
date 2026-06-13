# Design Decisions

This file captures the rationale behind significant architecture and format
choices, so the reasoning is preserved for future maintainers (including
yourself six months from now).

---

## Sentinel File vs config.ini (v0.3.2)

### Problem
`config.ini` persisted the save folder path to disk, including the user's
filesystem-username. This is a privacy concern — paths are visible next to
the binary.

### Decision
Remove `config.ini` entirely. The save folder is session-only — set it each
time via **Set save folder**. The disclaimer acceptance is tracked via a
0-byte sentinel file (`NotAlterra_LICENSE_ACCEPTED`) alongside the binary.

### Rationale

**Privacy** — no paths written to disk. The save folder exists only in
memory while the tool runs.

**Simplicity** — no config parsing, no INI format to maintain, no migration
code for renamed keys.

**Sentinel, not config** — a 0-byte file communicates exactly one boolean
(disclaimer accepted). It cannot grow into a configuration file over time.
The format intentionally prevents scope creep.

**What was removed:**
- `AppConfig` struct (save_path, ini_path, save_scan, disclaimer_accepted)
- `load_config()` / `save_config()` with INI parsing
- Cached `ini_path` — now derived from save folder at runtime
- Four integration tests for config round-trips

---

## Manual Path Entry vs Auto-Discovery (v0.3.0)

### Problem
Auto-discovery scanned user profiles and system directories for Subnautica 2
save folders. This is a privacy concern — it traverses `/home/*` (Linux) and
`C:\Users\*` (Windows).

### Decision
Replace full auto-discovery with manual path entry via **Set save folder**.
Keep a lightweight `quick_discover()` that checks only the current user's
default install paths at startup.

### Rationale

**Privacy** — no scanning of other users' profiles or system drives.

**Current-user convenience** — `quick_discover()` checks 1 path on Windows,
3 paths on Linux, all within the current user's own directories. Returns
the first match silently, no UI. If nothing is found, the user enters their
path manually.

**Discovery module retained** — `validate_custom_path()` and
`derive_ini_path()` still live in `discovery.rs` for the manual entry flow.
The aggressive scan functions (`discover_save_folders()`, `scan_other_users()`,
`walk_for_subnautica()`) are removed.

---

## tar.gz Backup Format (v0.4.0)

### Problem
Directory-tree backups (`NotAlterra_Backups/notalterra_copy_<timestamp>/`)
are messy, uncompressed, and have no integrity guarantees.

### Decision
One `tar.gz` archive per backup event, stored in `backups/saves/`.

### Rationale

**No vendor lock-in** — standard `tar -xzf` recovers data without the tool.
If NotAlterra stops working, the user's backups are still accessible with
standard system utilities.

**Single file per event** — reduces clutter. One backup = one file, not
a directory tree with 15+ loose save files.

**Compression** — save files compress well (~75MB → ~20MB). Reduces disk
usage without user effort.

**Pure Rust implementation** — `tar` + `flate2` crates, 200M+ downloads
combined. No system dependencies, no external tools.

**Per-entry restore** — extracting a single save file from the archive
does not require decompressing the entire archive.

### Safeguards

- **Atomic write**: backup written to `.tmp` file, then atomically renamed.
  Power loss during backup discards a temp file, not a real backup.
- **Integrity check after creation**: archive is read back and validated
  before reporting success.
- **SHA256 manifest**: a `MANIFEST` file inside each archive records the
  hash of every contained save file. On restore, each extracted file is
  verified against its expected hash — silent bit-rot detected before bad
  data reaches the save folder.
- **Fuzz target**: round-trip fuzzing (create archive from diverse inputs →
  restore → compare) catches logic bugs.

### Migration
Existing `NotAlterra_Backups/` directory-tree backups are detected and
transparently imported on first run after upgrade. No manual migration
required.

---

## GVAS Heuristic Parser vs Structural Walker (v0.4.0)

### Problem
The GVAS save-file parser in `src/gvas.rs` uses byte-scanning to find
property names as raw string patterns. It does not walk the full UE5 GVAS
schema tree. This means:

- Properties the scanner isn't written to look for are silently skipped
- Byte sequences that happen to match a property name can produce false
  positives (mitigated by validating the preceding length field)
- Overall GVAS structure (header magic, version, property ordering) is not
  validated

The GVAS format is defined by the public Unreal Engine 5 source code — the
SaveGame system and its binary serialization are part of the engine's
open API.

### Decision
Keep the heuristic byte-scan parser. Marked as Won't fix.

### Rationale

**No published schema** — Unknown Worlds does not publish the GVAS property
layout for Subnautica 2. A structural walker would require reverse-
engineering the full property type system without ground truth.

**No user-facing benefit** — the tool reads six properties (SlotName,
DisplayName, bIsMultiplayerSave, playtime, etc.) for display purposes. The
byte-scan finds all of them reliably on known save files. A structural
walker would produce the same output.

**Graceful degradation** — when the scanner cannot find a property, the
picker falls back to the filename. The tool never crashes or presents
incorrect data.

**Fuzz coverage** — three fuzz targets (`parse_gvas`, `full_metadata`,
`backup_roundtrip`) verify the parser does not panic on adversarial input.
If a future game update changes the binary layout, fuzzing will catch it.

---

## Dead-Code Cleanup (v0.4.0)

### Problem
`src/main.rs` had `#![allow(dead_code)]` at the crate level, silencing
compiler warnings for four unused functions. This prevented the compiler
from flagging real dead code.

### Decision
Remove `_game_running_windows()` and `_backup_root()`. Retain
`_game_running_linux()` and `available_space()` for planned future use.

### Rationale

**Removed:**
- `_game_running_windows()` — process detection via `tasklist` was
  intentionally disabled across the project to avoid Windows Defender
  false positives (Trojan:Win32/Wacatac.C!ml). The startup reminder modal
  is the replacement. No plan to re-enable.
- `_backup_root()` — returned the legacy `NotAlterra_Backups/` path,
  replaced by `backups/saves/` and `backups/config/` in v0.4.0.

**Retained (each with its own `#[allow(dead_code)]`):**
- `_game_running_linux()` — kept for possible opt-in process detection
  on Linux where AV false positives are not a concern.
- `available_space()` — kept for a planned disk-space warning before
  backup operations.

---

## Persistent app.ini vs Session-Only Paths (v0.4.1)

### Problem
Save-folder and backup-root paths were session-only — re-entered each
time the tool launched. This was a deliberate privacy choice (v0.3.2),
but in practice users expected paths to persist between sessions.

### Decision
Persist both paths to `app.ini` under the platform config directory
(`data_local_dir/NotAlterra/config/`). Backup data stays in
`~/NotAlterra/backups/` (user-facing, not in AppData).

### Rationale

**Usability** — re-entering paths every session was friction with no
real privacy benefit. The paths already existed on the user's filesystem;
persisting them simply saves keystrokes.

**Transparency, not silence** — instead of claiming "no data stored,"
the documentation now accurately describes what is stored (save-folder
and backup-root paths, which may contain the system username), why
(minimal convenience data), and that it never leaves the machine.

**Platform standards** — config goes to the OS-designated config
directory (`AppData/Local` on Windows, `~/.local/share` on Linux).
User-facing backup data stays in the home directory where users expect
to find it. This separation is standard convention.

**Plain text, user-controlled** — `app.ini` is a simple key=value file.
Users can inspect or delete it at any time. No binary format, no
registry, no opaque storage.

---

## No CLI Flags for Automation (v0.4.3 → v0.5.0)

### Problem
The v0.4.0 roadmap listed CLI flags (`--backup`, `--extract`, `--inspect`,
`--list`) as a planned feature. These would allow scripting and cron-driven
backups without the TUI.

### Decision
Removed from the roadmap. Will not implement.

### Rationale

**TUI is the feature.** The file picker with live metadata preview, split
layout, pip navigation, and archive integrity checks IS the interface.
Dumping raw metadata to stdout or parsing command-line flags for a
non-interactive backup loses all the value the tool provides.

**No demand.** The tool serves a single user (you). You interact with it
through the TUI. There is no automation use case driving this feature.

**Overengineered for scope.** Adding CLI parsing, non-interactive modes,
and a separate output formatter would nearly double the surface area for
a feature neither the maintainer nor any hypothetical user would use.

**Previous error:** The CLI flags were carried over as an assumption from
"every CLI tool should have flags" without questioning whether they serve
the tool's actual purpose. They don't. This decision documents that
realization.
