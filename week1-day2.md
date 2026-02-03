# Week 1 Day 2: First Container Experience

**Date:** February 3, 2025
**Time spent:** 1 hour

---

## Part 1: Concepts I Learned

### Q1: What are Docker Image Layers?
Docker images are a set of instructions used by stacking up layers of file system, packages & app dependencies. They are immuatable means if you've build an image you cannot change it. Docker images are in readable format only.
If we talk layer by layer:
first layer set up the package management, followed by installing app and its dependencies, then third setting up working directory for application, then copying requirements from local machine to image then setting up the starting commands and settign up the base with network settings.


### Q2: Why does layering matter for efficiency?
Layering is very much important for efficiency because docker uses the already created/downloaded layers to build the images and run the contianer, as it is less time-taking because it uses already built-in layers and it saves time.

### Q3: What's the difference between nginx:latest and nginx:1.25?
The differnce between both when an image in pulled using nginx:latest it means the latest image in docker hub repository of nginx will be pulled while nginx:1.25 pull the specific version of the image mentioned. "Latest" and "1.25" are called image tags.
Tags are basically the versions of the image. While production mostly specific versions are tested for maximum availability across different versions of apps

---
TASK1:
# Pull different versions
docker pull nginx:latest
docker pull nginx:1.25
docker pull python:3.11-slim
docker pull python:3.11

# List all images
docker images
Output:
                                                                                                                                                   i Info →   U  In Use
IMAGE                     ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest        1b44b5a3e06a       10.1kB             0B    U   
image:latest              15aae674347b        161MB             0B        
jenkins/jenkins:latest    484aa0775dae        492MB             0B    U   
mongo-express:latest      870141b735e7        182MB             0B        
mongo:latest              ac3eefab8be5        916MB             0B    U   
nginx:1.25                e784f4560448        188MB             0B        
nginx:latest              248d2326f351        161MB             0B        
node-base:latest          23b768fba511        238MB             0B        
node:18-alpine            ee77c6cd7c18        127MB             0B        
node:current-alpine3.23   5382fa85eebe        163MB             0B        
python:3.11               fe720120cb54        1.1GB             0B        
python:3.11-slim          4b9bdfe9d486        124MB             0B        
ubuntu:latest             493218ed0f40       78.1MB             0B    U   

# Compare sizes - document what you see
```

The python image 3.11 consist all the libraires and dependencies of python and installing them on debian, while slim uses environment variabel to store key values of python verison already.
```
Image sizes I found:
- nginx:latest = 161MB
- nginx:1.25 = 188MB
- python:3.11-slim = 124MB
- python:3.11 = 1.1GB

Size difference between python:3.11 and python:3.11-slim: 1002.4MB

TASK2:

docker history nginx:latest
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
248d2326f351   11 hours ago   CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      11 hours ago   STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      11 hours ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      11 hours ago   COPY 30-tune-worker-processes.sh /docker-ent…   4.62kB    buildkit.dockerfile.v0
<missing>      11 hours ago   COPY 20-envsubst-on-templates.sh /docker-ent…   3.02kB    buildkit.dockerfile.v0
<missing>      11 hours ago   COPY 15-local-resolvers.envsh /docker-entryp…   389B      buildkit.dockerfile.v0
<missing>      11 hours ago   COPY 10-listen-on-ipv6-by-default.sh /docker…   2.12kB    buildkit.dockerfile.v0
<missing>      11 hours ago   COPY docker-entrypoint.sh / # buildkit          1.62kB    buildkit.dockerfile.v0
<missing>      11 hours ago   RUN /bin/sh -c set -x     && groupadd --syst…   82MB      buildkit.dockerfile.v0
<missing>      11 hours ago   ENV DYNPKG_RELEASE=1~trixie                     0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV PKG_RELEASE=1~trixie                        0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV ACME_VERSION=0.3.1                          0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV NJS_RELEASE=1~trixie                        0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV NJS_VERSION=0.9.4                           0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV NGINX_VERSION=1.29.4                        0B        buildkit.dockerfile.v0
<missing>      11 hours ago   LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      37 hours ago   # debian.sh --arch 'amd64' out/ 'trixie' '@1…   78.6MB    debuerreotype 0.17

