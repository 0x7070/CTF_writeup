
---

# Version Info
``` bash
# Kali OS [b] - Running from a VirtualBox VM hosted on Ubuntu version [a]

# ---

# [a]:
host:~$ cat /etc/os-release 
PRETTY_NAME="Kali GNU/Linux Rolling"
NAME="Kali GNU/Linux"
VERSION_ID="2024.4"
VERSION="2024.4"
VERSION_CODENAME=kali-rolling
ID=kali
ID_LIKE=debian
HOME_URL="https://www.kali.org/"
SUPPORT_URL="https://forums.kali.org/"
BUG_REPORT_URL="https://bugs.kali.org/"
ANSI_COLOR="1;31"

# VirtualBox version
Version 7.1.4 r165100 (Qt6.4.2)

# ---

# [b]:
kali:~$ cat /etc/os-release
PRETTY_NAME="Kali GNU/Linux Rolling"
NAME="Kali GNU/Linux"
VERSION_ID="2024.4"
VERSION="2024.4"
VERSION_CODENAME=kali-rolling
ID=kali
ID_LIKE=debian
HOME_URL="https://www.kali.org/"
SUPPORT_URL="https://forums.kali.org/"
BUG_REPORT_URL="https://bugs.kali.org/"
ANSI_COLOR="1;31"

# Docker version
kali:~$ docker --version
Docker version 26.1.5+dfsg1, build a72d7cd

# Firefox version
# Dirbuster version

```

# 1. Initialize and configure Docker containers

``` bash
# open a terminal
# ensure Docker is installed (a), or install docker (b)
# (a)
# docker --version
# (b)
# sudo apt install docker -y

# pull Docker images
sudo docker pull professorprofessional/cyberlions-vsftpd234:v1.0
sudo docker pull professorprofessional/cyberlions-apache2:v1.0
```

Should see a successful output for both:
``` bash
┌──(kali㉿kali)-[~]
└─$ sudo docker pull professorprofessional/cyberlions-vsftpd234:v1.0
v1.0: Pulling from professorprofessional/cyberlions-vsftpd234
d9802f032d67: Pull complete 
41b8fe7cdfd1: Pull complete 
80b72d25e429: Pull complete 
901bb8904628: Pull complete 
8040949a19bf: Pull complete 
45065d9d10bc: Pull complete 
f66eb90083b7: Pull complete 
e535bced3036: Pull complete 
29c7d911bc77: Pull complete 
c6c2452d5fe4: Pull complete 
c6382b3ba332: Pull complete 
Digest: sha256:50a819e7a69eacfb456c7055240acb20ecc2f10542024cc41addc65f88ac47c9
Status: Downloaded newer image for professorprofessional/cyberlions-vsftpd234:v1.0
docker.io/professorprofessional/cyberlions-vsftpd234:v1.0

┌──(kali㉿kali)-[~]
└─$ sudo docker pull professorprofessional/cyberlions-apache2:v1.0  
v1.0: Pulling from professorprofessional/cyberlions-apache2
d9802f032d67: Already exists 
48beede29b25: Pull complete 
8e19d85e48f1: Pull complete 
27c3fdf51c6e: Pull complete 
55c084f00bc3: Pull complete 
a4be2415c8ce: Pull complete 
ba5a913d3f4a: Pull complete 
8e1906eea38f: Pull complete 
62c0327efd8e: Pull complete 
Digest: sha256:0158274f91710dfae81f953d9319716c6277ed15ca79287eb7c512c674506ab2
Status: Downloaded newer image for professorprofessional/cyberlions-apache2:v1.0
docker.io/professorprofessional/cyberlions-apache2:v1.0
```

You'll see "already exists" as the containers are built using the same Ubuntu base image, which you'd already have downloaded with the first image

Verify installation
``` bash
┌──(kali㉿kali)-[~]
└─$ sudo docker images
REPOSITORY                                   TAG       IMAGE ID       CREATED      SIZE
professorprofessional/cyberlions-apache2     v1.0      d5d167c50eaa   5 days ago   260MB
professorprofessional/cyberlions-vsftpd234   v1.0      50178d3c26d7   5 days ago   385MB
```

