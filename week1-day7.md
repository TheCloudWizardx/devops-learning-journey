# Week 1 Day 7: Capstone Project - Task Manager Application

**Date:** [11th Feb 2026]
**Time spent:** [2-3 days]
**Status:** ✅ Complete

---

## Project Summary

**Application:** Task Manager (To-Do List)
**Architecture:** Multi-container (Frontend + Backend + Database)
**Containers:** 3 (Nginx, Flask, PostgreSQL)
**Network:** Custom Docker network
**Persistence:** Named volume for database

---

## Build Process

### Backend
**Image size:** [148MB]
**Build time:** [30 mins]
**Dockerfile optimization:** [Layer catching]

### Nginx
**Image size:** [62.1]
**Configuration:** [Reverse proxy + static files]

---

## Deployment Steps Completed

1. ✅ Created custom network
2. ✅ Created persistent volume
3. ✅ Built backend image
4. ✅ Built nginx image
5. ✅ Started database container
6. ✅ Started backend container
7. ✅ Started nginx container
8. ✅ Tested complete application

---

## Testing Results

### Functional Tests
- Add task: ✅
- Mark complete: ✅
- Delete task: ✅
- Multiple tasks: ✅

### Technical Tests
- Data persistence: ✅ [Survived container restart]
- Network communication: ✅ [All containers can communicate]
- API health: ✅ [Endpoint responds]
- Reverse proxy: ✅ [API requests proxied correctly]

---

## Challenges Faced

### Challenge 1: [Nginx Image]
**Problem:** [Building image]
**Solution:** [In dockerfile, it uses only current folder files not preceding folders, therefore changing the COPY location in docker file and building in under main folder with -f for dockerfile path.]
**Learning:** [Dockerfile concepts]

### Challenge 2: [Nginx Container]
**Problem:** [CMD mistake]
**Solution:** [Didn't used ";" in daemon off]
**Learning:** [CMD is docker file is necessary and should be in correct syntax]

---

## Skills Applied (Week 1 Review)

**Day 1:** Running containers
- Used: `docker run`, `docker ps`, `docker logs`

**Day 2:** Image layers
- Applied: Understanding caching in Dockerfiles

**Day 3:** Writing Dockerfiles
- Created: 2 custom Dockerfiles (backend, nginx)

**Day 4:** Building applications
- Built: Complete Flask REST API

**Day 5:** Debugging
- Used: `docker logs`, `docker exec`, troubleshooting

**Day 6:** Data persistence
- Implemented: Volume for PostgreSQL data

---

## Architecture Diagram
```
[Browser]
    ↓ HTTP (port 80)
[Nginx Container]
    ├─→ Static Files (/, /style.css, /app.js)
    └─→ API Proxy (/api/*) → [Backend Container]
                                    ↓ SQL
                              [PostgreSQL Container]
                                    ↓
                              [Volume: task-db-data]
```

---

## Final Statistics

**Total containers:** 3
**Total images built:** 2
**Total volumes:** 1
**Total networks:** 1

---

## What I Learned

**Technical Skills:**
1. [Docker networks]
2. [Data persistency in volumes]
3. [Troubleshooting]

**Problem-Solving:**
[Debugging and troubleshooting containers, images and dockerfiles ]

**Best Practices:**
[Use layer caching and using volumes to store data]

---

## Next Steps

**Future Enhancements:**
- [ ] Add Docker Compose
- [ ] Implement user authentication
- [ ] Add more features
- [ ] Deploy to cloud

---

## Confidence Assessment

**Multi-container apps:** [6]
**Docker networking:** [7]
**Data persistence:** [7]
**Production readiness:** [7]
**Overall Docker skills:** [8]

---

## Week 1 Complete! 🎉

**Days completed:** 7/7
**Skills mastered:** ✅ Docker fundamentals
**Ready for Week 2:** ✅ Yes

**What's next:**
Week 2 will cover Docker Compose, optimization, and deployment strategies.

---

✅ Day 7 Complete
✅ Week 1 Complete

**Capstone Status:** Production-Ready Application Deployed
