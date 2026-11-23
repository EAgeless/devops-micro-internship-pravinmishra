# Assignment 7 — Capstone: Deploy a Production-Grade Stack for The EpicBook

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook application as a production-oriented Docker Compose stack on a cloud VM. You will use optimized container images, isolated networks, health checks, persistent MySQL storage, a selected reverse proxy, logging, backup and restore testing, and reliability procedures.

---

# Task 0 — App Discovery and Architecture

## Goal

Review the EpicBook repository and design the intended application architecture.

### Evidence

#### Screenshot 1 — EpicBook Project Structure

Add a terminal screenshot showing the EpicBook project structure after cloning the repository.

![EpicBook Project Structure](screenshots/Wk-11-Ass-7-scrn-1.jpeg)

---

#### Screenshot 2 — Architecture Diagram

Add a screenshot of your architecture diagram showing:

- Public user
- Reverse proxy
- Frontend
- Backend
- Database
- Docker networks
- Public and private ports
- Persistent database storage

Add your full name inside the diagram or as a clear caption below it.

![Architecture Diagram](screenshots/Wk-11-Ass-7-scrn-2.jpeg)

---

#### Screenshot 3 — Environment Variables and Ports Document

Add a screenshot showing the contents of:

```text
docs/02-env-and-ports.md
```

It must document environment-variable names, internal ports, persistent-data details, and the health-check method. Do not expose real credentials or values.

![Environment Variables and Ports Document](screenshots/Wk-11-Ass-7-scrn-3A.jpeg)

![Environment Variables and Ports Document](screenshots/Wk-11-Ass-7-scrn-3B.jpeg)

![Environment Variables and Ports Document](screenshots/Wk-11-Ass-7-scrn-3C.jpeg)

---

# Task 1 — Create Production Docker Images

## Goal

Create optimized production images for the EpicBook backend and frontend.

### Evidence

#### Screenshot 4 — Backend Dockerfile

Add a screenshot showing `backend/Dockerfile`, including:

- Dependency stage
- Minimal runtime stage
- Production startup command
- Internal backend port
- Non-root user configuration

![Backend Dockerfile](screenshots/Wk-11-Ass-7-scrn-4.jpeg)

---

#### Screenshot 5 — Frontend Dockerfile

Add a screenshot showing `frontend/Dockerfile`, including:

- Nginx runtime image
- Static frontend files copied to the Nginx web root

![Frontend Dockerfile](screenshots/Wk-11-Ass-7-scrn-5.jpeg)

---

#### Screenshot 6 — Docker Ignore Files

Add a screenshot showing both:

```text
backend/.dockerignore
frontend/.dockerignore
```

![Docker Ignore Files](screenshots/Wk-11-Ass-7-scrn-6.jpeg)

---

#### Screenshot 7 — Docker Image Builds and Size Comparison

Add a terminal screenshot showing successful builds of:

- Baseline backend image
- Optimized backend image
- Frontend image

The screenshot must also show the baseline and optimized backend image-size comparison.

![Docker Image Builds](screenshots/Wk-11-Ass-7-scrn-7.jpeg)

---

#### Screenshot 8 — Backend Running as Non-Root User

Add a terminal screenshot showing the optimized backend container running as a non-root user.

![Backend Running as Non-Root User](screenshots/Wk-11-Ass-7-scrn-8.jpeg)

---

### Notes

Write a short note covering:

- Baseline and optimized backend image sizes
- The image-size reduction achieved
- One Docker layer-caching optimization used
- The security benefit of running the backend as a non-root user

The baseline backend image was approximately 1,000 MB, while the optimized image was approximately 200 MB, achieving a reduction of 800 MB, or 80%. To improve Docker layer caching, I copied package.json and package-lock.json and installed dependencies before copying the application source code. This allows Docker to reuse the dependency-installation layer when only the source code changes. Running the backend as a non-root user also reduces its privileges, limiting the potential impact of an application compromise.

---

# Task 2 — Create the Docker Compose Stack and Networks

## Goal

