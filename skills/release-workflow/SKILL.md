# Release Workflow

Versioning, release checklist, signing, and push conventions.

## Versioning

| Bump | When | Example |
|------|------|---------|
| PATCH | Bug fix or small polish after a release | v0.3.1 → v0.3.2 |
| MINOR | New feature or significant restructure | v0.3.x → v0.4.0 |
| MAJOR | Breaking change (1.0+ only) | 1.0.0 → 2.0.0 |

- Version in the project manifest matches the latest release tag
- Tags are created at release time, not per-commit
- Multiple commits can happen between tags

## Draft Release Cycle

```
commit → push → tag v0.x.y → CI builds draft → user tests → publish
```

No release goes live without the user testing the draft first.

## Release Checklist

Before signing a release tag:

- [ ] Impact analysis completed — all call sites for new/changed functions
- [ ] All tests pass
- [ ] Linter passes with zero warnings
- [ ] CHANGELOG.md has an entry for the new version
- [ ] Working tree is clean — no uncommitted changes
- [ ] Release notes file is updated for the new version

After CI completes:

- [ ] Download draft binaries from the releases page
- [ ] Test on target platform — basic workflow, key features
- [ ] Click **Publish release** on the hosting platform when satisfied

## Signing & Push

- Commit with `--no-gpg-sign` (avoids GPG passphrase hang in non-interactive terminals)
- **Sign once.** Wait for CI to go green on the unsigned commit, **then** amend with `--gpg-sign` + force-push. Do not amend between CI runs — each amend creates a new SHA, which triggers a new CI run and leaves stale Pages deployments that block the next run.
- **Passphrase typo protection.** Run `git commit --amend --gpg-sign --no-edit` **alone first**. If the passphrase is wrong, only that command fails — the force-push, tag, and push don't run on a broken state. Verify with `git verify-commit HEAD` before proceeding with the remaining commands.
- Normal push for fast-forward commits
- `--force-with-lease` for amended commits or tag refreshes
- **Start from a clean tag.** Before re-tagging, always delete the old tag locally AND remotely (`git tag -d v0.x.y && git push --delete origin v0.x.y`). If the old tag still exists, `git tag -s` will fail with "tag already exists" and you'll end up with a tag pointing to the wrong commit.
- If force-pushing, delete old tag and re-tag after the new commit lands
- Tag once, push once. Re-signing an existing tag pushes a new SHA and triggers another CI run with the same deployment conflict risk.

## Release Notes

- Release notes file contains only the current version's changes
- Historical release notes on the platform are cleaned per-release
