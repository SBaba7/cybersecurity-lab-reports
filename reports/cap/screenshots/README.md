# Cap screenshot evidence

The sanitized screenshots associated with the Cap report were captured in the authorized HTB lab. The GitHub text connector did not transfer the binary PNGs; add these files from the original report package when available:

1. `01-nmap-enumeration.png`
2. `02-burp-idor.png`
3. `03-ftp-wireshark.png`
4. `04-ssh-access.png`
5. `05-privilege-escalation.png`
6. `06-completion.png`

The screenshots in the provided ZIP are already redacted for sensitive FTP credentials and lab flags. Re-check all pixels and metadata before making a repository public. Do not upload raw `.pcap` captures, passwords, private flags, or unredacted terminal screenshots.

Once the PNGs are uploaded here, replace the corresponding italic evidence-file notes in the machine report with Markdown image links, such as:

```md
![Nmap enumeration](screenshots/01-nmap-enumeration.png)
```
