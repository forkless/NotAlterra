# CVE Vulnerability Report Template

**CVE** (Common Vulnerabilities and Exposures) is a standardized identifier
for publicly known security vulnerabilities — `CVE-2026-XXXXX`. Not every
bug qualifies; CVEs are reserved for security-relevant flaws that affect
confidentiality, integrity, or availability.

This template helps you report a potential CVE-class vulnerability in
NotAlterra. Use it before public disclosure — a patch should ship before a
CVE is published.

---

## ⚠️ No Public Disclosure

**Do not** file a public GitHub issue, post on forums, or discuss the
vulnerability on social media before a patch is released. Premature
disclosure puts every user of the tool at risk.

Report privately to the maintainer instead (see below).

---

## Template

Copy and fill out the section below into an email. Attach supporting files
(proof-of-concept, logs, crash dumps) directly.

```
Subject: [NotAlterra Security] <brief description>

### Vulnerability type

- [ ] Path traversal / arbitrary file write
- [ ] Silent data corruption during backup or restore
- [ ] Denial of service (crash / hang on crafted input)
- [ ] Dependency vulnerability (CVE in a library)
- [ ] Other: ___________

### Description

What does the vulnerability allow an attacker to do?  What conditions
are required?

### Steps to reproduce

1.
2.
3.

### Proof of concept (if applicable)

Attach or paste a minimal input, script, or sequence of actions that
triggers the issue.

### Affected versions

- NotAlterra version(s): (e.g. v0.4.2)
- Platform: (Windows / Linux / both)

### Impact assessment

- [ ] Data loss possible
- [ ] Remote code execution (unlikely — no network surfaces)
- [ ] Privilege escalation (runs in user context, no admin required)
- [ ] Other: ___________

### Suggested CVE assignment

- [ ] I intend to request a CVE ID for this issue
- [ ] I am not requesting a CVE (report only)
```

---

## Where to Send

### Preferred: GitHub Private Vulnerability Reporting

1. Go to the repository's **Security** tab:
   <https://github.com/forkless/NotAlterra/security/advisories>
2. Click **Report a vulnerability**.
3. Fill in the template fields — the thread is private by default.
4. GitHub can assign a CVE ID directly through their CNA without MITRE
   coordination.

### Fallback: Email

If you cannot use the GitHub advisory form, email the maintainer with
the completed template:

- **Email**: forkless@protonmail.com
- **GPG key**: [314BB48A3C72D8EC2830B8BED2B0DF63E2CBEA16](https://github.com/forkless.gpg)

Encrypted email is preferred when the report includes sensitive details
or proof-of-concept code.

## What Happens Next

1. **Acknowledgment** — within 48 hours of receipt.
2. **Triage** — the maintainer assesses severity and reproduces the issue.
3. **Patch** — fix committed within 48 hours of triage.
4. **CVE request** — the maintainer requests a CVE ID from MITRE or a
   CVE Numbering Authority (CNA) if the issue qualifies.
5. **Disclosure** — after the patched release ships, a public advisory is
   published and the CVE is made public.

For the full policy, see `SECURITY.md` in the project root.
