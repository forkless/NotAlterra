# Known Issues

## config.ini persists the save path

`config.ini` caches the save-folder path on disk (next to the binary). Paths are sanitized in transaction logs, but the raw path remains in the config file for re-use across sessions.

**Planned**: Remove `config.ini` entirely in a future release. The path would be re-requested from the user on each session. v0.3.0 target.

## Discovery module is deprecated

Auto-scan for save folders (`discovery.rs`) is deprecated — it scans user profiles and system directories for Subnautica 2 saves, which is a privacy concern. The `Locate save files` menu item shows a deprecation notice once per session.

**Planned**: Remove `discovery.rs` entirely in v0.3.0. Users will enter their save path manually via `Set save folder`.
