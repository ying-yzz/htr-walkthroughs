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