==Explain these==
``` bash
sudo docker rmi {iid}
sudo docker kill {cid}
sudo docker remove {cid}
```

### 1b. Spin up Docker containers

Verify nothing is running on ports 20,21,80
``` bash
ss | grep -w -e '20' -e '21' -e '80'
```

If there are open ports that **are not** a essential to the current session, terminate them, then re-verify
``` bash
sudo kill -9 {pid},{pid},...
```

If the ports **are** essential to the current session, follow option 2. 

Option 1:
```
# spin up Docker containers with default configuration
┌──(kali㉿kali)-[~]
└─$ sudo docker run -d -p 20:20 -p 21:21 professorprofessional/cyberlions-vsftpd234:v1.0
┌──(kali㉿kali)-[~]
└─$ sudo docker run -d -p 80:80 professorprofessional/cyberlions-apache2:v1.0
```

Option 2: ==find a method to use other ports==
``` bash
# spin up Docker containers
```

You'll see container IDs if they have been ==spun up (DEFINE)==. This does not always mean a container has been successfully initialized, however -- though there may not have been any errors printed during executing, there may still be issues with configuration.

Verify the containers have successfully been initialized
``` bash
┌──(kali㉿kali)-[~]
└─$ sudo docker ps -a                                                                   
CONTAINER ID   IMAGE                                             COMMAND                  CREATED              STATUS              PORTS                                           NAMES
cf452852d471   professorprofessional/cyberlions-vsftpd234:v1.0   "/usr/local/sbin/vsf…"   About a minute ago   Up About a minute   0.0.0.0:20-21->20-21/tcp, :::20-21->20-21/tcp   competent_blackburn
332ee0e1fc2d   professorprofessional/cyberlions-apache2:v1.0     "apache2ctl -D FOREG…"   2 minutes ago        Up 2 minutes        0.0.0.0:80->80/tcp, :::80->80/tcp               pensive_chatterjee
```
We're looking for the "up ..." in the "STATUS" column. Anything else will give us functionality issues if unintentionally set to that. 

> It is important to practice as if we are within a real environment, so we will proceed as if we are unaware of any of the past commands being executed -- we will assume we are only aware of the host node we have been given.
> Additionally, Kali is running on a bridged network, so it will have its own. Similarly the Docker containers are running within a virtualized bridged network, so they are sharing the hosts (kali's) network resources, but are accessible internally as their individually unique nodes. 
> Here are a few possible network topologies for our initial environment:
> Here is my current network topology with respect to what we *would* know at this point:

![sample network topology][./resources/cropdiag.png]]

**Ubuntu host, Kali bridged with host, unknown Docker services bridged with Kali**
Ubuntu host
|- Kali Attack
  |- Docker services

> You may have a similar network topology, but ultimately, the way you proceed will be determined by the next step.

---
# 2. Identify network topology

``` bash
┌──(kali㉿kali)-[~]
└─$ ip address show docker0
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:4c:37:57:bb brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:4cff:fe37:57bb/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
```

`ip addr show` will list all registered network interfaces --  we are only interested in `docker0`, which should be `UP`, and which is the node we will be probing first:

Verify connection through `ping` (-c is optional here)

``` bash

┌──(kali㉿kali)-[~]
└─$ ping -c 4 172.17.0.1
PING 172.17.0.1 (172.17.0.1) 56(84) bytes of data.
64 bytes from 172.17.0.1: icmp_seq=1 ttl=64 time=0.050 ms
64 bytes from 172.17.0.1: icmp_seq=2 ttl=64 time=0.041 ms
64 bytes from 172.17.0.1: icmp_seq=3 ttl=64 time=0.048 ms
64 bytes from 172.17.0.1: icmp_seq=4 ttl=64 time=0.034 ms

--- 172.17.0.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3077ms
rtt min/avg/max/mdev = 0.034/0.043/0.050/0.006 ms
```

