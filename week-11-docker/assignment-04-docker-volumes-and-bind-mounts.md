# Assignment 4 — Docker Volumes and Bind Mounts

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will use Docker Bind Mounts and Docker Volumes to persist logs and application data outside a container’s lifecycle. You will verify that data remains available after containers are removed and recreated.

---

# Task 1 — Persist Nginx Logs Using a Bind Mount

## Goal

Deploy an Nginx container with a Bind Mount and verify that its log files remain on the VM host after the container is removed.

### Evidence

#### Screenshot 1 — Nginx Image Pull

Add a screenshot of the terminal showing successful completion of:

```bash
docker pull nginx:alpine
```

![Nginx Image Pull](screenshots/Wk-11-Ass-4-scrn-1.png)

---

#### Screenshot 2 — Host Log Directory

Add a screenshot of the terminal showing the created host directory:

```text
$HOME/nginx-logs
```

![Host Log Directory](screenshots/Wk-11-Ass-4-scrn-2.png)

---

#### Screenshot 3 — Running Nginx Container with Port Mapping

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show the `myweb` container with:

```text
0.0.0.0:80->80/tcp
```

![Running Nginx Container](screenshots/Wk-11-Ass-4-scrn-3.png)

---

#### Screenshot 4 — Nginx Welcome Page

Add a browser screenshot showing the Nginx Welcome Page at:

```text
http://<YOUR-VM-PUBLIC-IP>
```

Ensure that the VM public IP is visible in the address bar. Add your full name as a clear caption directly below the screenshot.

![Nginx Welcome Page](screenshots/Wk-11-Ass-4-scrn-4.jpeg)

---

#### Screenshot 5 — Bind-Mounted Log Files

Add a screenshot of the terminal showing the host log files and access-log content from:

```text
$HOME/nginx-logs
```

The output must show `access.log`, `error.log`, and an access-log entry created when you opened the Nginx page.

![Bind-Mounted Log Files](screenshots/Wk-11-Ass-4-scrn-5.png)

---

#### Screenshot 6 — Nginx Container Removed

Add a screenshot of the terminal showing successful completion of:

```bash
docker stop myweb
docker rm myweb
```

![Nginx Container Removed](screenshots/Wk-11-Ass-4-scrn-6.png)

---

#### Screenshot 7 — Logs Persist After Container Removal

Add a screenshot of the terminal showing that `access.log` and `error.log` still exist in:

```text
$HOME/nginx-logs
```

The access log must retain its content after the container has been removed.

![Logs Persist After Container Removal](screenshots/Wk-11-Ass-4-scrn-7.png)

---

# Task 2 — Share Persistent Data Using a Docker Volume

## Goal

Deploy backend and frontend containers that share data through a named Docker Volume. Verify that the data remains after both containers are removed and recreated.

### Evidence

#### Screenshot 8 — Project File Structure

Add a screenshot of the terminal showing the `two-tier-app` project structure, including separate `backend` and `frontend` directories with a `Dockerfile` and `index.js` file in each.

![Project File Structure](screenshots/Wk-11-Ass-4-scrn-8.png)

---

#### Screenshot 9 — Custom Docker Network

Add a screenshot of the terminal showing `mynetwork` in:

```bash
docker network ls
```

![Custom Docker Network](screenshots/Wk-11-Ass-4-scrn-9.png)

---

#### Screenshot 10 — Docker Volume

Add a screenshot of the terminal showing `shared-data` in:

```bash
docker volume ls
```

![Docker Volume](screenshots/Wk-11-Ass-4-scrn-10.png)

---

#### Screenshot 11 — Backend Dockerfile

Add a screenshot of the terminal showing the completed backend `Dockerfile`.

![Backend Dockerfile](screenshots/Wk-11-Ass-4-scrn-11.png)

---

#### Screenshot 12 — Backend Image Build

Add a screenshot of the terminal showing successful completion of the `backend-app:latest` image build.

![Backend Image Build](screenshots/Wk-11-Ass-4-scrn-12.png)

---

#### Screenshot 13 — Running Backend Container

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show the running `backend` container.

![Running Backend Container](screenshots/Wk-11-Ass-4-scrn-13.png)

---

#### Screenshot 14 — Frontend Dockerfile

Add a screenshot of the terminal showing the completed frontend `Dockerfile`.

![Frontend Dockerfile](screenshots/Wk-11-Ass-4-scrn-14.png)

---

#### Screenshot 15 — Frontend Image Build

Add a screenshot of the terminal showing successful completion of the `frontend-app:latest` image build.

![Frontend Image Build](screenshots/Wk-11-Ass-4-scrn-15.png)

---

#### Screenshot 16 — Running Backend and Frontend Containers

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show both `backend` and `frontend` containers running. Only `frontend` must have the published port mapping:

```text
0.0.0.0:80->80/tcp
```

![Running Backend and Frontend Containers](screenshots/Wk-11-Ass-4-scrn-16.png)

---

#### Screenshot 17 — Backend Write Operation

Add a screenshot of the terminal showing a successful backend write operation to the shared Docker Volume.

The output must include:

```text
Data written: Hello from Backend!
```

![Backend Write Operation](screenshots/Wk-11-Ass-4-scrn-17.png)

