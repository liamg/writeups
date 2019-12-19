# ctf-notes

## Resources

- [GTFOBins](https://gtfobins.github.io/) - List of unix binaries which can be exploited to escalate privileges etc.
- [Reverse Shell Cheat Sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) - List of remote shells in various languages
- [NetCat without -e](https://pen-testing.sans.org/blog/2013/05/06/netcat-without-e-no-problem/) - Reverse shell spawning when netcat does not support `-e`
- [Exploit DB](https://www.exploit-db.com/) - Exploit DB

## Tools

_(skipping obvious stuff like metasploit, nmap, john etc.)_

### Port Scanning
- [furious](https://github.com/liamg/furious)

### Fuzzing
- [scout](https://github.com/liamg/scout) - Lightweight URL fuzzer
- [gobuster](https://github.com/OJ/gobuster) - File/directory/DNS/VHOST buster
- [wfuzz](https://tools.kali.org/web-applications/wfuzz) - Advanced web fuzzer

### Crypto
- [pax](https://github.com/liamg/pax) - Padding oracle exploiter

### Linux Enumeration
- [LinEnum](https://github.com/rebootuser/LinEnum) - Linux enumeration script

## Snippets

### Linux Enumeration

```bash
curl -s https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```
