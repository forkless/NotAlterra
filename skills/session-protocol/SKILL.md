# Session Protocol

How to structure and execute each development session efficiently.

## Initialization

1. **Full project scan first** — read the build file, all source files,
   tests, docs. Ensures the full codebase is understood before making changes.
2. **Check version control status** — identify any uncommitted work from
   prior sessions.
3. **Load companion skills** — `documentation`, `release-workflow`,
   `testing-fuzzing`, `privacy-security`, `ci-cd-pipeline`.

## Grav / CSS Troubleshooting

When a CSS rule targeting `.class-name` on the body won't apply despite the
class being in the page's YAML frontmatter, check **page inheritance**.

Grav child pages inherit settings from parent pages. If a parent page
(e.g. `02.projects/default.md`) has a different or conflicting
`body_classes`, the child's class may be overridden despite appearing
correct in the child's own frontmatter.

**Quick check sequence:**

1. Confirm the class appears in the actual rendered `<body>` tag — not just
   in the YAML (`curl -s <page> | grep -o '<body[^>]*>'`)
2. If the class is missing from the rendered body, check the parent page's
   `body_classes` at each level of the hierarchy
3. Ensure parent and child pages use identical `body_classes` values, or
   the child explicitly overrides

This saved hours of debugging. Check hierarchical YAML overrides before
assuming a CSS specificity problem.

## Task Protocol

1. **Impact analysis before code** — list every file and call site that needs
   to change. Present for validation before writing code.
2. **One feature per cycle** — one logical change per commit. Batch docs,
   changelog, and version metadata with the feature.
3. **Call site audit on completion** — verify every reference is updated.
   Build + test + lint before declaring done.
4. **Draft release cycle** — commit → push → tag → CI builds draft →
   user tests → publish. Never ship without draft testing.
