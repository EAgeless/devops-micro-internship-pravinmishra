# Assignment 5 — Publish a Docker Container on Docker Hub

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will publish a Dockerized React application to Docker Hub, remove the local image tags, pull the image again from Docker Hub, and run it to verify that it can be downloaded and deployed from a container registry.

---

# Task 1 — Publish a Docker Image to Docker Hub

## Goal

Tag a locally built React image, publish it to Docker Hub, remove the local copy, pull it again from Docker Hub, and run it successfully.

### Evidence

#### Screenshot 1 — Public Docker Hub Repository

Add a screenshot of Docker Hub showing your newly created public repository:

```text
my-react-app
```

![Public Docker Hub Repository](screenshots/Wk-11-Ass-5-scrn-1.png)

---

#### Screenshot 2 — Successful Docker Login

Add a screenshot of the terminal showing:

```text
Login Succeeded
```

Ensure that your full name is visible and that no password, Personal Access Token, or device code is exposed.

![Successful Docker Login](screenshots/Wk-11-Ass-5-scrn-2.png)

---

#### Screenshot 3 — Correctly Tagged Image

Add a screenshot of the terminal showing:

```bash
docker image ls <YOUR_DOCKERHUB_USERNAME>/my-react-app
```

The output must show the `latest` tag.

![Correctly Tagged Image](screenshots/Wk-11-Ass-5-scrn-3.png)

---

#### Screenshot 4 — Successful Docker Push

Add a screenshot of the terminal showing successful completion of:

```bash
docker push <YOUR_DOCKERHUB_USERNAME>/my-react-app:latest
```

The output must include a pushed status or image digest.

![Successful Docker Push](screenshots/Wk-11-Ass-5-scrn-4.png)

---

#### Screenshot 5 — Published `latest` Tag in Docker Hub

Add a screenshot of your Docker Hub repository showing the uploaded `latest` image tag.

![Published `latest` Tag in Docker Hub](screenshots/Wk-11-Ass-5-scrn-5.png)

---

#### Screenshot 6 — Local Image Removed and Pulled Again

Add a screenshot of the terminal showing:

- The targeted local image tags removed
- Successful `docker pull` output
- `docker image ls` showing the pulled image

![Local Image Removed and Pulled Again](screenshots/Wk-11-Ass-5-scrn-6.png)

---

#### Screenshot 7 — Running Pulled Image

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show the running `react-container` with:

```text
0.0.0.0:80->80/tcp
```

![Running Pulled Image](screenshots/Wk-11-Ass-5-scrn-7.jpeg)

---

#### Screenshot 8 — React Application in Browser

Add a browser screenshot showing the React application at:

```text
http://20.3.152.219
```

Ensure that the VM public IP is visible in the address bar. Add your full name as a clear caption directly below the screenshot.

![React Application in Browser](screenshots/Wk-11-Ass-5-scrn-8.jpeg)

---

# Docker Hub Repository URL

**Repository URL:** https://github.com/EAgeless/book-review-app.git

---

# Registry and Image Tagging Notes

Write a short explanation covering:

- Why image tagging is required before pushing to Docker Hub
- Why a container registry is useful in DevOps workflows
- Why production deployments should use versioned image tags instead of relying only on `latest`

Image tagging is required before pushing to Docker Hub because a tag identifies the Docker Hub namespace, repository, and version of the image being uploaded. For example, agelesseva/my-react-app:latest identifies the Docker Hub username, repository name, and image tag. Docker uses this information to determine where the image should be stored when docker push is run. Without the correct tag, Docker would not know which repository the image belongs to.

A tag is a human-readable label that points to a specific image version or variant. If no tag is provided, Docker uses latest by default. Tags allow multiple versions of an image to be stored and managed within one Docker Hub repository.

A container registry is useful in DevOps workflows because it provides a central location to store, share, and distribute container images. After an image is built and tested, it can be pushed to a registry such as Docker Hub and later pulled into development, staging, or production environments. This ensures that teams deploy the same tested image rather than rebuilding it separately on every server.

Registries also support automation in CI/CD pipelines. A pipeline can build an image, tag it, push it to a registry, and then deploy that exact image to the next environment. This makes deployments more repeatable, traceable, and consistent across teams and infrastructure.

Production deployments should use versioned image tags instead of relying only on latest because latest is mutable. A publisher can update the latest tag to point to a newer image, meaning that a deployment using latest may receive a different version without anyone explicitly changing the deployment configuration.

Versioned tags such as 1.0, 1.2.3, or a release-specific tag make it clear exactly which image is running in production. They also make rollbacks easier: if a new release causes an issue, the team can redeploy a previously tested version instead of guessing which image latest currently represents.

For example, using agelesseva/my-react-app:1.0 in production is safer than using agelesseva/my-react-app:latest because the versioned tag identifies a known, tested release. A good practice is to tag each release with a specific version and optionally update latest as a convenience pointer, while production deployments reference the fixed version tag

---

# LinkedIn Requirement

## Goal

Create a LinkedIn post about publishing a Docker container image to Docker Hub.

Include:

- Assignment title: **Publish a Docker Container Image to Docker Hub**
- Your Docker Hub repository URL
- What you published
- How you verified the remote image by pulling and running it
- Key learning outcomes

### Evidence

#### LinkedIn Post URL

https://lnkd.in/p/dgj8Z7Y2



---

#### LinkedIn Post Screenshot


---

# Submission Instructions

- Complete all steps in sequence.
- Include Screenshots 1–8 exactly as specified.
- Include your Docker Hub repository URL.
- Include the Registry and Image Tagging Notes.
- Include the LinkedIn post URL and screenshot.
- Ensure that your full name is visible in all terminal screenshots.
- Add your full name as a clear caption below the browser screenshot.
- Do not expose passwords, Personal Access Tokens, device codes, credentials, or other sensitive information.

---

# Completion Checklist

- [ ] Public `my-react-app` repository created
- [ ] Docker login completed successfully
- [ ] `react-multistage:latest` tagged correctly
- [ ] Image pushed to Docker Hub
- [ ] `latest` tag verified in Docker Hub
- [ ] Targeted local image tags removed
- [ ] Image pulled again from Docker Hub
- [ ] Pulled image runs successfully
- [ ] React application is accessible through the VM public IP
- [ ] Docker Hub repository URL included
- [ ] Registry and image-tagging notes completed
- [ ] LinkedIn post URL and screenshot included
- [ ] All required screenshots included
- [ ] Full name visible in terminal screenshots
- [ ] Browser screenshot has a full-name caption
- [ ] No passwords, tokens, or credentials exposed
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
