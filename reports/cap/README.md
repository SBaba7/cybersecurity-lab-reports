# Hack The Box: Cap

> **Machine:** Cap  
> **Difficulty:** Easy  
> **Operating system:** Linux (Ubuntu 20.04 observed)  
> **Platform:** Hack The Box  
> **Status:** Retired  
> **Assessment:** Authorized educational lab  
> **Completed:** November 2026  
> **Skills:** TCP enumeration, HTTP access control, Burp Suite, Wireshark, FTP, credential reuse, SSH, Linux capabilities

## Executive summary

Cap was compromised through a chain of weaknesses across a web dashboard, an exposed network capture, and a Linux host. The dashboard allowed access to another user's security snapshot by changing a numeric resource identifier (`/data/1` to `/data/0`). The resulting downloadable PCAP exposed an FTP username and password in plaintext. Those credentials also permitted SSH login. On the host, Python 3.8 had the `CAP_SETUID` Linux capability, allowing the unprivileged user to change its process UID to root and obtain a root shell.

**Attack chain:** TCP enumeration → insecure direct object reference (IDOR) → exposed PCAP → plaintext FTP credentials → SSH access → dangerous Linux capability → root access.

## Scope and approach

The target was the intentionally vulnerable HTB Cap machine, at lab IP `10.129.196.209` during this session. The work was limited to this authorized training environment. No production systems were tested.

Tools used: **Nmap**, **Burp Suite Community Edition**, **Wireshark**, **SSH**, and Linux commands (`getcap`, Python).

## 1. Reconnaissance and service enumeration

An initial TCP connect scan with service version detection covered Nmap's default set of 1,000 TCP ports:

```bash
nmap -sT -sV -v 10.129.196.209
```

| Port | Service | Identified software |
|---|---|---|
| `21/tcp` | FTP | vsftpd 3.0.3 |
| `22/tcp` | SSH | OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 |
| `80/tcp` | HTTP | Gunicorn |

Nmap reported 997 closed TCP ports among those tested. The presence of three open ports did **not** itself demonstrate exploitable vulnerabilities. HTTP was selected for further investigation because it provided an accessible application surface.

*Evidence image captured: `screenshots/01-nmap-enumeration.png` (PNG not uploaded yet).*

**Command correction:** In the recorded session, the attempted command appended `command-logs/` after `-oA "001_nmap"`. Nmap interpreted `command-logs/` as an additional target, causing an error about the target expression. The intended scan still completed for the IP address. The correct way to place all Nmap output formats inside the directory is:

```bash
mkdir -p command-logs
nmap -sT -sV -v -oA command-logs/001_nmap 10.129.196.209
```

`-oA` produces `.nmap`, `.xml`, and `.gnmap` files. The corrected command is recommended for future work, not represented as a command that was actually run during the recorded session.

## 2. HTTP enumeration and broken access control

The application, titled **Security Dashboard** in its HTML response, exposed a snapshot resource with a numeric identifier. A snapshot was available at `/data/1`. I tested the adjacent resource identifier by sending a modified request in **Burp Suite Repeater**:

```http
GET /data/0 HTTP/1.1
Host: 10.129.196.209
```

The application responded successfully with content related to a different security snapshot. The access to this resource, including its downloadable packet capture, indicated **insecure direct object reference (IDOR)**, a form of broken object-level authorization.

*Evidence image captured: `screenshots/02-burp-idor.png` (PNG not uploaded yet).*

**Why it matters:** An identifier being guessable is not, by itself, a vulnerability. The problem is the application's failure to enforce ownership or authorization checks before returning the requested user's resource.

**Remediation:** Check access permissions on the server for every resource request and associated download; do not rely on hiding or obfuscating IDs.

## 3. PCAP analysis and credential exposure

The unauthorized snapshot allowed a **PCAP** file to be downloaded and opened in **Wireshark**. Inspection of the captured FTP traffic revealed `USER` and `PASS` commands. FTP transmits authentication credentials in plaintext, which made the username and password visible inside the captured network traffic.

