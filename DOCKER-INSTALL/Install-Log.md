

![image](https://github.com/05t3/Homelab/assets/58165365/875c6d7d-4209-4862-bd2d-eb810ca38df5)


```bash
root@analyst:/home/analyst# cd /etc/netplan/
root@analyst:/etc/netplan# ls -la
total 12
drwxr-xr-x  2 root root 4096 May 17 21:32 .
drwxr-xr-x 98 root root 4096 May 17 22:26 ..
-rw-r--r--  1 root root  116 May 17 21:32 00-installer-config.yaml
root@analyst:/etc/netplan# cp 00-installer-config.yaml 00-installer-config.yaml.bak
root@analyst:/etc/netplan# cat 00-installer-config.yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      dhcp4: true
  version: 2
root@analyst:/etc/netplan# nano 00-installer-config.yaml
root@analyst:/etc/netplan# cat 00-installer-config.yaml
# This is the network config written by 'subiquity'
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.1.51/24
      gateway4: 192.168.1.1
      nameservers:
          addresses: [192.168.1.100, 8.8.8.8]
root@analyst:/etc/netplan# netplan apply

```


```bash
  18/05/2023   01:33.08   /home/mobaxterm  ssh analyst@192.168.1.51
Warning: Permanently added '192.168.1.51' (ED25519) to the list of known hosts.
analyst@192.168.1.51's password:
analyst@192.168.1.51's password:
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-125-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 17 May 2023 10:33:31 PM UTC

  System load:  0.27               Processes:              241
  Usage of /:   35.7% of 18.53GB   Users logged in:        1
  Memory usage: 15%                IPv4 address for ens33: 192.168.1.51
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status

New release '22.04.2 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


*** System restart required ***
Last login: Wed May 17 22:31:16 2023 from 192.168.1.103
analyst@analyst:~$ sudo su
[sudo] password for analyst:
root@analyst:/home/analyst# apt-get install ca-certificates curl gnupg -y
Reading package lists... Done
Building dependency tree
Reading state information... Done
ca-certificates is already the newest version (20211016ubuntu0.20.04.1).
ca-certificates set to manually installed.
curl is already the newest version (7.68.0-1ubuntu2.18).
curl set to manually installed.
gnupg is already the newest version (2.2.19-3ubuntu2.2).
gnupg set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
root@analyst:/home/analyst#  sudo install -m 0755 -d /etc/apt/keyrings
root@analyst:/home/analyst#  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
root@analyst:/home/analyst#  sudo chmod a+r /etc/apt/keyrings/docker.gpg
root@analyst:/home/analyst# echo \
>   "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
>   "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
>   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
root@analyst:/home/analyst# sudo apt-get update
Hit:1 http://ke.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://ke.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:3 http://ke.archive.ubuntu.com/ubuntu focal-backports InRelease
Get:4 https://download.docker.com/linux/ubuntu focal InRelease [57.7 kB]
Hit:5 http://ke.archive.ubuntu.com/ubuntu focal-security InRelease
Get:6 https://download.docker.com/linux/ubuntu focal/stable amd64 Packages [28.9 kB]
Fetched 86.6 kB in 1s (83.4 kB/s)
Reading package lists... Done
root@analyst:/home/analyst# sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  docker-ce-rootless-extras pigz slirp4netns
Suggested packages:
  aufs-tools cgroupfs-mount | cgroup-lite
The following NEW packages will be installed:
  containerd.io docker-buildx-plugin docker-ce docker-ce-cli docker-ce-rootless-extras docker-compose-plugin pigz slirp4netns
0 upgraded, 8 newly installed, 0 to remove and 0 not upgraded.
Need to get 110 MB of archives.
After this operation, 400 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://ke.archive.ubuntu.com/ubuntu focal/universe amd64 pigz amd64 2.4-1 [57.4 kB]
Get:2 https://download.docker.com/linux/ubuntu focal/stable amd64 containerd.io amd64 1.6.21-1 [28.3 MB]
Get:3 http://ke.archive.ubuntu.com/ubuntu focal/universe amd64 slirp4netns amd64 0.4.3-1 [74.3 kB]
Get:4 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-buildx-plugin amd64 0.10.4-1~ubuntu.20.04~focal [25.9 MB]
Get:5 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-ce-cli amd64 5:24.0.0-1~ubuntu.20.04~focal [13.3 MB]
Get:6 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-ce amd64 5:24.0.0-1~ubuntu.20.04~focal [22.8 MB]
Get:7 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-ce-rootless-extras amd64 5:24.0.0-1~ubuntu.20.04~focal [9,018 kB]
Get:8 https://download.docker.com/linux/ubuntu focal/stable amd64 docker-compose-plugin amd64 2.17.3-1~ubuntu.20.04~focal [10.9 MB]
Fetched 110 MB in 2min 11s (840 kB/s)
Selecting previously unselected package pigz.
(Reading database ... 108791 files and directories currently installed.)
Preparing to unpack .../0-pigz_2.4-1_amd64.deb ...
Unpacking pigz (2.4-1) ...
Selecting previously unselected package containerd.io.
Preparing to unpack .../1-containerd.io_1.6.21-1_amd64.deb ...
Unpacking containerd.io (1.6.21-1) ...
Selecting previously unselected package docker-buildx-plugin.
Preparing to unpack .../2-docker-buildx-plugin_0.10.4-1~ubuntu.20.04~focal_amd64.deb ...
Unpacking docker-buildx-plugin (0.10.4-1~ubuntu.20.04~focal) ...
Selecting previously unselected package docker-ce-cli.
Preparing to unpack .../3-docker-ce-cli_5%3a24.0.0-1~ubuntu.20.04~focal_amd64.deb ...
Unpacking docker-ce-cli (5:24.0.0-1~ubuntu.20.04~focal) ...
Selecting previously unselected package docker-ce.
Preparing to unpack .../4-docker-ce_5%3a24.0.0-1~ubuntu.20.04~focal_amd64.deb ...
Unpacking docker-ce (5:24.0.0-1~ubuntu.20.04~focal) ...
Selecting previously unselected package docker-ce-rootless-extras.
Preparing to unpack .../5-docker-ce-rootless-extras_5%3a24.0.0-1~ubuntu.20.04~focal_amd64.deb ...
Unpacking docker-ce-rootless-extras (5:24.0.0-1~ubuntu.20.04~focal) ...
Selecting previously unselected package docker-compose-plugin.
Preparing to unpack .../6-docker-compose-plugin_2.17.3-1~ubuntu.20.04~focal_amd64.deb ...
Unpacking docker-compose-plugin (2.17.3-1~ubuntu.20.04~focal) ...
Selecting previously unselected package slirp4netns.
Preparing to unpack .../7-slirp4netns_0.4.3-1_amd64.deb ...
Unpacking slirp4netns (0.4.3-1) ...
Setting up slirp4netns (0.4.3-1) ...
Setting up docker-buildx-plugin (0.10.4-1~ubuntu.20.04~focal) ...
Setting up containerd.io (1.6.21-1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /lib/systemd/system/containerd.service.
Setting up docker-compose-plugin (2.17.3-1~ubuntu.20.04~focal) ...
Setting up docker-ce-cli (5:24.0.0-1~ubuntu.20.04~focal) ...
Setting up pigz (2.4-1) ...
Setting up docker-ce-rootless-extras (5:24.0.0-1~ubuntu.20.04~focal) ...
Setting up docker-ce (5:24.0.0-1~ubuntu.20.04~focal) ...
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/systemd/system/docker.socket.
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for systemd (245.4-4ubuntu3.21) ...
root@analyst:/home/analyst# sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
719385e32844: Pull complete
Digest: sha256:fc6cf906cbfa013e80938cdf0bb199fbdbb86d6e3e013783e5a766f50f5dbce0
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

root@analyst:/home/analyst# docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
888a9126b617   hello-world   "/hello"   46 seconds ago   Exited (0) 45 seconds ago             objective_kare
root@analyst:/home/analyst#  sudo apt-get update
Hit:1 https://download.docker.com/linux/ubuntu focal InRelease
Hit:2 http://ke.archive.ubuntu.com/ubuntu focal InRelease
Hit:3 http://ke.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:4 http://ke.archive.ubuntu.com/ubuntu focal-backports InRelease
Hit:5 http://ke.archive.ubuntu.com/ubuntu focal-security InRelease
Reading package lists... Done
root@analyst:/home/analyst#  sudo apt-get install docker-compose-plugin
Reading package lists... Done
Building dependency tree
Reading state information... Done
docker-compose-plugin is already the newest version (2.17.3-1~ubuntu.20.04~focal).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
root@analyst:/home/analyst#  docker compose version
Docker Compose version v2.17.3
root@analyst:/home/analyst# shutdown now
Connection to 192.168.1.51 closed by remote host.
Connection to 192.168.1.51 closed.
```