# Detailed inspection
docker inspect nginx:latest
Ngnix image contains only 7 layer and it's stored in /var/lib/docker

# Compare Ubuntu layers
docker pull ubuntu:22.04
docker history ubuntu:22.04
```

Ubuntu consist of only 6 layer architecture made for AMD64 on linux os while Nginx consist of 18 layers with same os and architecture

**nginx:latest layers:**
```
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
248d2326f351   11 hours ago   CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      11 hours ago   STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      11 hours ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      11 hours ago   COPY 30-tune-worker-processes.sh /docker-ent…   4.62kB    buildkit.dockerfile.v0
<missing>      11 hours ago   COPY 20-envsubst-on-templates.sh /docker-ent…   3.02kB    buildkit.dockerfile.v0
<missing>      11 hours ago   COPY 15-local-resolvers.envsh /docker-entryp…   389B      buildkit.dockerfile.v0
<missing>      11 hours ago   COPY 10-listen-on-ipv6-by-default.sh /docker…   2.12kB    buildkit.dockerfile.v0
<missing>      11 hours ago   COPY docker-entrypoint.sh / # buildkit          1.62kB    buildkit.dockerfile.v0
<missing>      11 hours ago   RUN /bin/sh -c set -x     && groupadd --syst…   82MB      buildkit.dockerfile.v0
<missing>      11 hours ago   ENV DYNPKG_RELEASE=1~trixie                     0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV PKG_RELEASE=1~trixie                        0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV ACME_VERSION=0.3.1                          0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV NJS_RELEASE=1~trixie                        0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV NJS_VERSION=0.9.4                           0B        buildkit.dockerfile.v0
<missing>      11 hours ago   ENV NGINX_VERSION=1.29.4                        0B        buildkit.dockerfile.v0
<missing>      11 hours ago   LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      38 hours ago   # debian.sh --arch 'amd64' out/ 'trixie' '@1…   78.6MB    debuerreotype 0.17

```

**Number of layers:** 18

**Largest layer:** Python 3.11 with 1.1GB

**ubuntu:22.04 layers:**
```
IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
65c77cbc27c2   3 weeks ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      3 weeks ago   /bin/sh -c #(nop) ADD file:b499000226bd9a7c5…   77.9MB    
<missing>      3 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      3 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      3 weeks ago   /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B        
<missing>      3 weeks ago   /bin/sh -c #(nop)  ARG RELEASE                  0B   


TASK3:

# See all images including intermediate ones
docker images -a

# Get detailed format
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# Remove specific image
docker rmi nginx:1.25

# Remove all unused images
docker image prune -a
```

## 🎯 Key Takeaways

**Today I learned:**
1. Docker images use layered architecture for efficiency through caching
2. Slim variants can save 80-90% space compared to full images
3. Many Dockerfile instructions (ENV, LABEL, CMD) don't add to image size

**Most surprising:** 
The massive size difference between python:3.11 (1.1GB) and python:3.11-slim (124MB)

**Why this matters:**
Smaller images = faster deployments, less storage, quicker container startup

---

## ❓ Questions for Day 3

- How do I create my own custom images?
- What's the difference between CMD and ENTRYPOINT?
- How do I optimize Dockerfile for smaller images?

---

## 📈 Commands Mastered
```bash
docker pull <image>:<tag>     # Pull specific versions
docker images                 # List all images  
docker images -a              # Include intermediates
docker history <image>        # View layer history
docker inspect <image>        # Detailed metadata
docker rmi <image>            # Remove image
docker image prune -a         # Clean up unused images
```

---

**Status:** ✅ Day 2 Complete
**Next:** Week 1 Day 3 - Writing Dockerfiles
After cleanup (docker image prune):
Space reclaimed: 3.427  
---
