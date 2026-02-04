# Week 1 Day 4: Python Flask App Container

**Date:** [Feb,4 2026]
**Time spent:** [Time]
**Status:** ✅ Complete

---

## Part 1: Concepts

### Q1: What is requirements.txt?
[Requirements.txt is a text file which contains all the necessary app dependencies and libraries]

### Q2: Why copy requirements.txt before app code?
[Because requirements.txt contains the app libraries and will be installed after package installation]

### Q3: What does WORKDIR do?
[WORKDIR sets the working directory of the app, where all the files will be copied from local machine to contianer]

### Q4: RUN vs CMD
**RUN:** [install the packages and requirements at the time of building the container]
**CMD:** [the apps/services which will be running when the container is running]
**Key difference:** [run before building, runs after building]

---

## Part 2: Building the Flask App

### Project Structure
```
flask-docker-app/
├── app.py
├── requirements.txt
└── Dockerfile
```

### My Dockerfile
```dockerfile
[FROM python:3.11-slim
WORKDIR /app
COPY . /app
RUN pip install update && pip install --no-cache-dir -r requirements.txt
CMD ["python", "app.py" ]
```

**Why I structured it this way:**
[FROM selects the base OS
WORKDIR creates the default directory for copying files and running CMD commands
COPY copies everything from source to destination
RUN after setting up the directory and copying requirements file then only we can proceed for installation
CMD means the apps and command that will execute when the container starts.

The file has been structured this way for layer caching purpose becuase one will not change his working dir/base img/requirements only the code changes frequently for time efficiency docker file has been structured]

---

### Build Process

**Build command:**
```bash
docker build -t flash:v1 .
```

**Build output:**
```
[docker build -t flash:v1 .
[+] Building 22.4s (9/9) FINISHED                                                                                                                        docker:default
 => [internal] load build definition from dockerfile                                                                                                               0.0s
 => => transferring dockerfile: 220B                                                                                                                               0.0s
 => [internal] load metadata for docker.io/library/python:3.11-slim                                                                                                8.1s
 => [internal] load .dockerignore                                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                                    0.0s
 => [1/4] FROM docker.io/library/python:3.11-slim@sha256:d0d43a8b0c352c215cd1381f3f4d7ac34cf3440cd0415873451d7affca53a769                                          0.0s
 => [internal] load build context                                                                                                                                  0.0s
 => => transferring context: 2.65kB                                                                                                                                0.0s
 => CACHED [2/4] WORKDIR /app                                                                                                                                      0.0s
 => [3/4] COPY . /app                                                                                                                                              0.0s
 => [4/4] RUN pip install --upgrade pip &&  pip install --no-cache-dir -r requirements.txt                                                                        13.9s
 => exporting to image                                                                                                                                             0.3s 
 => => exporting layers                                                                                                                                            0.3s 
 => => writing image sha256:74f74b5cf717a58e43e07cce8f51ba2d5d30a89fe8509dddaadffb04a50194c4                                                                       0.0s 
 => => naming to docker.io/library/flash:v1                                                                                                                        0.0s 
]
```

**Build stats:**
- Number of steps: [15]
- Build time: [30sec]
- Final image size: [147MB]

---

### Running the Application

**Run command:**
```bash
docker run -d -p 5000:5000 --name flask-app flash:v1
```

**Testing endpoints:**

**Homepage (/):**
[<img width="1322" height="617" alt="image" src="https://github.com/user-attachments/assets/bd65976d-8187-4b83-8216-d4af3bfae1c9" />
]

**/health endpoint:**
```json
[{
  "message": "Application is running successfully",
  "service": "flask-docker-app",
  "status": "healthy"
}]
```

**/time endpoint:**
```json
[{
  "current_time": "2026-02-04 12:06:56",
  "timezone": "UTC"
}]
```

**/about endpoint:**
```json
[{
  "app": "Flask Docker Learning Project",
  "author": "[DEV GUPTA]",
  "description": "Learning to containerize Python applications",
  "version": "1.0"
}]
```

**All endpoints working:** ✅ Yes 

---

## Part 3: Layer Caching Experiments

### Experiment 1: Modify App Code