Create one Docker Compose stack containing the reverse proxy, frontend, backend, and MySQL database.

### Evidence

#### Screenshot 9 — Docker Compose Services

Add a screenshot showing `docker-compose.yml` with all four services:

```text
reverse-proxy
frontend
backend
database
```

![Docker Compose Services](screenshots/Wk-11-Ass-7-scrn-9.jpeg)

---

#### Screenshot 10 — Networks and Named Volume

Add a screenshot showing:

- `front-tier` network
- `back-tier` network
- `db_data` named volume

![Networks and Named Volume](screenshots/Wk-11-Ass-7-scrn-10.jpeg)

---

#### Screenshot 11 — Docker Compose Validation

Add a terminal screenshot showing successful Docker Compose validation without exposing environment-variable values or secrets.

![Docker Compose Validation](screenshots/Wk-11-Ass-7-scrn-11.jpeg)

---

# Task 3 — Configure Health Checks and Startup Dependencies

## Goal

Configure health checks and ensure services start only after their dependencies are healthy.

### Evidence

#### Screenshot 12 — Backend Health Endpoint

Add a screenshot showing the backend application configuration for the `/health` endpoint.

![Backend Health Endpoint](screenshots/Wk-11-Ass-7-scrn-12.jpeg)

---

#### Screenshot 13 — MySQL and Backend Health Checks

Add a screenshot showing `docker-compose.yml` with health checks for MySQL and the backend.

![MySQL and Backend Health Checks](screenshots/Wk-11-Ass-7-scrn-13A.jpeg)

![MySQL and Backend Health Checks](screenshots/Wk-11-Ass-7-scrn-13B.jpeg)

---

#### Screenshot 14 — Frontend and Reverse-Proxy Health Checks

Add a screenshot showing:

- Frontend health check
- Reverse-proxy health check
- `depends_on` conditions using `service_healthy`

![Frontend and Reverse-Proxy Health Checks](screenshots/Wk-11-Ass-7-scrn-14A.jpeg)

![Frontend and Reverse-Proxy Health Checks](screenshots/Wk-11-Ass-7-scrn-14B.jpeg)

---

#### Screenshot 15 — Running Healthy Services

Add a terminal screenshot showing Docker Compose service status. The database, backend, frontend, and reverse proxy must be running successfully.

![Running Healthy Services](screenshots/Wk-11-Ass-7-scrn-15.jpeg)

---

#### Screenshot 16 — Public Health Endpoint

Add a terminal screenshot showing a successful response from the public application health endpoint through the reverse proxy.

![Public Health Endpoint](screenshots/Wk-11-Ass-7-scrn-16.jpeg)

---

#### Screenshot 17 — Health-Check and Startup-Order Document

Add a screenshot showing the contents of:

```text
docs/03-healthchecks-and-depends-on.md
```

Explain the health-check method for each service and the startup dependency order.

![Health-Check and Startup-Order](screenshots/Wk-11-Ass-7-scrn-15.jpeg)

---

# Task 4 — Configure the Reverse Proxy and Same-Origin Routing

## Goal

Use either Nginx or Traefik as the only public entry point for the EpicBook application.

### Evidence

#### Screenshot 18 — Selected Reverse-Proxy Configuration

Add a screenshot showing the configuration for your selected reverse proxy.

It must show routes for:

- Static frontend assets
- Application pages
- API requests
- Health endpoint

![Selected Reverse-Proxy Configuration](screenshots/Wk-11-Ass-7-scrn-14B.jpeg)

---

#### Screenshot 19 — Only Reverse Proxy Publishes Port 80

Add a screenshot of `docker-compose.yml` showing that only the `reverse-proxy` service publishes port 80.

![Only Reverse Proxy Publishes Port 80](screenshots/Wk-11-Ass-7-scrn-15.jpeg)
---

#### Screenshot 20 — Reverse-Proxy Route Testing

Add a terminal screenshot showing successful requests through the selected reverse proxy to:

- Application page
- One API endpoint
- One static asset
- Health endpoint

