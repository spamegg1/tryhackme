# Network Services 1

## Task 1: Get Connected

Hello and welcome!

This room will explore common Network Service vulnerabilities and
misconfigurations, but in order to do that, we'll need to do a few things first!

A basic knowledge of Linux, and how to navigate the Linux file system, is required for
this room. If you think you'll need some help with this, try completing the
['Linux Fundamentals' Module](https://tryhackme.com/module/linux-fundamentals)

1. Connect to the TryHackMe OpenVPN Server (See <https://tryhackme.com/access> for help!)
2. Make sure you're sitting comfortably, and have a cup of Tea, Coffee or Water close!

Now, let's move on!

N.B. This is not a room on WiFi access hacking or hijacking, rather how to gain
unauthorized access to a machine by exploiting network services.
If you are interested in WiFi hacking, I suggest checking out
[WiFi Hacking 101 by NinjaJc01](https://tryhackme.com/room/wifihacking101)

***Answer the questions below***

Ready? Let's get going!

*Solution:* Connect to Tryhackme through OpenVPN as described in above link.
No need to start AttackBox, since I'll be using my own PC connected to Tryhackme.
Then start machine in each task.

***Correct answer: No answer needed***

## Task 2: Understanding SMB

### What is SMB?

SMB - Server Message Block Protocol - is a client-server communication protocol used for
sharing access to files, printers, serial ports and other resources on a network.
[source](https://searchnetworking.techtarget.com/definition/Server-Message-Block-Protocol)

Servers make file systems and other resources (printers, named pipes, APIs) available to
clients on the network. Client computers may have their own hard disks,
but they also want access to the shared file systems and printers on the servers.

The SMB protocol is known as a response-request protocol, meaning that it transmits
multiple messages between the client and server to establish a connection. Clients
connect to servers using TCP/IP (actually NetBIOS over TCP/IP
as specified in RFC1001 and RFC1002), NetBEUI or IPX/SPX.

### How does SMB work?

![smb1](smb1.png)

Once they have established a connection, clients can then send commands (SMBs) to the
server that allow them to access shares, open files, read and write files, and generally
do all the sort of things that you want to do with a file system.
However, in the case of SMB, these things are done over the network.

### What runs SMB?

Microsoft Windows operating systems since Windows 95 have included client
and server SMB protocol support. Samba, an open source server
that supports the SMB protocol, was released for Unix systems.

***Answer the questions below***

What does SMB stand for?

***Correct answer: Server Message Block***

What type of protocol is SMB?

***Correct answer: response-request***

What protocol suite do clients use to connect to the server?

***Correct answer: TCP/IP***

What systems does Samba run on?

***Correct answer: Unix***

## Task 3: Enumerating SMB

### Lets Get Started

Before we begin, make sure to deploy the room and give it some time to boot.
Please be aware, this can take up to five minutes so be patient!

### Enumeration

Enumeration is the process of gathering information on a target
in order to find potential attack vectors and aid in exploitation.

This process is essential for an attack to be successful, as wasting time with exploits
that either don't work or can crash the system can be a waste of energy. Enumeration can
be used to gather usernames, passwords, network information, hostnames, application data,
services, or any other information that may be valuable to an attacker.

### SMB

Typically, there are SMB share drives on a server that can be connected to and used to
view or transfer files. SMB can often be a great starting point for an
attacker looking to discover sensitive information,
you'd be surprised what is sometimes included on these shares.

### Port Scanning

The first step of enumeration is to conduct a port scan, to find out as
much information as you can about the services, applications,
structure and operating system of the target machine.

If you haven't already looked at port scanning, I recommend checking out the Nmap room
[here](https://tryhackme.com/room/furthernmap).

### Enum4Linux

Enum4linux is a tool used to enumerate SMB shares on both Windows and Linux systems. It
is basically a wrapper around the tools in the Samba package and makes it easy to quickly
extract information from the target pertaining to SMB. It's already installed on the
AttackBox, however if you need to install it on your own attacking machine,
you can do so from the official [Github](https://github.com/portcullislabs/enum4linux).

The syntax of Enum4Linux is nice and simple: "`enum4linux [options] ip`"

|TAG|FUNCTION|
|:-|:-|
|-U|get userlist|
|-M|get machine list|
|-N|get namelist dump (different from -U and-M)|
|-S|get sharelist|
|-P|get password policy information|
|-G|get group and member list|
|-a|all of the above (full basic enumeration)|

Now we understand our enumeration tools, let's get started!

***Answer the questions below***

Conduct an **nmap** scan of your choosing, How many ports are open?

*Solution:*

```bash
❯ export ip=10.10.215.31
❯ nmap $ip -vvv
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-22 11:53 +03
Initiating Ping Scan at 11:53
Scanning 10.10.215.31 [2 ports]
Completed Ping Scan at 11:53, 0.07s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 11:53
Completed Parallel DNS resolution of 1 host. at 11:53, 0.03s elapsed
DNS resolution of 1 IPs took 0.03s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating Connect Scan at 11:53
Scanning 10.10.215.31 [1000 ports]
Discovered open port 445/tcp on 10.10.215.31
Discovered open port 22/tcp on 10.10.215.31
Discovered open port 139/tcp on 10.10.215.31
Completed Connect Scan at 11:53, 3.18s elapsed (1000 total ports)
Nmap scan report for 10.10.215.31
Host is up, received conn-refused (0.096s latency).
Scanned at 2025-05-22 11:53:51 +03 for 3s
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE      REASON
22/tcp  open  ssh          syn-ack
139/tcp open  netbios-ssn  syn-ack
445/tcp open  microsoft-ds syn-ack

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 3.30 seconds
```

***Correct answer: 3***

What ports is **SMB** running on? Provide the ports in ascending order.

*Solution:*

SMB requires different network ports on a machine to enable communications with other
systems. SMB originally ran on top of NetBIOS, which uses port 139. NetBIOS is an older
transport layer which allows computers to talk to each other on the network. The SMB
protocol runs on port 445, but may rely on NetBIOS to communicate with
old devices that do not support the direct hosting of SMB over TCP/IP.

***Correct answer: 139/445***

Let's get started with Enum4Linux, conduct a full basic enumeration.
For starters, what is the **workgroup** name?

*Solution:*

I am using the improved version of Enum4Linux,
on [Github](https://github.com/cddmp/enum4linux-ng/)

```bash
❯ enum4linux-ng.py $ip -A
ENUM4LINUX - next generation (v1.3.4)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.10.215.31
[*] Username ......... ''
[*] Random Username .. 'wvremhel'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =====================================
|    Listener Scan on 10.10.215.31    |
 =====================================
[*] Checking LDAP
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 ===========================================================
|    NetBIOS Names and Workgroup/Domain for 10.10.215.31    |
 ===========================================================
[+] Got domain/workgroup name: WORKGROUP
[+] Full NetBIOS names information:
- POLOSMB         <00> -         B <ACTIVE>  Workstation Service
- POLOSMB         <03> -         B <ACTIVE>  Messenger Service
- POLOSMB         <20> -         B <ACTIVE>  File Server Service
- ..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
- WORKGROUP       <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
- WORKGROUP       <1d> -         B <ACTIVE>  Master Browser
- WORKGROUP       <1e> - <GROUP> B <ACTIVE>  Browser Service Elections
- MAC Address = 00-00-00-00-00-00

 =========================================
|    SMB Dialect Check on 10.10.215.31    |
 =========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:
  SMB 1.0: true
  SMB 2.02: true
  SMB 2.1: true
  SMB 3.0: true
  SMB 3.1.1: true
Preferred dialect: SMB 3.0
SMB1 only: false
SMB signing required: false

 ===========================================================
|    Domain Information via SMB session for 10.10.215.31    |
 ===========================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: POLOSMB
NetBIOS domain name: ''
DNS domain: ''
FQDN: polosmb
Derived membership: workgroup member
Derived domain: unknown

 =========================================
|    RPC Session Check on 10.10.215.31    |
 =========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for random user
[+] Server allows session using username 'wvremhel', password ''
[H] Rerunning enumeration with user 'wvremhel' might give more results

 ===================================================
|    Domain Information via RPC for 10.10.215.31    |
 ===================================================
[+] Domain: WORKGROUP
[+] Domain SID: NULL SID
[+] Membership: workgroup member

 ===============================================
|    OS Information via RPC for 10.10.215.31    |
 ===============================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[+] Found OS information via 'srvinfo'
[+] After merging OS information we have the following result:
OS: Linux/Unix (Samba 4.7.6-Ubuntu)
OS version: '6.1'
OS release: ''
OS build: '0'
Native OS: Windows 6.1
Native LAN manager: Samba 4.7.6-Ubuntu
Platform id: '500'
Server type: '0x809a03'
Server type string: Wk Sv PrQ Unx NT SNT polosmb server (Samba, Ubuntu)

 =====================================
|    Users via RPC on 10.10.215.31    |
 =====================================
[*] Enumerating users via 'querydispinfo'
[+] Found 0 user(s) via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 0 user(s) via 'enumdomusers'

 ======================================
|    Groups via RPC on 10.10.215.31    |
 ======================================
[*] Enumerating local groups
[+] Found 0 group(s) via 'enumalsgroups domain'
[*] Enumerating builtin groups
[+] Found 0 group(s) via 'enumalsgroups builtin'
[*] Enumerating domain groups
[+] Found 0 group(s) via 'enumdomgroups'

 ======================================
|    Shares via RPC on 10.10.215.31    |
 ======================================
[*] Enumerating shares
[+] Found 4 share(s):
IPC$:
  comment: IPC Service (polosmb server (Samba, Ubuntu))
  type: IPC
netlogon:
  comment: Network Logon Service
  type: Disk
print$:
  comment: Printer Drivers
  type: Disk
profiles:
  comment: Users profiles
  type: Disk
[*] Testing share IPC$
[-] Could not check share: STATUS_OBJECT_NAME_NOT_FOUND
[*] Testing share netlogon
[-] Share doesn't exist
[*] Testing share print$
[+] Mapping: DENIED, Listing: N/A
[*] Testing share profiles
[+] Mapping: OK, Listing: OK

 =========================================
|    Policies via RPC for 10.10.215.31    |
 =========================================
[*] Trying port 445/tcp
[+] Found policy:
Domain password information:
  Password history length: None
  Minimum password length: 5
  Maximum password age: 49710 days (136 years) 6 hours 21 minutes
  Password properties:
  - DOMAIN_PASSWORD_COMPLEX: false
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
Domain lockout information:
  Lockout observation window: 30 minutes
  Lockout duration: 30 minutes
  Lockout threshold: None
Domain logoff information:
  Force logoff time: 49710 days (136 years) 6 hours 21 minutes

 =========================================
|    Printers via RPC for 10.10.215.31    |
 =========================================
[+] No printers returned (this is not an error)

Completed after 19.61 seconds
```

***Correct answer: WORKGROUP***

What comes up as the **name** of the machine?

***Correct answer: POLOSMB***

What operating system **version** is running?

***Correct answer: 6.1***

What share sticks out as something we might want to investigate?

***Correct answer:***

## Task 4

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 5

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 6

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 7

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 8

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 9

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 10

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 11

### Subheading

***Answer the questions below***

***Correct answer:***