---

#### Screenshot 18 — Frontend Reads Shared Data

Add a browser screenshot showing:

```text
Hello from Backend!
```

Add your full name as a clear caption directly below the screenshot.

![Frontend Reads Shared Data](screenshots/Wk-11-Ass-4-scrn-18.png)

---

#### Screenshot 19 — First Shared-Data Update

Add a browser screenshot showing:

```text
Test Data 1
```

Add your full name as a clear caption directly below the screenshot.

![First Shared-Data Update](screenshots/Wk-11-Ass-4-scrn-19.png)

---

#### Screenshot 20 — Second Shared-Data Update

Add a browser screenshot showing:

```text
Test Data 2 - New Update
```

Add your full name as a clear caption directly below the screenshot.

![Second Shared-Data Update](screenshots/Wk-11-Ass-4-scrn-20.png)

---

#### Screenshot 21 — Container Removal and Recreation

Add a screenshot of the terminal showing the `frontend` and `backend` containers removed and recreated using the same `shared-data` Docker Volume.

![Container Removal and Recreation](screenshots/Wk-11-Ass-4-scrn-21.png)

---

#### Screenshot 22 — Data Persists After Recreation

Add a browser screenshot showing:

```text
Test Data 2 - New Update
```

This proves that the `shared-data` Docker Volume outlived both application containers.

Add your full name as a clear caption directly below the screenshot.

![Data Persists After Recreation](screenshots/Wk-11-Ass-4-scrn-21.png)

---

# Storage Persistence Notes

Write a short explanation covering:

- The difference between a Bind Mount and a Docker Volume
- How Task 1 proved Bind Mount persistence
- How Task 2 proved Docker Volume persistence
- Why Docker Volumes are commonly used for application data

A bind mount and a Docker volume both allow data to persist outside a container’s lifecycle, but they work differently. A bind mount links a specific directory or file on the host machine directly into a container, while a Docker volume is a storage location created and managed by Docker.

With a bind mount, you choose the exact host path to mount—for example, a folder containing Nginx logs. The container writes to that folder, and because the data is stored on the host, it remains available even after the container is removed. Bind mounts are useful when you need to share or edit files directly between the host and a container, such as source code, configuration files, or logs.

In Task 1, I proved bind-mount persistence by mounting a host directory into an Nginx container to store access and error logs. After removing the container, the log files remained in the host directory because they were not stored only inside the container. Recreating the container with the same bind mount would allow it to access the existing logs again. This demonstrated that a bind mount keeps data tied to the chosen host path rather than the container itself.

A Docker volume, on the other hand, is managed entirely by Docker. Docker creates the storage location on the host, and containers reference it by volume name rather than a host directory path. Volumes are isolated from the host’s directory structure, easier to back up or migrate, and can be safely shared among multiple containers.

In Task 2, I proved Docker volume persistence by creating a shared volume used by separate backend and frontend containers. The backend wrote data to the volume, while the frontend read the same data. After removing and recreating both containers, the data remained available because it was stored in the Docker-managed volume, not inside either container. This showed that volumes preserve application data independently of container lifecycles.

Docker volumes are commonly used for application data because application containers may be stopped, removed, or replaced, but important data must remain available. Volumes are the preferred option for data generated and used by containers, including database records, user uploads, caches, and application logs. They provide persistent, portable, and manageable storage without requiring developers to manage a specific host directory.

---

# Public Application URL

**Application URL:** http://20.3.152.219

---

# LinkedIn Requirement

## Goal

Create a LinkedIn post about Docker Volumes and Bind Mounts, including one difference between them, how you verified persistent storage, and your key learning outcomes.

### Evidence

#### LinkedIn Post URL


https://lnkd.in/p/dgj8Z7Y2

---

#### LinkedIn Post Screenshot

Add a screenshot of the published LinkedIn post here. Include a screenshot of the application displaying shared data.

---

# Submission Instructions

- Complete all tasks in sequence.
- Include Screenshots 1–22 exactly as specified.
- Include the Storage Persistence Notes section.
- Include the public application URL.
- Include the LinkedIn post URL and screenshot.
- Ensure that your full name is visible in all terminal screenshots.
- Add your full name as a clear caption below every browser screenshot.
- Do not expose private keys, passwords, access keys, tokens, account IDs, or other sensitive information.

---

# Completion Checklist

- [ ] Nginx image pulled successfully
- [ ] Host log directory created
- [ ] Bind Mount configured successfully
- [ ] Nginx logs remain after container removal
- [ ] Custom Docker network created
- [ ] Docker Volume created
- [ ] Backend Dockerfile and image created
- [ ] Frontend Dockerfile and image created
- [ ] Both containers mount `shared-data`
- [ ] Backend writes data to the Docker Volume
- [ ] Frontend reads the same data from the Docker Volume
- [ ] Updated data appears after browser refresh
- [ ] Data remains after frontend and backend containers are removed and recreated
- [ ] All required screenshots included
- [ ] Storage Persistence Notes completed
- [ ] Public application URL included
- [ ] LinkedIn post URL and screenshot included
- [ ] Full name visible in terminal screenshots
- [ ] Browser screenshots have full-name captions
- [ ] No sensitive information exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*
