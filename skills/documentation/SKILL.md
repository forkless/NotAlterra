# Documentation

When to update each documentation file in the project.

## Hierarchy

| File | Purpose | When to update |
|------|---------|----------------|
| `src/*` | Doc comments on every function | Every change to the function |
| `CHANGELOG.md` | What changed, version, date | Every commit |
| `GOVERNANCE.md` | Roadmap, release process, checklist | When process or plans change |
| `KNOWN_ISSUES.md` | Bugs and limitations users might encounter | When a new issue surfaces or is resolved |
| `DECISIONS.md` | Rationale behind architecture choices | When a significant decision is made |
| `_release.md` | What's new in the current release | Every release |
| `README.md` | User-facing features, build instructions | When features or setup change |
| CI workflow file | Pipeline configuration | When pipeline logic changes |

## Doc Comment Standards

- Every public function must have a doc comment
- Each comment explains *what* the function does, *why* it exists, and
  *what edge cases* it handles
- No "Internal helper" or "see module-level docs" placeholders
- Language-standard doc format (e.g. `///` in Rust, `///` in C#, `/** */` in JS)

## Changelog Format

```
## [v0.x.y] — YYYY-MM-DD

### Added
- New features

### Fixed
- Bug fixes

### Changed
- Behavior changes, refactors

### Removed
- Features removed

### Notes
- Operational notes (git resets, CI issues, etc.)
```

## Release Notes

- Contains only the current version's changes — no history from older versions
- Replaced entirely for each new release, not appended
