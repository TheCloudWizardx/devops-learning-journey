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

[
### Problem 1: Volume Permission Issues

**Setup:**
```bash
docker volume create test-data
docker run -d --name perm-test \
  -v test-data:/data \
  --user 1000:1000 \
  ubuntu bash -c "echo 'test' > /data/file.txt && sleep infinity"
```

**Error encountered:**
```
bash: /data/file.txt: Permission denied
```

**Why it happened:**
When Docker creates a volume, it's owned by root (user 0). When you run container as user 1000, that user can't write to root-owned directory.

**Think of it like:**
- Volume = folder created by administrator
- Container user = regular user account
- Regular user can't write to admin's folder = permission denied

**Solutions:**

**Option 1: Run as root (easiest but less secure)**
```bash
docker run -d --name perm-test \
  -v test-data:/data \
  ubuntu bash -c "echo 'test' > /data/file.txt && sleep infinity"
# Works because root user has all permissions
```

**Option 2: Fix permissions before using (production approach)**
```bash
# Create volume
docker volume create test-data

# Set correct ownership
docker run --rm -v test-data:/data ubuntu chown -R 1000:1000 /data

# Now user 1000 can write
docker run -d --name perm-test \
  -v test-data:/data \
  --user 1000:1000 \
  ubuntu bash -c "echo 'test' > /data/file.txt && sleep infinity"
```

**Option 3: Use bind mount with correct host permissions**
```bash
mkdir ~/my-data
# Your user owns this directory
docker run -d --name perm-test \
  -v ~/my-data:/data \
  --user 1000:1000 \
  ubuntu bash -c "echo 'test' > /data/file.txt && sleep infinity"
# Works because you own ~/my-data
```

**When this matters in production:**
- Running containers as non-root is a security best practice
- But volumes need correct permissions
- Always test with actual user IDs your app will use

**Lesson learned:** Volume permissions must match container user, or you'll get "Permission denied" errors.

---

### Problem 2: Bind Mount Path Typos

**Setup:**
```bash
# Try to mount a directory with typo
docker run -d --name bind-test \
  -v ~/my-websit:/data \
  nginx
# Note the typo: "websit" instead of "website"
```

**What Docker does:**
```bash
docker inspect bind-test --format='{{.Mounts}}'
# Shows: Source: /home/billu/my-websit
```

**The problem:**
- Docker created `/home/billu/my-websit` (empty directory)
- Your actual files are in `/home/billu/my-website`
- Container has empty directory, your files aren't there

**Why this is dangerous:**
You think your data is mounted, but it's not. Application might:
- Start with empty database
- Overwrite your configs with defaults
- Create new files in wrong location

**How to catch this:**
```bash
# Always verify before running
ls -la ~/my-websit
# If it doesn't exist or is empty, you have the wrong path!

# Check inside container after starting
docker exec bind-test ls -la /data
# If empty when it shouldn't be, check your mount path
```

**Solution:**
```bash
# Use tab completion when typing paths
docker run -d -v ~/my-web<TAB> 
# Shell completes to ~/my-website/ if it exists

# Or use absolute paths to be explicit
docker run -d -v /home/billu/my-website:/data nginx
```

**Real-world story:**
Developer mounts config file with typo. Docker creates empty file. Application uses defaults instead of production config. Production database gets wiped because wrong credentials. **Always verify paths!**

**Lesson learned:** One typo in bind mount path = wrong data or no data. Always verify paths exist before mounting.

---

### Problem 3: Multiple Containers Writing to Same Volume

**Setup:**
```bash
# Create shared volume
docker volume create shared-vol

# Container 1: writes "Writer1" every second
docker run -d --name writer1 \
  -v shared-vol:/data \
  ubuntu bash -c "while true; do echo 'Writer1' >> /data/log.txt; sleep 1; done"

# Container 2: writes "Writer2" every second
docker run -d --name writer2 \
  -v shared-vol:/data \
  ubuntu bash -c "while true; do echo 'Writer2' >> /data/log.txt; sleep 1; done"

# Wait 10 seconds
sleep 10

# Read the file
docker exec writer1 cat /data/log.txt
```

**Result you see:**
```
Writer1
Writer2
Writer1
Writer2
Writer1
Writer2
...
```

**What's happening:**
Both containers writing to same file simultaneously. The writes happen to alternate (not guaranteed, just what often happens).

**Why this works (but is risky):**

**File system allows multiple writers:**
- Both containers can open the file
- Both can append to it
- Filesystem handles the low-level coordination

**But there's NO application-level coordination:**
- If both write at EXACT same time, data could corrupt
- No guarantee of write order
- No locking mechanism
- One container doesn't know what other is doing

**Think of it like:**
Two people writing in same notebook simultaneously:
- Sometimes they alternate nicely
- Sometimes they write over each other's text
- Sometimes pages get torn
- No coordination = chaos potential

**When this is OK:**
- Read-only sharing (multiple containers reading same config)
- Logging where order doesn't matter critically
- Temporary dev/test scenarios

**When this is NOT OK:**
- Database files (NEVER share database volume between multiple database instances)
- Files requiring exact write order
- Financial transactions
- Any data where corruption = disaster

**Proper solutions for shared data:**

**Option 1: Database with proper locking**
```bash
# One MySQL container with volume
# Multiple app containers connect via network (not volume)
docker run -d --name mysql -v db-data:/var/lib/mysql mysql
docker run -d --name app1 --link mysql webapp
docker run -d --name app2 --link mysql webapp
# Apps access MySQL over network, MySQL handles coordination
```

**Option 2: Message queue**
```bash
# Containers write to queue, one consumer processes
docker run -d --name redis -v redis-data:/data redis
docker run -d --name writer1 --link redis writer-app
docker run -d --name writer2 --link redis writer-app
docker run -d --name processor --link redis processor-app
```

**Option 3: Read-only mounts**
```bash
# Multiple containers read config, none can write
docker run -d -v config:/etc/config:ro app1
docker run -d -v config:/etc/config:ro app2
# :ro = read-only, safe for sharing
```

**Lesson learned:** 
- Multiple containers CAN share a volume
- But your APPLICATION must handle coordination
- For databases: ONE container per volume, others connect via network
- For configs: Use read-only mounts (`:ro`)
- For logs: Consider centralized logging instead

**Real-world example:**
Company runs 3 containers all writing to same log volume. Works fine in testing. In production under load, log file gets corrupted. Debugging nightmare. **Solution:** Use proper logging service (like ELK stack) or read-only shares for configs.]

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
