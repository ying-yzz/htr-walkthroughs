---
title: "Network Enumeration with Nmap Answer"
---
# Host Discovery
* *Question 1*
  * *Based on the last result, find out which operating system it belongs to. Submit the name of the operating system as result.*
  * *HINT: The information that gives us such an indication is Time-To-Live (TTL). There exist a lot of different overviews with different protocols giving us an overview of which systems work specific TTL values.*

<p align="center">
  <img width="700" height="259" alt="Captura de pantalla 2026-09-13 174229" src="https://github.com/user-attachments/assets/b4ee2b9f-e9ee-4c0a-a7a1-7b71f264ea0d" />
</p>

Based on the image, our IP is 10.10.14.2 and we sent an echo request with a TTL = 255. Therefore, the target IP replies with a TTL = 128. A quick Google search shows that the OS used is likely Windows.

---

# Host and Port Scanning
* *Question 1*
  * *Find all TCP ports on your target. Submit the total number of found TCP ports as the answer.*
  ```bash
nmap -p- <IP target>
```
We use the -p- flag to scan all 65,535 ports
* *Question 2*
  *  *Enumerate the hostname of your target and submit it as the answer. (case-sensitive)*
```bash
nmap -p 22,80,110,139,143,445,31337 -sC <IP target>
```

<p align="center">
<img width="700" height="529" alt="Captura de pantalla 2026-09-13 220441" src="https://github.com/user-attachments/assets/5dff18ba-5b54-412b-bfac-498653c7a4c7" />
</p>

Although we scan all open ports, it is only necessary to scan ports 139 and 445 to find the hostname; which is NIX-NMAP-DEFAULT

---

# Saving the Results
* *Question 1*
 *  *Perform a full TCP port scan on your target and create an HTML report. Submit the number of the highest port as the answer.*

 ```bash
sudo nmap <IP target> -p- -oA target
```
-oA target saves the scan results in all formats to the "target" file.

```bash
xsltproc target.xml -o target.html
```
Convert the XML format into HTML.

---

# Service Enumeration
* *Question 1*
 * *Enumerate all ports and their services. One of the services contains the flag you have to submit as the answer.*

```bash
nmap -p- <IP target>
```
To know all the useful ports: 

```text
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
110/tcp   open  pop3
139/tcp   open  netbios-ssn
143/tcp   open  imap
445/tcp   open  microsoft-ds
31337/tcp open  Elite

```bash
nmap -p 22,80,110,139,143,445,31337 -sV -sC 10.129.166.14
```

-sV to known the using version
-sC to grab the banners

In the port 31337 we can see the flag

---

# Nmap Scripting Engine

* *Question 1*
 * *Use NSE and its scripts to find the flag that one of the services contain and submit it as the answer.*

```bash
nmap -p- <IP target>
```
To known all the useful ports: 

```text
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
110/tcp   open  pop3
139/tcp   open  netbios-ssn
143/tcp   open  imap
445/tcp   open  microsoft-ds
31337/tcp open  Elite

```bash
nmap -p 22,80,110,139,143,445,31337 -A <IP target>
```
-A use a aggressive scan: use the -sV, -O, -sC and traceroute

Using this command we can find the previous flag and the new flag in the port 31337

---

# Firewall and IDS/IPS Evasion

## Easy Lab

* *Question 1*
 * *Our client wants to know if we can identify which operating system their provided machine is running on. Submit the OS name as the answer.*
 * *HINT: Remember, you don't need to provide a version of it. Think about which services can give you information about the operating system. After interviewing the administrators, we found out that they want to prevent neighboring hosts of their /24 subnet mask from communicating with each other.*

```bash
nmap -p- <IP target>
```

```text
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
10001/tcp open  scp-config

We'll scan the port 22 because normally can give us more information about the host

```bash
nc -nv <IP target>
```
-n deactivate DNS resolution
-v activate the verbose mode: the terminal shows us diagnostic state

We use the NetCat command because is more quietly than nmap.

## Medium Lab

* *Question 1*
 *  *After the configurations are transferred to the system, our client wants to know if it is possible to find out our target's DNS server version. Submit the DNS server version of the target as the answer.*
 *  *HINT: During the meeting, the administrators talked about the host we tested as a publicly accessible server that was not mentioned before.*

```bash
sudo nmap -T2 -D RND:5 <IP target>
```
sudo changes the nmap determinate behavior and perform a SYN scan instead of performing a complete TCP connection scan
-T2 Configures the timing template. Nmap has six levels, from -T0 to -T5. The -T2 level is called Polite and deliberately slows down the scan by introducing pauses between sent packets. By not bombarding the server all at once, you avoid triggering IDS alarms based on rapid traffic volume.
-D RND:5 Enables the use of Decoys. It instructs Nmap to generate 5 random IP addresses (RND:5) and mix them with your real IP address. If the server administrator analyzes the logs, they will see that 6 different machines are scanning it at the same time, making your real IP blend in with the noise

```text
PORT    STATE    SERVICE
21/tcp  open     ftp
22/tcp  open     ssh
53/tcp  filtered domain
80/tcp  open     http
110/tcp open     pop3
139/tcp open     netbios-ssn
143/tcp open     imap
445/tcp filtered microsoft-ds

```bash
sudo nmap -sU -p 53 -sV <IP target>
```
-sU we use UDP instead of TCP because the DNS server use UDP connection
-p 53 is the port used for the DNS server

## Hard Lab

* *Question 1*
 * *Now our client wants to know if it is possible to find out the version of the running services. Identify the version of service our client was talking about and submit the flag as the answer.*

```bash
sudo nmap -T2 -D RND:5 <IP target>
```

```text
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

We didn't get useful information. Instead, we'll use another command

```bash
sudo nmap <IP target> --source-port 53
```
--source-port 53 it forces all packets leaving your machine to carry the "tag" indicating they originate from port 53. Since this is the universal port for the DNS service, many firewalls are configured with permissive rules and allow the scan to pass through, letting you see ports that would normally be blocked

```text
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
50000/tcp open  ibm-db2

We found a new port.

```bash
sudo ncat -nv -s <Our IP> --source-port 53 <IP target> <Port>
```
We will use the port 50000 with that command and pressing enter we'll find the flag

