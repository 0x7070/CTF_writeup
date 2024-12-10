
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

```

# 1a. Initialize and configure Docker containers

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

# 1b. Spin up Docker containers

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
> Here are two possible network topologies:

nmap -sc 172.17.255.255/24
nmap -sc 172.17.0.1/24
get nmap for os version
make topographies based off of all that 

![[cropdiag.png]]