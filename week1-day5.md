# Week 1 Day 5: Container Debugging & Logs

**Date:** [Feb, 4 2026]
**Status:** ✅ Complete

---

## Part 1: Concepts

### Q1: Container States
[Create - container is created but not running
Running - container is running
Exited - container exited because of some during container run command
Paused - container is paused or frozen
Restart - container will restart
Dead - Contianer needs to created again because of some fatal error which cannot be resolved]

### Q2: docker logs vs docker exec
**docker logs:** [When container exited before creation]
**docker exec:** [When contianer is running you need to check/access directories inside container]
**Key difference:** [Run when container exited before creation due to error, used when you need to enter container filesystem for troubleshooting]

### Q3: docker inspect
[It shows so much of information in JSON format like PID, created time, container id, state, image used, driver, OS, architecture, port bindings, volume & network details]

### Q4: Checking Container Health
[
**Method 1: docker ps**
- Check STATUS column (Up X minutes = running)

**Method 2: docker logs**
- Check for error messages
- No errors = likely healthy

**Method 3: docker stats**
- CPU and memory within normal range
- Not hitting limits

**Method 4: Application endpoint test**
```bash
curl http://localhost:PORT
# or
docker exec container curl http://localhost:80
```

**Method 5: Check processes**
```bash
docker top container_name
# Main process still running = healthy
```

**Method 6: Built-in health check (if defined)**
```bash
docker inspect --format='{{.State.Health.Status}}' container
# Only works if HEALTHCHECK defined in Dockerfile
# Most containers DON'T have this
```

**Note:** Health checks are optional in Docker. If not defined in Dockerfile, you'll get "map has no entry for key Health" error - this is normal!]

---

## Part 2: Debugging Tools Explored

### Tool: docker ps
**Most useful flags:**
- `docker ps -a` → [Shows all containers]
- `docker ps -s` → [Shows sizes]

**Sample output:**
```
[docker ps -a
CONTAINER ID   IMAGE            COMMAND           CREATED          STATUS          PORTS                                         NAMES
521168bebeb0   working:latest   "python app.py"   43 minutes ago   Up 43 minutes   0.0.0.0:5000->5000/tcp, [::]:5000->5000/tcp   priceless_chatelet
docker ps -s
CONTAINER ID   IMAGE            COMMAND           CREATED          STATUS          PORTS                                         NAMES                SIZE
521168bebeb0   working:latest   "python app.py"   43 minutes ago   Up 43 minutes   0.0.0.0:5000->5000/tcp, [::]:5000->5000/tcp   priceless_chatelet   166kB (virtual 147MB)
]
```

---

### Tool: docker logs
**Commands practiced:**
```bash
docker logs -f <container>    # Follow logs
docker logs --tail 20 <container>
docker logs -t <container>    # With timestamps
```

**Use case:** [ -f: checks ongoing logs, --tail 20: last 20 logs, -t: checks logs with time stamp to check when disaster has occured]

---

### Tool: docker exec
**Useful commands inside container:**
```bash
docker exec -it <container> bash
ps aux          # See running processes
ls /app         # Check files
env             # Check environment variables
```

**What I discovered:** [ps aux: listed all the processess running in container
ls /app: list all the files and directories present in /app
env: list all the env variables]

---

### Tool: docker inspect
**Container IP address:** [many ways, use inspect command to check in the network coloumn]
**Environment variables:** [use docker inspect to find env]
**Mount points:** [How to check]

**Most useful format strings:**
```bash
--format='{{.State.Status}}'
--format='{{.NetworkSettings.IPAddress}}'
```

---

### Tool: docker stats
**Metrics monitored:**
- CPU %
- Memory usage/limit
- Network I/O
- Block I/O

**When to use:** [when containers are working slow and sloppy we can check the memory they are taking so we can stop and restart container or clear cache and temp files used]

---

## Part 3: Debugging Scenarios

### Scenario 1: Container Won't Start (Syntax Error)

**Problem encountered:**
```
[  File "/app/app.py", line 6
    def home()  # SYNTAX ERROR: missing colon
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
SyntaxError: expected ':']
```

**Debugging steps:**
1. [Checked docker ps -a - status: Exited]
2. [Ran docker logs - found Python syntax error]
3. [Identified missing colon in function definition]
4. [Fixed app.py]
5. [Rebuilt and ran successfully]

**Time to debug:** [10-15 minutes]

**Key lesson:** [Always check logs first when container exits]

---

### Scenario 2: Container Running But Not Accessible

**Problem:** Can't access nginx in browser

**Debugging process:**
1. Container status: [Running ✅]  
2. Port mappings: [None - this was the problem!]
3. Container IP: [172.17.0.2]
4. Curl to container IP: [Worked!]
5. Solution: [Recreate with -p flag]

**Lesson learned:** [Port mapping vs internal networking]

---

### Scenario 3: Container Exits Immediately

**Exit code:** 0

**Why it exited:** [Command completed, no foreground process]

**Solution:** [Use long-running command like sleep infinity]

**Understanding exit codes:**
- 0 = Success
- 1 = Error
- 137 = Killed (OOM)

---

### Scenario 4: High Memory Usage

**Memory limit set:** 100MB

**What happened:** [Container killed after hitting limit]

**Exit code:** 137

**Diagnosis commands:**
```bash
docker stats memory-test
docker inspect --format='{{.State.ExitCode}}' memory-test
```

**Lesson:** [How to identify and debug OOM issues]

---

### Scenario 5: Networking Between Containers

**Container IPs:**
- app1: [172.17.0.4]
- app2: [172.17.0.5]

**Could they communicate?** Yes

**Network:** [bridge]

**Commands used:**
```bash
docker inspect --format='{{.NetworkSettings.IPAddress}}' app1
docker exec app1 curl http://[app2-ip]:80
```

---

## Part 4: My Debugging Checklist

### When a container won't start:
1. ✅ Check `docker ps -a` for status and exit code
2. ✅ Run `docker logs <container>` to see error messages
3. ✅ Check if image built successfully
4. ✅ Verify Dockerfile syntax
5. ✅ Check for missing dependencies

### When a container is running but not accessible:
1. ✅ Verify container is running: `docker ps`
2. ✅ Check port mappings: `docker ps` (PORTS column)
3. ✅ Get container IP: `docker inspect --format='{{.NetworkSettings.IPAddress}}'`
4. ✅ Test with curl to container IP directly
5. ✅ Recreate with proper -p flag if needed

### When a container exits immediately:
1. ✅ Check exit code: `docker inspect --format='{{.State.ExitCode}}'`
2. ✅ Check logs: `docker logs <container>`
3. ✅ Exit code 0 = command completed (need foreground process)
4. ✅ Exit code 137 = killed (OOM)
5. ✅ Use long-running command like `sleep infinity`

### When debugging performance:
1. ✅ Check resource usage: `docker stats`
2. ✅ Check running processes: `docker top`
3. ✅ Get inside container: `docker exec -it bash`
4. ✅ Check for memory limit issues (exit code 137)

### When debugging networking:
1. ✅ Get container IP: `docker inspect`
2. ✅ Test connectivity: `docker exec <container> curl <target>`
3. ✅ Verify containers are on same network
4. ✅ Check if ports are properly mapped with -p```

---

## Key Takeaways

1. **Logs are your first debugging tool:** Always check `docker logs` when something fails
2. **Exit codes tell a story:** 0=success, 1=error, 137=killed
3. **docker inspect reveals everything:** Use format strings to extract specific data
4. **Port mapping vs internal networking:** Understand the difference
5. **Resource limits matter:** Monitor with docker stats, check for OOM kills

**Most important insight:**
[Your biggest learning about debugging]

**Why this matters in production:**
90% of DevOps work is troubleshooting. These skills are MORE important than building containers.

---

## Confidence Assessment

**Container debugging skills:** [7]
**Comfort with docker logs:** [7]
**Understanding of networking:** [7]
**Ready for Day 6:** ✅ Yes

---

## Questions for Day 6
- How do I persist data across container restarts?
- What are Docker volumes?
- How do I share data between containers?

---

✅ Day 5 Complete
**Next:** Week 1 Day 6 - Docker Volumes & Data Persistence