Scan Docker service (172.17.0.1) and identify services through a surface level scan
```
┌──(kali㉿kali)-[~]
└─$ nmap 172.17.0.1
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-10 19:44 EST
Nmap scan report for 172.17.0.1
Host is up (0.0000090s latency).
Not shown: 997 closed tcp ports (reset)
PORT      STATE    SERVICE
32768/tcp filtered filenet-tms
32769/tcp filtered filenet-rpc
32770/tcp filtered sometimes-rpc3

Nmap done: 1 IP address (1 host up) scanned in 1.30 seconds
```
Nmap identified 3 filtered ports on the network, which we will not probe further, since they are already being filtered.

From this point onward, we will refer to the Docker service (172.17.0.1) as the Docker Gateway.

### 2a. Scan the 172.17.0.255 subnet (0.0->0.255)

Instead, we can search for additional nodes besides the Docker service we just probed. This scan is relatively noisy, and is only being used as we are in a controlled environment.
```
┌──(kali㉿kali)-[~]
└─$ nmap -sn -T5 --min-rate 10000 172.17.0.1/24 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-10 19:55 EST
Nmap scan report for 172.17.0.2
Host is up (0.000024s latency).
MAC Address: 02:42:AC:11:00:02 (Unknown)
Nmap scan report for 172.17.0.3
Host is up (0.0000080s latency).
MAC Address: 02:42:AC:11:00:03 (Unknown)
Nmap scan report for 172.17.0.1
Host is up.
Nmap done: 256 IP addresses (3 hosts up) scanned in 0.23 seconds
```

### 2b. Probe 172.17.0.2

Here, we learn of two additional nodes on the network -- (172.17.0.2) (172.17.0.3). Let's find out more about each of them. We'll begin with 172.17.0.2:
``` bash
┌──(kali㉿kali)-[~]
└─$ nmap 172.17.0.2                            
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-10 20:06 EST
Nmap scan report for 172.17.0.2
Host is up (0.0000050s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http
MAC Address: 02:42:AC:11:00:02 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.16 seconds
```
We've discovered an open HTTP port on 80. Analyzing the scan results, we find the following information about the node 172.17.0.2:
Port 80 is open
|- It is serving HTTP

However, that alone doesn't give us much information. Lets see if we can discover anything else.

By performing the following scan, we are probing not only the host (172.17.0.2) itself, but also its open ports and the services it is hosting:
``` bash
┌──(kali㉿kali)-[~]
└─$ nmap -sC -sV -p 80 172.17.0.2
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-10 20:27 EST
Nmap scan report for 172.17.0.2
Host is up (0.000031s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Login Page
MAC Address: 02:42:AC:11:00:02 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.48 seconds
```
Now, we have identified the version, as well as the title page -- the front facing page --, among other things. We'll save this information for later.

Let's continue identifying the network topology.
### 2c. Probe 172.17.0.3

``` bash
┌──(kali㉿kali)-[~]
└─$ nmap 172.17.0.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-10 21:02 EST
Nmap scan report for 172.17.0.3
Host is up (0.0000080s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:AC:11:00:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.18 seconds
```

Again, this doesn't tell us much. Let's proceed with the same noisy scan from earlier, substituting `-p 80` for `-p 21`:
```
┌──(kali㉿kali)-[~]
└─$ nmap -sC -sV -p 21 172.17.0.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-10 21:03 EST
Nmap scan report for 172.17.0.3
Host is up (0.000024s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 172.17.0.1
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxr--    4 1001     1001         4096 Dec 04 15:46 beary
| drwxrwxr--    3 1002     1002         4096 Dec 04 15:46 franklin
| -rwxrwxr-x    1 0        0          134881 Dec 04 02:41 key.png
|_-rwxrwxr-x    1 0        0          689793 Dec 04 02:41 lock.png
MAC Address: 02:42:AC:11:00:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.44 seconds
```
That's a lot of information! Ignoring the details of the output, we can assume the following network topology, excluding the topology preceding our Kali attack machine, as that is user-dependent.

