# Week 1 Day 6: Docker Volumes & Data Persistence

**Date:** [Feb 5, 2026]
**Time spent:** [Time]
**Status:** ✅ Complete

---

## Part 1: Concepts

### Q1: Why are containers ephemeral?
[It can be created easily, and when a container crash/exits a new container can be in running states in seconds due to layer caching, when a container is deleted all of its data and file system is deleted because no physical volume is attached to it.]

### Q2: Three types of Docker data storage
**1. Volumes:** [Volumes are storage which are entirely managed by docker]
**2. Bind Mounts:** [A specific directory or file is mapped to container which stores all the data in directory, used for developments etc]
**3. tmpfs:** [All the data is stored in memory(RAM), used for crucial data like credentials etc ]

### Q3: Named Volume vs Bind Mount
**Named Volume:**
- Location: [Docker-managed]
- Use case: [Production, databases]

**Bind Mount:**
- Location: [Host path you specify]
- Use case: [Development, config files]

**Key difference:** [Control and management]

### Q4: Volume storage location
[/var/lib/docker/volumes/ on Linux]

---

## Part 2: Ephemeral Container Demonstration

[**What I did:**
docker run -d --name testing nginx #starting container
docker exec test /bin/bash -c "echo 'hello' > /usr/share/nginx/html/data.txt  #Created file inside container
docker exec test /bin/bash cat /usr/share/nginx/html/data.txt   #Verified it existed
docker stop testing #container stopped
docker rm testing   #Deleted container
docker run -d --name testing nginx #Created new container with same image

**Result:**
- File after deletion: [Gone! File not found error]
- Why: [Container filesystem is temporary]

**Key learning:** [Data inside containers is lost when container is removed]]

---

## Part 3: Named Volumes

[docker run -d --name nginx-vol \
  -v my-data:/usr/share/nginx/html \
  -p 8080:80 \
  nginx  # Container start
68c986c45ccd51cd4a6f086cadb4fca861ab53478e368ca06fb41892346ec581   #container ID
docker exec nginx-vol bash -c "echo '<h1>Persistent Data</h1>' > /usr/share/nginx/html/index.html"  #Storing data in txt file
curl http://localhost:8080  #Checking if data exists or not
<h1>Persistent Data</h1>    #Exists
docker stop nginx-vol       #Stopping Container
nginx-vol
docker rm nginx-vol         #Removing container
nginx-vol
docker run -d -p 8080:80 --name volume-use -v my-data:/usr/share/nginx/html nginx   #New container with same image
32d38ac56ae1d8bf320a7ad95361f7c229babc48f805c6e115699cae79a9997d
curl http://localhost:8080   #Checking if data persists
<h1>Persistent Data</h1>     #Exists
]

---

## Part 4: Bind Mounts

[Include your bind mount experiments]

---

## Part 5: Volume vs Bind Mount Comparison

[Volume Bind Mount
Volumes are created by docker itself and managed by the docker only uses, config files
Bind mount is a directory or file which is specified for storing data uses, devlopment]

---

## Part 6: Real-World Application

### Persistent Flask + SQLite App

[Include your database persistence test results]

---

## Part 7: Troubleshooting

[Include the 3 problem scenarios]

---

## Commands Reference
```bash
# Volume commands
docker volume create <name>
docker volume ls
docker volume inspect <name>
docker volume rm <name>
docker volume prune

# Using volumes in containers
docker run -v <volume-name>:/path <image>        # Named volume
docker run -v /host/path:/container/path <image> # Bind mount

# Check volume usage
docker ps --filter volume=<volume-name>
```

---

## Key Takeaways

1. **Containers are ephemeral by default:** Data disappears when container is removed
2. **Volumes persist data:** Independent of container lifecycle
3. **Named volumes for production:** Docker-managed, portable, safe
4. **Bind mounts for development:** Live code editing, instant changes
5. **Always use volumes for databases:** Data loss is unacceptable in production

**Most important insight:**
[Your key learning about data persistence]

---

## Confidence Assessment

**Understanding of volumes:** [1-10]
**Comfort with bind mounts:** [1-10]
**Ready for real applications:** [1-10]
**Ready for Day 7 (Final Project):** ✅ Yes

---

## Questions for Day 7
- How do I combine everything I learned?
- What's a real-world multi-container application?
- How do I deploy something production-ready?

---

✅ Day 6 Complete
**Next:** Week 1 Day 7 - Week 1 Capstone Project
