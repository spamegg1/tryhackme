# Template

## Task 1

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 2

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 3

### Subheading

***Answer the questions below***

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

## Task 9: Enumerating MySQL

### Let's Get Started

Before we begin, make sure to deploy the room and give it some time to boot.
Please be aware, as this can take up to five minutes, so be patient!

### When you would begin attacking MySQL

MySQL is likely not going to be the first point of call when getting initial
information about the server. You can, as we have in previous tasks,
attempt to brute-force default account passwords if you really don't have any
other information; however, in most CTF scenarios,
this is unlikely to be the avenue you're meant to pursue.

### The Scenario

Typically, you will have gained some initial credentials from enumerating other services
that you can then use to enumerate and exploit the MySQL service. As this room focuses on
exploiting and enumerating the network service, for the sake of the scenario, we're going
to assume that you found the credentials: "root:password" while enumerating subdomains of
a web server. After trying the login against SSH unsuccessfully,
you decide to try it against MySQL.

### Requirements

You will want to have MySQL installed on your system to connect to the remote MySQL
server. In case this isn't already installed, you can install it using
`sudo apt install default-mysql-client`.
Don't worry, this won't install the server package on your system,
just the client. Once you install the MySQL client, you can connect using
`mysql -h 10.10.97.114 -u root -p`.

***IMPORTANT!*** `mysql` is not installed in the Tryhackme machine.
And it's not possible to install it with `sudo apt install default-mysql-client`.
Because the Tryhackme machine is blocked from accessing the internet.
So I had to use my own PC for this, I connected to the target machine via OpenVPN.
`sudo apt install default-mysql-client` works on my own PC.

Note: If your mysql client returns a TLS/SSL error, you are advised to add
`--ssl-mode=DISABLED`; therefore your command becomes
`mysql -h 10.10.97.114 --ssl-mode=DISABLED -u root -p`.

Again, we're going to be using Metasploit for this; it's important that you have
Metasploit installed, as it is by default on both Kali Linux and Parrot OS.

### Alternatives

As with the previous task, it's worth noting that everything we will be doing using
Metasploit can also be done either manually or with a set of non-Metasploit tools such as
nmap's mysql-enum script:

- <https://nmap.org/nsedoc/scripts/mysql-enum.html> or
- <https://www.exploit-db.com/exploits/23081>.

I recommend that after you complete this room, you go back and
attempt it manually to make sure you understand the process
that is being used to display the information you acquire.

Okay, enough talk. Let's get going!

***Answer the questions below***

As always, let's start out with a port scan, so we know what
port the service we're trying to attack is running on. What port is MySQL using?

*Solution:*

```bash
# remember: ip=10.10.97.114
❯ nmap -on $ip
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-16 09:28 +03
Nmap scan report for 10.10.97.114
Host is up (0.068s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
3306/tcp open  mysql

Nmap done: 1 IP address (1 host up) scanned in 5.28 seconds
```

***Correct answer: 3306***

Good, now we think we have a set of credentials.
Let's double check that by manually connecting to the MySQL server.
We can do this using the command `mysql -h [IP] -u [username] -p`

*Solution:* Username is `root` and password is `password`:

```bash
❯ export ip=10.10.97.114
❯ mysql -h $ip -u root -p
Enter password: password
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.42-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

***Correct answer: No answer needed***

Okay, we know that our login credentials work.
Let's quit out of this session with "exit" and launch up Metasploit.

*Solution:*

```bash
mysql> exit
Bye
❯ msfconsole
Running the 'init' command for the database:
Existing database found, attempting to start it
Starting database at /home/spam/.msf4/db...pg_ctl: another server might be running; trying to start server anyway
waiting for server to start.... done
server started
success
Metasploit tip: Display the Framework log using the log command, learn
more with help log

# cowsay++
 ____________
< metasploit >
 ------------
       \   ,__,
        \  (oo)____
           (__)    )\
              ||--|| *


       =[ metasploit v6.4.82-dev-                               ]
+ -- --=[ 2,546 exploits - 1,309 auxiliary - 1,680 payloads     ]
+ -- --=[ 431 post - 49 encoders - 13 nops - 9 evasion          ]

Metasploit Documentation: https://docs.metasploit.com/
The Metasploit Framework is a Rapid7 Open Source Project

msf >
```

***Correct answer: No answer needed***

We're going to be using the `mysql_sql` module.

Search for, select and list the options it needs.
What three options do we need to set? (in descending order).

*Solution:*

```bash
msf > use mysql_sql

Matching Modules
================

   #  Name                             Disclosure Date  Rank    Check  Description
   -  ----                             ---------------  ----    -----  -----------
   0  auxiliary/admin/mysql/mysql_sql  .                normal  No     MySQL SQL Generic Query


Interact with a module by name or index. For example info 0, use 0 or use auxiliary/admin/mysql/mysql_sql

[*] Using auxiliary/admin/mysql/mysql_sql
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf auxiliary(admin/mysql/mysql_sql) > options

Module options (auxiliary/admin/mysql/mysql_sql):

   Name  Current Setting   Required  Description
   ----  ---------------   --------  -----------
   SQL   select version()  yes       The SQL to execute.


   Used when connecting via an existing SESSION:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   no        The session to run this module on


   Used when making a new connection via RHOSTS:

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   PASSWORD                   no        The password for the specified username
   RHOSTS                     no        The target host(s), see https://docs.metasplo
                                        it.com/docs/using-metasploit/basics/using-met
                                        asploit.html
   RPORT     3306             no        The target port (TCP)
   USERNAME                   no        The username to authenticate as


View the full module info with the info, or info -d command.

msf auxiliary(admin/mysql/mysql_sql) > set PASSWORD password
PASSWORD => password
msf auxiliary(admin/mysql/mysql_sql) > set RHOSTS 10.10.97.114
RHOSTS => 10.10.97.114
msf auxiliary(admin/mysql/mysql_sql) > set USERNAME root
USERNAME => root
msf auxiliary(admin/mysql/mysql_sql) >
```

***Correct answer: PASSWORD/RHOSTS/USERNAME***

Run the exploit.
By default it will test with the `select version()` command,
what result does this give you?

*Solution:*

```bash
msf auxiliary(admin/mysql/mysql_sql) > run
[*] Running module against 10.10.97.114
[*] 10.10.97.114:3306 - Sending statement: 'select version()'...
[*] 10.10.97.114:3306 -  | 8.0.42-0ubuntu0.20.04.1 |
[*] Auxiliary module execution completed
msf auxiliary(admin/mysql/mysql_sql) >
```

***Correct answer: 8.0.42-0ubuntu0.20.04.1***

Great! We know that our exploit is landing as planned.
Let's try to gain some more ambitious information.
Change the `sql` option to `show databases`.
How many databases are returned?

*Solution:*

```bash
msf auxiliary(admin/mysql/mysql_sql) > set SQL show databases
SQL => show databases
msf auxiliary(admin/mysql/mysql_sql) > run
[*] Running module against 10.10.97.114
[*] 10.10.97.114:3306 - Sending statement: 'show databases'...
[*] 10.10.97.114:3306 -  | information_schema |
[*] 10.10.97.114:3306 -  | mysql |
[*] 10.10.97.114:3306 -  | performance_schema |
[*] 10.10.97.114:3306 -  | sys |
[*] Auxiliary module execution completed
msf auxiliary(admin/mysql/mysql_sql) >
```

***Correct answer: 4***

## Task 10

### Subheading

***Answer the questions below***

***Correct answer:***

## Task 11

### Subheading

***Answer the questions below***

***Correct answer:***
