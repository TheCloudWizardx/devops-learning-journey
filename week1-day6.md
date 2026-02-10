# Week 1 Day 6: Docker Volumes & Data Persistence

**Date:** [Feb 5, 2026]
**Time spent:** [3-4 hours]
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

[ mkdir bindmount
 cd bindmount/
 ls
 vi index.html
 docker run -d --name nginx-bind -p 8082:80 -v ~/cloud/docker/bindmount:/usr/share/nginx/html nginx
1f135cddee2c74a66b15556d002284a181a4b3b35be229c09bae56859de49db4
 curl http://localhost:8082
<!DOCTYPE html>
<html>
<head>
    <title>Bind Mount Test</title>
    <style>
        body {
            font-family: Arial;
            background: #2c3e50;
            color: white;
            text-align: center;
            padding: 50px;
        }
    </style>
</head>
<body>
    <h1>🔗 Bind Mount Demo</h1>
    <p>This file is on my HOST machine!</p>
    <p>Edit it and refresh - changes appear instantly!</p>
</body>
</html>

 docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                                     NAMES
1f135cddee2c   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8082->80/tcp, [::]:8082->80/tcp   nginx-bind
 vi index.html 
 curl http://localhost:8082
<!DOCTYPE html>
<html>
<head>
    <title>Bind Mount Test</title>
    <style>
        body {
            font-family: Arial;
            background: #2c3e50;
            color: white;
            text-align: center;
            padding: 50px;
        }
    </style>
</head>
<body>
    <h1>🔗 Bind Mount Demo</h1>
    <p>This file is on my HOST machine!</p>
    <p>meowwwwwwwwwwwwww Edit it and refresh - changes appear instantly!</p>
</body>
</html>
]

---

## Part 5: Volume vs Bind Mount Comparison

[
### Volume vs Bind Mount Comparison

| Feature | Named Volume | Bind Mount |
|---------|--------------|------------|
| **Location** | Docker-managed (/var/lib/docker/volumes) | User-specified (~/path) |
| **Portability** | High (managed by Docker) | Low (host-specific path) |
| **Use case** | Production, databases | Development, config files |
| **Syntax** | `-v volume-name:/path` | `-v /host/path:/container/path` |
| **Performance** | Optimized by Docker | Native filesystem performance |
| **Backup** | Use docker volume commands | Standard file backup tools |

**When to use volumes:** [Production databases, persistent app data]
**When to use bind mounts:** [Development, sharing config files, logs]]

---

## Part 6: Real-World Application

### Persistent Flask + SQLite App

[### Database Persistence Test

**Data created:**
- Table: users
- Records: Alice, Bob

**After container deletion:**
[Data still present in new container ✅]

**Why this works:**
[Volume persists data outside container lifecycle]

**Real-world use:**
[Production databases MUST use volumes to avoid data loss]]

---

## Part 7: Troubleshooting

[### Problem 1: Permission Issues

**Error encountered:**
```bash
bash: /data/file.txt: Permission denied
```

**Why it happened:**
Volume created by root, but container running as user 1000 can't write to it

**Solutions:**
1. Run as root user (remove --user flag)
2. Pre-create volume with correct permissions:
```bash
   docker volume create test-data
   docker run --rm -v test-data:/data ubuntu chown -R 1000:1000 /data
   docker run -d --user 1000:1000 -v test-data:/data ubuntu ...
```
3. Use bind mount with host directory owned by user 1000

**Lesson:** Volume permissions matter when using non-root usersn this, user doesn't have root permission, therefore either use root and the ubuntu command won't also run due to no permission

### Problem 2: Typo in Bind Mount Path

**What happens:** Docker creates empty directory at typo path
**Issue:** Your actual data isn't where you expected
**Solution:** Always verify paths before running: `ls -la ~/path/to/verify`

### Problem 3: Multiple Writers to Same Volume

**Tested:** Two containers writing to same file simultaneously
**Result:** Both can write, but no coordination - data can interleave
**Lesson:** Multiple containers can share volumes, but application needs to handle concurrent writes (locks, queues, etc.)]

[  ### Persistent Database Application

**Visit count before deletion:** [e.g., Visit #3]

**Visit count after recreation:** [e.g., Visit #4 - continued!]

**Why this matters:**
In production, databases MUST persist data. Without volumes:
- User data would be lost on every deployment
- Database would reset on container restart
- All application state would disappear

**Real-world applications:**
- User databases
- Application logs
- Uploaded files
- Configuration data]

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
[
Containers are designed to be disposable - the application inside can be replaced/upgraded at any time. But DATA must persist. This is why volumes exist outside the container lifecycle.

**The critical distinction:**
- **Container = disposable** (upgrade, replace, delete freely)
- **Volume = permanent** (survives container deletion)

This separation is the foundation of modern cloud architecture - stateless applications with stateful data storage.

**Real-world impact:**
Without volumes, every deployment would wipe user data, every crash would lose transactions, every update would reset databases. Volumes make production systems viable.]

---

## Confidence Assessment

**Understanding of volumes:** [7]
**Comfort with bind mounts:** [7]
**Ready for real applications:** [7]
**Ready for Day 7 (Final Project):** ✅ Yes

---

## Questions for Day 7
- How do I combine everything I learned?
- What's a real-world multi-container application?
- How do I deploy something production-ready?

---

✅ Day 6 Complete
**Next:** Week 1 Day 7 - Week 1 Capstone Project