![Reverse-Proxy Route Testing](screenshots/Wk-11-Ass-7-scrn-20.jpeg)

---

#### Screenshot 21 — EpicBook Application Through Public IP

Add a browser screenshot showing the EpicBook application loaded through the VM public IP address.

Add your full name as a clear caption below the screenshot.

![EpicBook Application Through Public IP](screenshots/Wk-11-Ass-7-scrn-21.jpeg)

---

#### Screenshot 22 — Proxy Routing and CORS Document

Add a screenshot showing the contents of:

```text
docs/04-proxy-routing-and-cors.md
```

Explain the proxy routes and state whether CORS was required and why.


---

# Task 5 — Prove Data Persistence, Backup, and Restore

## Goal

Verify MySQL persistence and perform a controlled backup and restore drill.

### Evidence

#### Screenshot 23 — MySQL Volume Configuration

Add a terminal screenshot showing the `db_data` named volume and its MySQL mount configuration.

![MySQL Volume Configuration](screenshots/Wk-11-Ass-7-scrn-23.jpeg)

---

#### Screenshot 24 — Test Data Before Backup

Add a terminal screenshot showing the selected test data before the backup and restore drill.

![Test Data Before Backup](screenshots/Wk-11-Ass-7-scrn-24.jpeg)

---

#### Screenshot 25 — Successful Backup Creation

Add a terminal screenshot showing successful backup creation and the backup file stored in the host backup directory.

![Successful Backup Creation](screenshots/Wk-11-Ass-7-scrn-25.jpeg)

---

#### Screenshot 26 — Controlled Data-Loss Test

Add a terminal screenshot showing that the selected test record was removed during the controlled data-loss test.

![Controlled Data-Loss Test](screenshots/Wk-11-Ass-7-scrn-26.jpeg)

---

#### Screenshot 27 — Restore Verification

Add a terminal screenshot showing successful restore and verification that the deleted test record is available again.

![Restore Verification](screenshots/Wk-11-Ass-7-scrn-27.jpeg)

---

#### Screenshot 28 — Persistence After Down/Up Cycle

Add a terminal screenshot showing that database data remains available after a non-destructive Docker Compose down/up cycle.

Do not use `docker compose down -v`.

![Persistence After Down/Up Cycle](screenshots/Wk-11-Ass-7-scrn-28.jpeg)

---

#### Screenshot 29 — Persistence and Backup Document

Add a screenshot showing the contents of:

```text
docs/05-persistence-and-backup.md
```

Include the backup plan and restore procedure.

![Persistence and Backup Document](screenshots/Wk-11-Ass-7-scrn-29A.jpeg)

![Persistence and Backup Document](screenshots/Wk-11-Ass-7-scrn-29B.jpeg)

---

# Task 6 — Configure Logging and Observability

## Goal

Configure useful reverse-proxy and backend logs without exposing sensitive information.

### Evidence

#### Screenshot 30 — Logging Configuration

Add a screenshot showing:

- Configuration for the selected reverse proxy
- Proxy log format
- Docker Compose host log-directory bind mount

![Logging Configuration](screenshots/Wk-11-Ass-7-scrn-30.jpeg)

---

#### Screenshot 31 — Persistent Proxy Logs and Backend Logs

Add a terminal screenshot showing:

- Selected reverse-proxy logs available from the host directory after a proxy restart
- Backend logs displayed through Docker Compose

![Persistent Proxy Logs and Backend Logs](screenshots/Wk-11-Ass-7-scrn-31.jpeg)

---

### Notes

Write a short note covering:

- The selected reverse proxy
- Host path used for reverse-proxy logs
- How backend logs are viewed
- Whether JSON or standard text logs were used
- Why passwords, tokens, headers, and database connection strings must not appear in logs

I selected reverse-proxy to forward incoming requests to the application services. Reverse-proxy logs are stored on the VM, bind-mounted into the proxy container so they remain accessible outside the container. Backend logs are viewed using docker compose logs backend, or docker compose logs -f backend to follow them in real time.

