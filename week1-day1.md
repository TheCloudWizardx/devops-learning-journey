# Week 1 Day 1: First Container Experience

**Date:** February 1, 2025
**Time spent:** 1 hour

---

## Part 1: Concepts I Learned

### Q1: Image vs Container
image is recipe of cake while container is baked cake from the recipe

### Q2: What does "docker run" do?
first check image locally 
then docker hub repository
downloads the image locally
assigns the network and local filesystem in container
runs the container
returns container id


### Q3: Why are containers useful?
Light weight, portable, isolated means multiple apps can be run simultanously without conflicts

---

## Part 2: Installation

### Commands I ran:
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
```

### Verification:
```bash
docker --version
Docker version 29.2.0, build 0b9d198
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
17eec7bbc9d7: Pull complete 
Digest: sha256:05813aedc15fb7b4d732e1be879d3252c1c9c25d885824f6295cab4538cb85cd
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

docker run hello-world
billu@billu:~/cloud/docker/monitoring$ docker run hello-world

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


---

## Part 3: Running Nginx

### Commands executed:
```bash
docker run -d -p 8080:80 --name my-nginx nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
119d43eec815: Already exists 
700146c8ad64: Already exists 
d989100b8a84: Already exists 
500799c30424: Already exists 
10b68cfefee1: Already exists 
57f0dd1befe2: Already exists 
eaf8753feae0: Already exists 
Digest: sha256:c881927c4077710ac4b1da63b83aa163937fb47457950c267d92f7e4dedf4aec
Status: Downloaded newer image for nginx:latest
2f92038c838202157371e56014fd1b7353bd441bf064880f50bb8ed196135a92


docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                     NAMES
2f92038c8382   nginx     "/docker-entrypoint.…"   53 minutes ago   Up 53 minutes   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   nginx

docker logs my-nginx
docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/02/02 13:36:09 [notice] 1#1: using the "epoll" event method
2026/02/02 13:36:09 [notice] 1#1: nginx/1.29.4
2026/02/02 13:36:09 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/02/02 13:36:09 [notice] 1#1: OS: Linux 6.14.0-37-generic
2026/02/02 13:36:09 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:524288
2026/02/02 13:36:09 [notice] 1#1: start worker processes
2026/02/02 13:36:09 [notice] 1#1: start worker process 29
2026/02/02 13:36:09 [notice] 1#1: start worker process 30
2026/02/02 13:36:09 [notice] 1#1: start worker process 31
2026/02/02 13:36:09 [notice] 1#1: start worker process 32
2026/02/02 13:36:09 [notice] 1#1: start worker process 33
2026/02/02 13:36:09 [notice] 1#1: start worker process 34
2026/02/02 13:36:09 [notice] 1#1: start worker process 35
2026/02/02 13:36:09 [notice] 1#1: start worker process 36
10.111.11.28 - - [02/Feb/2026:13:47:43 +0000] "GET / HTTP/1.1" 200 615 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36" "-"
2026/02/02 13:47:43 [error] 29#29: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 10.111.11.28, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "10.111.11.28:8080", referrer: "http://10.111.11.28:8080/"
10.111.11.28 - - [02/Feb/2026:13:47:43 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://10.111.11.28:8080/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36" "-"

```

### Browser access:
- URL: http://localhost:8080
yes

### Exploring inside container:
```bash
docker exec -it my-nginx bash
ls
pwd
exit
root@2f92038c8382:/# ls       
bin  boot  dev	docker-entrypoint.d  docker-entrypoint.sh  etc	home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@2f92038c8382:/# ^C
root@2f92038c8382:/# exit

```

### Container lifecycle:
```bash
docker stop my-nginx
docker ps -a
docker start my-nginx
docker stop my-nginx
docker rm my-nginx
# [Paste outputs]
```

---

## Part 4: Experiments

### Experiment 1: Different port (9000)
docker run -d -p 9000:80 nginx
```bash
docker run -d -p 9000:80 --name nginx-9000 nginx
```
**Result:** [Could you access http://localhost:9000? What did you see?]
yes, it's showing
### Experiment 2: No port mapping
docker run -d --name nginx-9000 nginx
```bash
docker run -d --name nginx-no-port nginx
```
**What happened:** no, because hsot port is not mapped to comtainerport therefore no outbound traffic can be diverted to container
**docker ps output:** [Paste it - shows no ports mapped]

### Experiment 3: Foreground mode
**Command:**
```bash
docker run -p 8080:80 --name nginx-foreground nginx
```
**Observation:** yes, container opened in terminal with interactive and if'll close the terminal the the container also closes.
**How I stopped it:** Ctrl+C/exit

### Cleanup:
```bash
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
```

---

## Part 5: Post-Experiment Questions

### Q4: About `docker run -d -p 8080:80 nginx`
- **Where did nginx image come from?** docker hub respository
- **What does the 80 represent?** container port
- **What happens when I close terminal?** container keep on running because it's in detached mode

### Q5: About `docker exec -it my-nginx bash`
- **What does -it mean?** command means container's cmd will open in interactive mode in local machine terminal
- **Why could I run Linux commands?** because it's a linux image with built-in filesystem

---

## What Confused Me:
nothing

## Questions for Day 2:
concepts after these

---

## Status: ✅ Day 1 Complete
