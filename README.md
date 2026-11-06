## Disclaimer

This repository contains penetration-testing walkthroughs and technical reports from authorized cybersecurity training environments, primarily Hack The Box.

Reports may include simulated credentials, IP addresses, exploitation commands, screenshots, and challenge solutions for officially retired machines.

All activities were performed within authorized lab environments for educational purposes. No real-world credentials or private information are intentionally disclosed.

Solutions for active Hack The Box machines will not be published.

# Cybersecurity Lab Reports

Technical writeups from **authorized cybersecurity training environments**, focused on investigation, evidence, vulnerability root causes, and remediation.

## Machine reports

| Machine | Platform | Difficulty | OS | Areas covered | Writeup |
| --- | --- | --- | --- | --- | --- |
| [Cap](reports/cap/README.md) | Hack The Box (retired) | Easy | Linux | Service enumeration, IDOR, PCAP analysis, credential reuse, Linux capabilities | [Report](reports/cap/README.md) |
| [TwoMillion](reports/twomillion/README.md) | Hack The Box (retired) | Easy | Linux | Invite API analysis, access control, command injection, credential reuse, CVE-2023-0386 | [Report](reports/twomillion/README.md) |

New machines will be added as separate entries, with their own report and evidence. The repository structure is designed for multiple labs, not one particular target.

## Organization

```text
reports/
  <machine-slug>/
    README.md            # Investigation, evidence, findings, remediation
    screenshots/         # Numbered, relevant, sanitized images
    scripts/             # Optional: original machine-specific code
templates/
  machine-report.md      # Reusable writeup skeleton
docs/
  reporting-workflow.md  # How to document and publish future labs
```

Folders marked **optional** are created only when needed. Original reusable tools, if developed later, can have their own projects instead of empty placeholders here.

## Working principles

- Solve only systems for which you have explicit authorization.
- Take raw notes, command logs and screenshots **locally** during a session; prepare the report afterward.
- Explain what a result establishes and what remains uncertain. Keep evidence concise, reproducible, and linked.
- Publish solutions only after the training platform permits public writeups.
- Never publish passwords, tokens, flags, VPN configurations or sensitive packet captures. Review screenshots and repository history before changing visibility.

See the [report template](templates/machine-report.md) and [reporting workflow](docs/reporting-workflow.md).

> **Repository status:** Private. Some original Cap screenshots still contain lab credentials or flags and must be sanitized, including Git history, before public release.
