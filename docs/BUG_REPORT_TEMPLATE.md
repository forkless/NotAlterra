# Bug Report Template

> **I will not respond to bug reports that are the equivalent of "It does
> not work!"** Provide the information below — what you did, what happened,
> what you expected, and your environment. Reports without these details
> will be closed without response.

Copy the section below into a new GitHub issue at
<https://github.com/forkless/NotAlterra/issues/new> and fill it out.

---

```markdown
### Describe the bug

What happened?  What did you expect to happen instead?

### Steps to reproduce

1.
2.
3.

### Environment

- **OS**: (e.g. Windows 11, Ubuntu 24.04, Steam Deck)
- **NotAlterra version**: (shown in the title bar, e.g. v0.4.1)
- **Subnautica 2 install**: (Steam, Xbox, Epic, custom)

### Logs

If available, attach the `transaction.log` file located next to the
binary (`logs/transaction.log`).

### Screenshots (optional)

If the terminal output is relevant, paste a screenshot or a copy of the
on-screen text.
```

---

## Privacy

Do not paste save file contents, personal names, or anything you consider
private into the issue. Do not paste full file paths that might reveal
your real name or system layout — the tool's `transaction.log` is
sanitized, but your issue is public.

When in doubt, omit it. Include only what is necessary to reproduce the
problem.

---

## Security Issues

**Do not** report security vulnerabilities through a public GitHub issue.

### Preferred: GitHub Private Vulnerability Reporting

1. Go to the repository's **Security** tab:
   <https://github.com/forkless/NotAlterra/security/advisories>
2. Click **Report a vulnerability** — the thread is private by default.

### Fallback: Email

If you cannot use the GitHub advisory form, email the maintainer:

- **Email**: forkless@protonmail.com
- **GPG key**: [314BB48A3C72D8EC2830B8BED2B0DF63E2CBEA16](https://github.com/forkless.gpg)

Reports are acknowledged within 48 hours. Patches are committed within
48 hours of triage, followed by a public advisory after the release ships.

For more detail, see `SECURITY.md` in the project root.
