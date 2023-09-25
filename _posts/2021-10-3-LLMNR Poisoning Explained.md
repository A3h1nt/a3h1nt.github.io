---
title: LLMNR Poisoning
tags: ActiveDirectory LLMNR psexec Responder
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

## LLMNR Poisoning
LLMNR stands for link local multicast name resolution, which is simply a name resolution protocol used by windows in an active directory environment, LLMNR protocol is used when the DNS server fails to resolve a name, and as a result the computer sends a broadcast message to the entire network to resolve that name.
The order of preference is
1.  DNS
2.  mDNS
3.  LLMNR
4.  NBNS

So if i try to access a file share and i mistyped the spelling as *fileshar*, which does not exist, we can see the windows machine sending requests using different protocols for name resolution.

![t](/Images/1.png){:.shadow}

### The Problem
The problem with this protocol is that, it broadcasts the request to the entire intranet, meaning all the machines on the network would see this request, but there is no mechanism for the computer to verify the authenticity of the responses, as a result an attacker can spoof a response tricking the victim into trusting the malicious server, allowing attacker to steal the NTLM hash of the user.


### Working
![t](/Images/2.png){:.shadow}

1. The victim tries to access `fileshar` and sends a DNS request to resolve it, the DNS server fails to resolve the name.
2. Since DNS failed, the victim machine broadcasts the message using LLMNR protocol to the entire intranet.
3. Attacker being part of the intranet, responds back, spoofing itself as `fileshar` and asks client for the NTLM hash to connect, since there's no way to authenticate this response, the client trusts the attacker's response and sends the NTLM hash.

### Practical
Setup:
1. Windows Server running AD services.
2. A client computer that has joined the domain
3. The Attacker Machine

#### Setting Up Responder
We'll use a tool called responder, that'll basically do everything for us, from spoofing responses to getting the NTML hash. This tool comes pre-installed in kali.

![t](/Images/3.png){:.shadow}

To run, we'll use the following command
```bash
sudo responder -I eth0 -wd -v

# -I : interface
# -w : enable wpad
# -d : Enable answers for netbios domain suffix queries
# -v : verbose
```

Output:
![t](/Images/4.png){:.shadow}

Now we'll wait for the client to enter an invalid name, as soon as the client enters an invalid name.

![t](/Images/5.png){:.shadow}

Our responder will generate spoof replies and get us the NTLM hash.

![t](/Images/6.png){:.shadow}

Analyzing the traffic in wireshark can provide us with more insight.

![t](/Images/7.png){:.shadow}

**Packet 205** : Client sends a request to DNS server to resolve `fileshar`

**Packet 206** : The server responds back with `no such name` as it fails to resolve it

**Packer 207-217** : Now the victim machine starts broadcasting message to the entire intranet.

**Packet 218** : The attacker machine ( `172.16.177.51` ) responds back with the spoofed response and later authenticates the victim to get his NTLM hash.

#### Cracking the Hash
Since we have the NTML hash now, we can use some password cracking tools to crack it, i'll use johntheripper for demonstration purposes.
Command:
```bash
john forjohn --wordlist=/usr/share/wordlists/rockyou.txt
```
Output:
![t](/Images/8.png){:.shadow}

Now we can login using `psexec`.
```bash
psexec.py EVILCORP.local/gideon:'password@123'@172.16.177.50
```
Output:
![t](/Images/9.png){:.shadow}

*Other options, wmiexec, smbexec, metasploit*

In the above example the password used by gideon is pretty simple, which allowed us to crack the hash in no time, but that wouldn't be the case everytime, in other cases we can straight away use the NTLM hash and continue our attack trip.

### Defense 
- Disable LLMNR 
- Monitor network traffic and block LLMNR, NBT-NS and mDNS traffic, if not necessary.
- Enable SMB signing