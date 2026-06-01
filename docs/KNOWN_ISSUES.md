# Known Issues

## config.ini persistence (privacy)

`config.ini` caches the save-folder path including the Windows username on disk. Paths are sanitized in logs, but the raw path remains in the config file.

**Planned**: Remove `config.ini` entirely. Auto-discover by default, prompt for manual path entry at runtime if discovery fails. v0.3.0 target.

## No manual path override UI

Users who cannot auto-detect save locations have no way to manually specify a path. This will be resolved alongside the config.ini phase-out with a runtime input prompt.