The lab username identified in the capture was `nathan`. The password is deliberately omitted from this public report.

*Evidence image captured: `screenshots/03-ftp-wireshark.png` (PNG not uploaded yet).*

**Why it matters:** The broken dashboard authorization exposed a file containing credentials. This converted an application access-control failure into a host compromise opportunity.

**Remediation:** Restrict access to packet captures, redact sensitive contents, use encrypted protocols such as SFTP/SSH instead of plaintext FTP, and avoid reusing credentials across services.

## 4. SSH access

The FTP username and password recovered from the authorized lab capture were tested against the SSH service. Login succeeded as `nathan`, demonstrating password reuse across services.

```bash
ssh nathan@10.129.196.209
```

The resulting interactive shell provided unprivileged access to the Ubuntu host. The user flag was located in `user.txt`. **Flag contents are intentionally excluded.**

*Evidence image captured: `screenshots/04-ssh-access.png` (PNG not uploaded yet).*

**Observation:** SSH itself was not shown to be vulnerable. The authenticated login was possible because a valid credential had been exposed elsewhere and was reused.

## 5. Linux privilege escalation

Once logged in, I investigated file capabilities using:

```bash
getcap -r / 2>/dev/null
```

A notable finding was:

```text
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
```

`CAP_SETUID` can permit a process to change its user ID. Giving this capability to a general-purpose interpreter is dangerous because a local user can execute arbitrary Python code with that privilege. In this HTB environment, the following command changed the process UID to `0` and started a shell:

```bash
/usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

The prompt then reflected root access (`root@cap`), and the root flag file could be read. Both flag values are redacted in the screenshot.

*Evidence image captured: `screenshots/05-privilege-escalation.png` (PNG not uploaded yet).*

**Remediation:** Remove unnecessary privileges from general-purpose interpreters and audit file capabilities. Grant narrowly scoped capabilities only to executables that demonstrably require them.

## 6. Security findings overview

| Finding | Root cause | Demonstrated consequence | Recommended fix |
|---|---|---|---|
| Broken object-level authorization | Snapshot endpoint did not adequately enforce resource ownership | Another user's PCAP became accessible | Server-side authorization on each snapshot and download |
| Sensitive credential exposure | Captured FTP login carried plaintext credentials | Recovered a usable password | Encrypted transfer protocols; restrict and sanitize packet captures |
| Credential reuse | Same credential accepted by FTP and SSH | Unauthorized shell access with recovered password | Distinct credentials, stronger authentication controls |
| Excessive Linux capability | Python interpreter was assigned `CAP_SETUID` | Local user could elevate to UID 0 | Remove capability and audit other privileged binaries |

These are documented observations in a deliberately vulnerable lab. A production assessment would also require environment context, evidence-backed likelihood and impact, and an appropriately justified severity rating.

## 7. Lessons learned

1. Follow the evidence from application behavior into downloaded artifacts: the valuable information was in a packet capture, not simply the webpage.
2. Test authorization, not just resource discovery: changing `/data/1` to `/data/0` revealed a missing object ownership check.
3. Analyze protocols at the packet level: plaintext FTP authentication can expose credentials to anyone with access to the capture.
4. Distinguish the initial entry point from later weaknesses: SSH was the access channel, while earlier data exposure and password reuse made that login possible.
5. Audit Linux capabilities during privilege-escalation enumeration: `CAP_SETUID` on an interpreter can be as dangerous as a misconfigured SUID executable.
6. Keep command output as text, screenshots as supporting evidence, and publication-sensitive details redacted.

## Proof of completion

*Evidence image captured: `screenshots/06-completion.png` (PNG not uploaded yet).*

---

**Disclosure and ethics:** This walkthrough describes a retired, intentionally vulnerable Hack The Box machine. All IP addresses, commands, and results refer to the authorized lab session. The screenshots included here are sanitized: the FTP password, packet payload and flag values have been redacted.