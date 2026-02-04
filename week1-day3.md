# Week 1 Day 3: Writing My First Dockerfile

**Date:** [Date]
**Time spent:** [Time]
**Status:** ✅ Complete

---

## Part 1: Concepts

### Q1: What is a Dockerfile?
Docker file is a set of instructions which builds the docker image.
Simple analogy dockerfile are the ingredients, images are the receipe and container are the baked cake.


### Q2: Dockerfile Instructions
- **FROM:** [takes the base image]
- **COPY:** [copy the files from source to destination]
- **RUN:** [run the commands on your image for installation or setup ]
- **CMD:** [run the commands when the container is started]
- **EXPOSE:** [which port does container listens to]
- **WORKDIR:** [ setting up the working directory]

### Q3: Build Context
[docker build -t my_image .
this is the command used for building the images from dockerfile, my_image is name of image that is built from the dockerfile, "build" means set of instructions are building docker image
]

---

## Part 2: My First Custom Image

### Project: Simple HTML Website

**My Dockerfile:**
```dockerfile
[FROM nginx:alpine

COPY index.html /usr/share/nginx/html

EXPOSE 80]
```

**Understanding each line:**
- FROM nginx:alpine → [pulls the image from dockerhub registory after finding it locally]
- COPY index.html ... → [copies index.html file from source to destination]
- EXPOSE 80 → [sets the metadata, and documentation that app listens to this port]

### Build Process

**Build command used:**
```bash
docker build -t my-website:v1 .
```

**Build output:**
```
[[+] Building 0.1s (7/7) FINISHED                                                                                                                         docker:default
 => [internal] load build definition from dockerfile                                                                                                               0.0s
 => => transferring dockerfile: 105B                                                                                                                               0.0s
 => [internal] load metadata for docker.io/library/nginx:alpine                                                                                                    0.0s
 => [internal] load .dockerignore                                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                                    0.0s
 => [1/2] FROM docker.io/library/nginx:alpine                                                                                                                      0.1s
 => [internal] load build context                                                                                                                                  0.0s
 => => transferring context: 610B                                                                                                                                  0.0s
 => [2/2] COPY index.html /usr/share/nginx/html                                                                                                                    0.0s
 => exporting to image                                                                                                                                             0.0s
 => => exporting layers                                                                                                                                            0.0s
 => => writing image sha256:3891fa7521c5202dd215aa9b8a44b75934f2eccb62eb81046a46f14843377f81                                                                       0.0s
 => => naming to docker.io/library/website ]
```

**Number of steps:** [7]
**Build time:** [15 seconds]
**Final image size:** [61.9MB]

### Running the Container

**Command:**
```bash
docker run -d -p 8080:80 --name nginx website
```

**Result:** ✅ Success 

**Browser screenshot/description:** [<img width="1322" height="617" alt="image" src="https://github.com/user-attachments/assets/61921d8b-de40-4e05-9ae6-ef17c6218697" />
]

---

## Part 3: Rebuilding with Changes

**Changes I made to HTML:**
[Changed the name]

**Build v2 output:**
```
[ => CACHED [1/2] FROM docker.io/library/nginx:alpine                                                                                                               0.0s
 => [2/2] COPY index.html /usr/share/nginx/html                                                                                                                    0.0s
 => exporting to image ]
```

**Cache behavior observed:**
[Pulling the docker file is cached because it was already pulled from docker hub and docker uses layer catching, copying file disn't used catching because file has been modified]

---

## Part 4: Experiments

### Experiment 1: No EXPOSE instruction
**Result:** [Worked]
**Conclusion:** [The website still works because Expose is optional and it's used for meta data and documentation purpose when you use -p it maps the port to the host for traffic
]

### Experiment 2: Missing build context
**Error:** [ERROR: docker: 'docker buildx build' requires 1 argument
Usage:  docker buildx build [OPTIONS] PATH | URL | -
Run 'docker buildx build --help' for more information]
**Learning:** [Docker buildx needs one arguement means docker needs a place or directory/lcoation to build image if your file is in different location share the exact path of docker file
]

### Experiment 3: Non-existent file
**Error:** [ => ERROR [2/2] COPY indesdx.html /usr/share/nginx/html                                                                                                            0.0s
------
 > [2/2] COPY indesdx.html /usr/share/nginx/html:
------
dockerfile:3
]
**Learning:** [If the file you're trying to copy to destination is not availale docker build commands exits and throws and error.]

---

## Commands Reference
```bash
docker build -t <name>:<tag> .        # Build image from Dockerfile
docker build -f <file> -t <name> .    # Build from specific Dockerfile
docker images                          # List built images
docker run -d -p <host>:<container> <image>  # Run built image
```

---
1. **Docker build process:** Dockerfiles create images layer by layer
2. **EXPOSE is documentation:** Real port mapping happens with -p flag
3. **Layer caching:** Unchanged layers are reused, speeding up rebuilds

**Most important insight:** 
Understanding that EXPOSE doesn't actually open ports - it's just documentation. The -p flag in docker run is what actually maps ports. This distinction is crucial!

**Practical application:**
When building images in real projects, order Dockerfile commands carefully - put things that change rarely (base image, dependencies) at the top, and things that change often (application code) at the bottom to maximize cache hits.

---

## Key Takeaways

1. [Docker run]
2. [How does Expose work]
3. [Docker file]

**Most important:** [Your biggest insight]

---

## Confusion Points
[Nothing]

## Tomorrow's Preview
**Day 4:** Building a Python Flask app container

---

✅ Day 3 Complete 
