# Reporting workflow

This workflow applies to every authorized machine or training lab.

## During the session: capture first

1. Work in a separate local folder per machine; keep the note vault and GitHub repository distinct if possible.
2. Capture meaningful terminal commands, their output, HTTP traffic and evidence screenshots in time order.
3. Save lengthy outputs as text (e.g., Nmap `-oA`), and keep raw captures, passwords, flags and session logs **local**.
4. Record one-line hypotheses at key decision points so you can reconstruct *why* you tested something.
5. Do not push active-machine walkthroughs or confidential captures to a public repository.

## After completion: write and review

1. Confirm the platform permits publishing the specific content.
2. Create `reports/<machine-slug>/README.md` using `templates/machine-report.md`.
3. Copy only relevant sanitized screenshots to `reports/<machine-slug>/screenshots/`, named `01-topic.png`, `02-topic.png`, etc.
4. Link images relatively: `![Evidence description](screenshots/01-topic.png)`.
5. Describe the sequence of discovery, observations, validation, impact, root cause and remediation. Distinguish hypotheses from confirmed findings.
6. Remove unrelated output and check all Markdown links locally or on GitHub.
7. Review `git diff --cached` and verify no secrets, passwords, flags, live-target data, or .pcap files are staged.
8. Add one row to the root report index.

## Reusable organization

- One **repository** can contain many machines.
- One **folder** per machine: `reports/<machine-slug>/`.
- One **README.md** per machine as the main report.
- Add `scripts/` or extra files within that machine only when genuinely useful.
- Keep the report template platform-neutral and adapt sections to the actual investigation: not every lab includes a shell or privilege escalation.
- Store reusable, independent tooling in its own project when mature, not as empty folders.

## Privacy before public release

Original Cap screenshots uploaded on 2026-11-06 contain credentials/flags. **Do not make this repository public as-is.** Sanitizing current images is necessary but not sufficient: old Git commits can retain the originals. Publish a clean-history repository or properly rewrite the relevant history and verify the resulting commits before changing visibility.