==make diagram==
┌─Kali Attack
\
(Virtual Bridge)
/
└─Docker Gateway└─ ─ 
   | - 172.17.0.1
   | - 80/tcp (filtered)
   | - 21/tcp (filtered)
   |
   ├── Docker Container A
   |     - 172.17.0.2
   |     - Apache 2.4.41
   |     - 80/tcp (open - HTTP)
   |
   ├── Docker Container B
   |     - 172.17.0.3
   |     - vsFTPd 2.3.4
   |     - 21/tcp (open - FTP)

Let's save this for later, and turn back to the HTTP serer, as that is likely the simplest attack surface.

# 3. Assess the HTTP server

Navigating to http://172.17.0.2:80/ will verify that the front-facing page is indeed titled "Login Page"

> Note that the `:80` is not necessary in this case, as http traffic defaults to port 80. If the web server were to be hosted on a different port -- we'll use 9999 in this example -- we would then connect to http://172.17.0.2:999/

![[loginpage.png]]

We can try a few things here.

### 3a. Guess login credentials

First, we can try to guess the credentials. Again, this is easily detectable by any activity analysis, but as we're in a closed environment, we will go ahead.

```
admin
pass

admin
password

user 
pass

user
password
```

We don't have much knowledge of the system or the users that hold credentials, so this understandably leads us nowhere.

### 3b-a. View page source code

Second, we can view source code.

**On Firefox**

`RightClick > View Page Source`
Here, we are interested in the following bit of HTML:
``` html
<img src=[resources/login_icon_alt.png] alt="Login icon">
```

This indicates the existence of both the `resources` directory and the `login_icon_alt.png` png within it. Nothing else seems out of the ordinary, so let's see what is accessible at the path we just discovered.

`http://172.17.0.2:80/resources`
![[resources_dir.png]]

Let's go further.

`http://172.17.0.2:80/resources/login_icon_alt.png`
![[login_icon_alt.png]]
Just a simple png. We could look at metadata and binary information, but at this point, there's no reason to pursue an image. 

`http://172.17.0.2:80/resources/bcred.txt`
![[bcred.png]]
Interesting! It looks like we have a pair of hashed credentials.

### 3b-b. Hash analysis

Let's analyze these hashes.

In the first column (delimited by the colon `:`), we can assume these are usernames. In the second column, thanks to the hash identifier `$2y$`, we have what looks to be password hashes.

By doing some research, we can positively identify these hashes as an iteration of the bcrypt hashing algorithm. ==more information can be found here==. time to crack (https://specopssoft.com/blog/hashing-algorithm-cracking-bcrypt-passwords/) Furthermore, we can conclude that cracking these hashes is unrealistic, seen in this time-to-crack table. 

### 3c-a. Brute force paths with Dirbuster

Though viewing source code got us to the paths not initially visible to the user, it can become tedious and increasingly difficult with large code-bases. However, it is open-source, and is difficult to detect.

Alternatively, as we are in a closed environment without any concerns to firewalls or threat detection, we can take the more aggressive approach: Dirbuster. We will proceed as if we have just reached the login page and have not yet discovered any internal paths.

==explain dirbuster and how it uses errors to identify==
==explain both modes - brude and dict==

For this approach, we'll opt for the GUI, as it is more user friendly, and provides us with some nice functionality.

#### Open Dirbuster
##### Terminal

``` bash
# Terminal
┌──(kali㉿kali)-[~]
└─$ dirbuster
```

##### Kali search menu

![[menu.png]]

![[menu_dirbuster.png]]

==add arrow icons==

Either method will open the an identical Dirbuster GUI.

Set the following configuration:
```
- Target URL (eg http://example.com:80/)
> Enter "http://172.17.0.2:80/" (without quotes)

- Select scanning type:
> Check "Pure Brute Force"

|> Start
```

The result of this type of brute force will vary based on your connection, hardware, and available resources. 

Looking at the bottom left, we given a time-of-completion estimate. For this specific attack we have configured, it will take an unbelievably long amount of time -- we have not made any configurations that would result in a particularly 'efficient' brute force attack. Fortunately, however, the paths within the web server aren't obfuscated, and are found relatively quickly. 

Still, let's explore another potential configuration.  