The deployment uses standard logs. Passwords, access tokens, sensitive headers such as Authorization and Cookie, and database connection strings must not appear in logs because they could expose credentials, enable account impersonation, or allow unauthorized database access. Sensitive values should be omitted or redacted while retaining useful diagnostic information.

---

# Task 7 — Deploy and Verify the Stack on a Cloud VM

## Goal

Deploy the completed Docker Compose stack on an AWS or Azure VM and verify public access.

### Evidence

#### Screenshot 32 — VM Public IP and Inbound Rules

Add a cloud-console screenshot showing:

- VM public IP address
- SSH port 22 restricted to your IP address
- HTTP port 80 allowed from Anywhere

![VM Public IP and Inbound Rules](screenshots/Wk-11-Ass-7-scrn-32A.jpeg)

![VM Public IP and Inbound Rules](screenshots/Wk-11-Ass-7-scrn-32B.jpeg)

---

#### Screenshot 33 — Cloud VM Stack Verification

Add a VM terminal screenshot showing:

- Docker Compose service status
- Successful public health or API response
- No published database, frontend, or backend ports

![Cloud VM Stack Verification](screenshots/Wk-11-Ass-7-scrn-33.jpeg)

---

#### Screenshot 34 — EpicBook Application on Cloud VM

Add a browser screenshot showing the EpicBook application loaded through the VM public IP address.

Add your full name as a clear caption below the screenshot.

![EpicBook Application on Cloud VM](screenshots/Wk-11-Ass-7-scrn-34.jpeg)

---

### Notes

I deployed the Book Review App on an Azure VM running Ubuntu 24.04 LTS. For the reverse-proxy deployment, I exposed HTTP port 80 as the public application entry point. The security rules allowed public HTTP access while restricting SSH on port 22 to my IP address and keeping MySQL port 3306 private. I verified that both the frontend and backend API worked through the reverse proxy by accessing the application through the VM’s public IP, logging in, and successfully submitting a book review.

---

# Task 8 — Automate Deployment with CI/CD (Optional)

## Goal

Optionally automate image build, image push, and deployment through GitHub Actions or Azure Pipelines.

### Optional Evidence

#### Optional Screenshot — Successful CI/CD Pipeline Run

Add a screenshot showing a successful pipeline run with build, image push, deployment, and verification stages.


---

### Optional Notes

Write a short note covering:

- CI/CD platform used
- Image-tagging method
- Registry used
- Deployment trigger
- Manual approval or secret-handling approach


I used Azure Pipelines to automate building and deploying the application. Each container image was tagged with the Git commit ID to make releases traceable and published to Docker Hub. A push to the main branch triggered the pipeline to build and publish the images, followed by deployment to the Azure VM. Registry credentials and SSH deployment credentials were stored as protected pipeline secrets rather than committed to the repository or printed in logs.


---

# Task 9 — Perform Reliability Tests and Create an Operations Runbook

## Goal

Test controlled service failures and document safe operating procedures.

### Evidence

#### Screenshot 35 — Backend Failure and Recovery

Add a terminal screenshot showing:

- Backend failure test
- Expected unavailable response through the reverse proxy
- Backend restart
- Successful health-check recovery

![Backend Failure and Recovery](screenshots/Wk-11-Ass-7-scrn-35.jpeg)

---

#### Screenshot 36 — Database Failure and Recovery

Add a terminal screenshot showing:

- Database outage test
- Failed database-dependent request
- Database restart
- Successful application recovery

![Database Failure and Recovery](screenshots/Wk-11-Ass-7-scrn-36.jpeg)

![Database Failure and Recovery](screenshots/Wk-11-Ass-7-scrn-36B.jpeg)

![Database Failure and Recovery](screenshots/Wk-11-Ass-7-scrn-36C.jpeg)

---

### Notes

Write a short operations runbook covering:

- Safe restart procedure for reverse proxy, frontend, backend, and database
- Backup and restore procedure
- Secret-rotation approach
- Database recovery procedure
- What to check when the application returns an error
- Results of backend and database reliability tests

