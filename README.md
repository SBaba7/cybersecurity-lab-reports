# Cybersecurity Lab Reports

Technical reports from authorized cybersecurity training labs, focused on **methodology, evidence, root cause, and remediation** rather than simply collecting flags.

## Machine reports

| Machine | Platform | OS | Difficulty | Techniques | Report |
|---|---|---|---|---|---|
| **Cap** | Hack The Box (retired) | Linux | Easy | Nmap, IDOR, PCAP analysis, credential reuse, SSH, Linux capabilities | [Read the report](reports/cap/README.md) |

## Repository structure

```text
cybersecurity-lab-reports/
├── README.md
├── .gitignore
├── reports/
│   └── cap/
│       ├── README.md
│       └── screenshots/
│           └── README.md
├── templates/
│   └── machine-report.md
└── tools/
    └── README.md
```

Every writeup documents the attack chain, the technical evidence, and how the underlying issues could be mitigated. Reusable scripts and further retired-machine reports will be added as they are completed.

**Evidence status:** The Cap Markdown report is uploaded. Its six **sanitized PNG screenshots are pending upload** into `reports/cap/screenshots/` and the report currently references them as evidence-file notes. See [screenshot instructions](reports/cap/screenshots/README.md).

## Documentation workflow

1. Solve only authorized targets; capture terminal outputs and screenshots privately.
2. Reconstruct the investigation with observations, reasoning, important commands, and remediation.
3. Remove secrets, credentials, flags, and sensitive artifacts.
4. Publish only after the platform allows public walkthroughs.

> All findings concern intentionally vulnerable lab systems. Nothing in this repository is authorization to test unrelated systems.
