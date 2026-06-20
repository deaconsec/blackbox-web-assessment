# Web Application Security Assessment - Public Report

A black-box external penetration test of an authorized live WordPress site. Scope covered reconnaissance, TLS analysis, WordPress enumeration, HTTP security-header review and CSRF/HTML-injection modeling - followed by full remediation with the operator and a verification re-audit.

> **Authorization & redaction**
> Conducted with the explicit authorization of the site operator, including permission to publish. This is a redacted public version: the target domain, usernames, IP addresses, API tokens and specific infrastructure details have been removed or replaced with placeholders.

---

## Scope

- **Type:** Black-box external testing (no access to the origin server)
- **In scope:** Public-facing interfaces of the target domain
- **Out of scope:** Origin-server IP, internal networks, social engineering
- **Platform:** Intel NUC 15 Pro - Arch Linux (KVM/QEMU) running a Kali Linux VM

## Tools used

| Tool | Purpose |
|------|---------|
| **Nmap** | TCP port & service enumeration (incl. `--script vuln`) |
| **SSLyze** | TLS/crypto configuration and vulnerability analysis |
| **WPScan** | WordPress user / plugin / theme enumeration |
| **Burp Suite** | Passive HTTP response-header analysis |

## Summary of findings

| # | Finding | Risk | Status |
|---|---------|------|--------|
| 1 | TLS configuration & crypto analysis | Low | No action needed |
| 2 | Missing brute-force protection (rate limiting) | Medium | Fixed |
| 3 | Username enumeration via REST API | Medium | Recommended (open) |
| 4 | Missing HTTP security headers | Medium | Fixed |
| 5 | HTML injection in comment field | Low | Accepted residual risk |
| 6 | Missing anti-CSRF tokens | Low | Mitigated by infrastructure |
| 7 | Open alternative web ports | Informational | No action needed |
| 8 | Slowloris DoS (CVE-2007-6750) | Low | Fixed |

No critical, externally exploitable vulnerabilities were found. A Cloudflare reverse proxy plus server-side protection plugins provided a strong baseline; the remaining best-practice gaps were fixed with the operator and confirmed by a re-audit.
[Nmap --script vuln Scan: offene Ports und CSRF-Prüfung (redacted)](screenshots/recon_nmap_redacted.png)



## Methodology

Full assessment lifecycle: recon → analysis → exploitation modeling → remediation → verification. A few specifics worth noting:

- Risk ratings adjusted for compensating controls - e.g. Slowloris (CVE-2007-6750) downgraded from Medium to Low because Cloudflare terminates all TCP connections before they reach the origin.
- HTML injection vs. CSS injection distinguished precisely (the comment field allowed inline-style HTML, not CSS-selector data exfiltration).
- `X-XSS-Protection` deliberately *not* added - the header is deprecated and removed from modern browsers; a Content-Security-Policy was implemented instead.
- Remediation delivered as concrete, operator-ready server configuration, then re-audited.

## Report

See [`Pentest_Report_Public.pdf`](./Pentest_Report_Public.pdf) for the full redacted report.
---

This work was performed legally and with authorization, for educational and portfolio purposes.

**Kyrill** 