Safe restart procedure
I restart only the affected service using docker compose restart followed by its service name. For a full maintenance restart, I bring MySQL back first and confirm it is healthy, then restart the backend, frontend, and reverse proxy in that order. I verify service status, database connectivity, and browser access afterward. I avoid docker compose down -v because it deletes the named database volume.

Backup and restore procedure
Before major changes, I take a logical MySQL backup using mysqldump with securely supplied credentials. I store backups outside the container, restrict access, and keep an encrypted off-VM copy. To restore, I pause application writes, import a verified backup into MySQL, and check the user and review records before restoring application access. I test backup restoration separately rather than assuming a backup is usable.

Secret rotation
I generate new, unique credentials and update the protected secret store or local untracked .env file. For database passwords, I change the actual MySQL account password and update the backend configuration together; editing .env alone is not a database password rotation. I recreate affected containers to load the updated environment. Rotating the JWT signing secret requires users to log in again. Secrets must never appear in Git, screenshots, or logs.

Database recovery
If MySQL fails, I inspect its logs, available disk space, volume mount, and health status before making changes. I preserve the existing volume and avoid destructive cleanup. If restoration is necessary, I retain a copy of the failed data where possible, restore a verified backup to a separate recovery volume, and validate it before reconnecting the application.

Application-error checks
I check container status, reverse-proxy logs, backend logs, and MySQL logs. I then verify upstream addresses, published ports, database connectivity, browser-facing API URLs, and CORS configuration. Browser DevTools helps identify the failing request and response. During troubleshooting, I corrected missing /api prefixes that caused registration requests to return 404 Not Found.

Reliability-test results
The completed down/up persistence test showed that the same user account and original book review remained available after the containers were removed and recreated. Registration, login, review creation, and browser API access also worked after the frontend route correction. This demonstrates functional operation and database persistence; separate backend-crash and database-crash recovery results have not been recorded in the work shown here.

---

# Final Public Application URL

**EpicBook URL:** `http://20.3.152.219`

Replace the placeholder with your working public URL.

---

# GitHub Repository URL

**Your Fork or Repository URL:** `https://github.com/EAgeless/theepicbook.git`

---

# LinkedIn Requirement

## Goal

Create a professional LinkedIn post of 6–10 lines about your EpicBook capstone deployment.

Your post must include:

- The architectural decision that most improved reliability
- Your biggest image-size reduction, with numbers
- Key production-hardening lessons
- A deployment verification image

### Evidence

**LinkedIn Post URL:** `https://lnkd.in/p/dgj8Z7Y2`

#### LinkedIn Post Screenshot

Add a screenshot of the published LinkedIn post showing the text body and deployment verification image.

---

# Submission Checklist

- [ ] EpicBook repository reviewed and architecture diagram created
- [ ] Environment variables, ports, persistence, and health-check details documented
- [ ] Backend and frontend production Dockerfiles created
- [ ] Backend runs as a non-root user
- [ ] Docker image-size comparison completed
- [ ] Docker Compose stack includes reverse proxy, frontend, backend, and database
- [ ] `front-tier` and `back-tier` networks configured
- [ ] `db_data` named volume configured
- [ ] MySQL, backend, frontend, and reverse-proxy health checks configured
- [ ] Startup dependencies use `service_healthy`
- [ ] Nginx or Traefik selected as the only public reverse proxy
- [ ] Only reverse-proxy port 80 is publicly published
- [ ] Same-origin routing configured and CORS used only when required
- [ ] Backup, restore, and persistence testing completed
- [ ] Reverse-proxy and backend logs verified
- [ ] Cloud VM deployment verified through the public IP
- [ ] Backend and database reliability tests completed
- [ ] Screenshots 1–36 included
- [ ] Required notes completed
- [ ] LinkedIn post URL and screenshot included
- [ ] Full name visible in required screenshots or captions
- [ ] No passwords, tokens, private keys, account IDs, or other sensitive information exposed

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