**What I changed:**
[Changed the name to Dev Gupta]`

**Cached layers:**
- [ CACHED [2/4] WORKDIR /app  ]

**Rebuilt layers:**
- [[+] Building 9.5s (9/9) FINISHED                                                                                                                         docker:default
 => [internal] load build definition from dockerfile                                                                                                               0.0s
 => => transferring dockerfile: 213B                                                                                                                               0.0s
 => [internal] load metadata for docker.io/library/python:3.11-slim                                                                                                2.9s
 => [internal] load .dockerignore                                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                                    0.0s
 => [1/4] FROM docker.io/library/python:3.11-slim@sha256:d0d43a8b0c352c215cd1381f3f4d7ac34cf3440cd0415873451d7affca53a769                                          0.0s
 => [internal] load build context                                                                                                                                  0.0s
 => => transferring context: 2.46kB                                                                                                                                0.0s
 => [3/4] COPY . /app                                                                                                                                              0.0s
 => [4/4] RUN pip install --upgrade pip &&  pip install --no-cache-dir -r requirements.txt                                                                         6.3s
 => exporting to image                                                                                                                                             0.1s 
 => => exporting layers                                                                                                                                            0.1s 
 => => writing image sha256:18e7ee1813611b63e8ea3c139066244964eef10dbad62386ac35b0e92313341b                                                                       0.0s 
 => => naming to docker.io/library/flask:v1   ]

**Why:** [Because the dependecies didn't changed only the code was updated]

---

### Experiment 2: Add New Dependency

**Added to requirements.txt:** requests==2.31.0

**Rebuild output:**
```
[[+] Building 20.0s (9/9) FINISHED                                                                                                                        docker:default
 => [internal] load build definition from dockerfile                                                                                                               0.0s
 => => transferring dockerfile: 213B                                                                                                                               0.0s
 => [internal] load metadata for docker.io/library/python:3.11-slim                                                                                                8.1s
 => [internal] load .dockerignore                                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                                    0.0s
 => [1/4] FROM docker.io/library/python:3.11-slim@sha256:d0d43a8b0c352c215cd1381f3f4d7ac34cf3440cd0415873451d7affca53a769                                          0.0s
 => [internal] load build context                                                                                                                                  0.0s
 => => transferring context: 149B                                                                                                                                  0.0s
 => CACHED [2/4] WORKDIR /app                                                                                                                                      0.0s
 => [3/4] COPY . /app                                                                                                                                              0.0s
 => [4/4] RUN pip install --upgrade pip &&  pip install --no-cache-dir -r requirements.txt                                                                        11.7s
 => exporting to image                                                                                                                                             0.1s 
 => => exporting layers                                                                                                                                            0.1s 
 => => writing image sha256:f760bca3085dfa5a4e65cc2733a927d361d3b0d59ff2963a536665ae4f8d8c5f                                                                       0.0s 
 => => naming to docker.io/library/flask:v2                                                                                                                        0.0s]
```

**What rebuilt:**
[Pip install layers, copying files layer]

**Key learning:**
[Why adding a dependency causes more layers to rebuild - explain the importance of Dockerfile orde
Because when dependecy is added all the necessary file is being added and dowmloaded again, a new dependency a new library, a new set of instructions]

---

## Part 4: Troubleshooting

### Challenge 1: Missing Flask

**Error encountered:**
```
[Traceback (most recent call last):
  File "/app/app.py", line 1, in <module>
    from flask import Flask, jsonify
ModuleNotFoundError: No module named 'flask'
]
```

**Diagnosis process:**
1. [Requirements.txt file for the available dependencies]
2. [If flask is not present there, i'll add flask along with its version, then run docker build, followed by docker run and then check if contianer is running or not]
3. [Docker logs revealed that the container is not running and exited]

**Root cause:** [Flask was not present in the requirements.txt & flask is being used in app]

**Solution:** [Just check requirements.txt file and insert flask dependency along with its version and then re run the docker build and run commanad]

---

### Challenge 2: Wrong Port Mapping

**Problem:** [The website is refused to connect, not accessible]

**Why it failed:** [Because flask listens to port 5000 by default]

**Correct command:**
```bash
[docker run -d -p 5000:5000 flask-app flask:v1]
```

---

### Challenge 3: No WORKDIR

**Files copied to:** [root]

**Did it work?** [No]

**Why WORKDIR is important:**
[Docker needs one directory to copy the app dependecies and source code to container]

---

## Commands Reference
```bash
# Build Python app image
docker build -t flask-app:v1 .

# Run Flask container
docker run -d -p 5000:5000 --name my-flask-app flask-app:v1

# View application logs
docker logs my-flask-app

# Execute command in running container
docker exec my-flask-app pwd

# Test endpoints
curl http://localhost:5000/health
```

---

## Key Takeaways

1. **Layer ordering matters:** Copy requirements before code to maximize cache hits
2. **Dependency management:** requirements.txt defines Python packages needed
3. **WORKDIR best practice:** Sets working directory for subsequent commands
4. **RUN vs CMD:** RUN executes during build, CMD runs when container starts

**Most important insight:**
[Your biggest learning - about layer caching and Dockerfile optimization]

**Why this matters in production:**
Proper Dockerfile ordering means faster rebuilds during development and deployment. If you put frequently-changing code before rarely-changing dependencies, you invalidate cache and rebuild everything.

---

## Confusion Points
[]

## Questions for Day 5
- How do I debug when containers fail?
- What are Docker volumes for?
- How do I handle environment variables?

---

## 📈 Self-Assessment

**Understanding of Dockerfiles:** [1-10]
**Confidence building Python apps:** [1-10]
**Ready for Day 5:** ✅ Yes / ⏳ Need more practice

---

✅ Day 4 Complete
**Next:** Week 1 Day 5 - Container Debugging & Logs
