# Assignment 4 — Gotto Job: Backlog Refinement & Sprint 1 in Jira

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this 90-minute, time-boxed exercise, you will act as a Scrum team — or run in Solo Mode, playing every role yourself — to turn the Gotto Job template into a value-ordered backlog, estimate the work in story points, plan Sprint 1, open the burndown chart, and ship one small UI-only increment (text, color, spacing, a label, or a CTA — no backend changes).

---

# Task 1 — Roles & Mode Setup (Team vs Solo)

## Goal

Choose Team Mode or Solo Mode, and document how each Scrum role (Product Owner, Scrum Master, Dev Lead, DevOps Lead) was handled.

### Evidence

#### Screenshot 1 — Jira "Create project" screen, or the project sidebar after creation

![Jira "Create project" screen](screenshots/Wk-05-Ass-4-scrn-1.png)

---

### Notes

Write one line for each role: PO (what you prioritized), SM (how you ensured process), Dev Lead (what you built), DevOps Lead (how you shipped).

I worked in Solo Mode, so I played all four roles:

Product Owner (PO): I prioritized small, high‑impact UI changes that improve clarity and trust first (hero tagline, CTA color, and job card typography) instead of starting with heavier layout work.

Scrum Master (SM): I enforced the 90‑minute time box, kept the Jira board clean (statuses and subtasks updated), and made sure every story had clear acceptance criteria before starting work.

Dev Lead: I implemented the selected UI change on the Gotto Job template (updating the hero tagline text and verifying the design stayed clean on desktop and mobile).

DevOps Lead: I handled Git branching, commits, and the deploy to my EC2 Nginx host, then verified the live URL and attached proof back to Jira.

---

# Task 2 — Create the Jira Project (Team-managed → Scrum)

## Goal

Create a Team-managed Scrum project named `Gotto Job – Team <#>` (Team Mode) or `Gotto Job – <YourName>` (Solo Mode).

### Evidence

#### Screenshot 2 — Project created page showing the project name and key

![Project created page](screenshots/Wk-05-Ass-4-scrn-2.png)

---

# Task 3 — Create the Epic

## Goal

Create the Epic `Improve Gotto Job UI discoverability & trust` to group the UI improvement initiative.

### Evidence

#### Screenshot 3 — Backlog showing the Epic panel with the Epic visible

![Backlog showing the Epic panel](screenshots/Wk-05-Ass-4-scrn-3.png)

---

# Task 4 — Seed the Product Backlog (6–8 Stories + Fibonacci Points + Ranking)

## Goal

Create at least six Stories under the Epic, estimate each with 1, 2, or 3 story points, and rank them by value.

### Evidence

#### Screenshot 4 — Backlog showing the Epic and at least six Stories under it

![Backlog showing the Epic](screenshots/Wk-05-Ass-4-scrn-4.png)

---

#### Screenshot 5 — One Story opened showing its Story Points and acceptance criteria filled in

![One Story opened showing its Story Points](screenshots/Wk-05-Ass-4-scrn-5.png)

---

# Task 5 — Planning Poker (Estimate + Debate Notes)

## Goal

Confirm the Story Points (1, 2, or 3) for each Story and record brief reasoning for each estimate.

### Evidence

#### Screenshot 6 — Backlog showing Story Points visible, or two or three Stories opened showing their points

![Backlog showing Story Points visible](screenshots/Wk-05-Ass-4-Scrn-6.png)

---

### Notes

For each story, explain in one or two lines why it is a 1, 2, or 3 (mention any debate, even in Solo Mode).

1‑point stories (Hero tagline, CTA color, Posted date text, Footer trust links):
Small CSS/HTML changes, low risk, minimal testing surface. I estimated 1 point because they can be done quickly within the sprint and have clear acceptance criteria.

2‑point stories (Job card typography, Remote badge, Advanced search labels if you added it):
Touch more elements, require responsive checks, and can affect multiple pages. I estimated 2 points because they involve more selectors and layout testing but still UI-only.

Debate (even in Solo Mode):
I briefly considered making the remote badge 3 points because of card variations, but kept it at 2 once I confirmed it was mostly a repeated pattern.

---

# Task 6 — Sprint Planning: Create Sprint 1 + Sprint Goal + Scope

## Goal

Create Sprint 1, move three or four Stories into it (approximately 3–6 points), set the Sprint Goal, and break each selected Story into Build, Verify, Deploy, and Screenshot Sub-tasks.

### Evidence

#### Screenshot 7 — Sprint 1 with the selected Stories inside it

![Sprint 1 with the selected Stories inside it](screenshots/Wk-05-Ass-4-scrn-7.png)

---

#### Screenshot 8 — One Story showing the Sub-tasks created

![One Story showing the Sub-tasks created](screenshots/Wk-05-Ass-4-scrn-8.png)

---

# Task 7 — Reports: Open Burndown Chart

## Goal

Open the Burndown Chart and confirm it exists for Sprint 1. It is acceptable if the chart is not yet populated.

### Evidence

#### Screenshot 9 — Burndown Chart page opened, even if empty

![Burndown Chart page opened](screenshots/Wk-05-Ass-4-scrn-9.png)

---

# Task 8 — Ship One Small Increment (Build + Deploy + Proof)

## Goal

Implement one small UI-only Story from Sprint 1, commit it, deploy it live, and move the Story and its Sub-tasks to Done in Jira.

### Evidence

#### Screenshot 10 — Jira board showing the Story moved to Done

![Jira board showing the Story moved to Done](screenshots/Wk-05-Ass-4-scrn-10.png)

---

#### Screenshot 11 — Git commit output

![Git commit output](screenshots/Wk-05-Ass-4-scrn-11.png)

---

#### Screenshot 12 — Live URL in the browser showing the UI change, with the URL visible

![Live URL in the browser showing the UI change](screenshots/Wk-05-Ass-4-scrn-12.png)

---

# Task 9 — Retro Notes (Scrum Pillar + Value)

## Goal

What went well:
Keeping the scope small (one UI story) made it easy to complete planning, implementation, and deployment inside the time box. Jira subtasks plus the burndown made my progress visible even in Solo Mode.

What to improve:
Next time I’d automate more of the deployment steps (scripted scp + Nginx reload) and schedule short checkpoints during the sprint instead of bunching deployment work toward the end.

Scrum pillar observed: Transparency
The Jira board, story points, and burndown chart made it clear what was planned, what was in progress, and what was actually done.

Scrum value observed: Focus
Limiting Sprint 1 to a few small UI stories helped me stay focused on shipping one real, verifiable increment instead of starting too many changes at once.

### Evidence

#### Screenshot 13 — Jira retro comment visible

![Jira retro comment visible](screenshots/Wk-05-Ass-4-scrn-13.png)

---

# Task 10 — LinkedIn Post (Mandatory)

## Goal

Publish a LinkedIn post about what you delivered, including your live URL, three to five lines on what you did and learned, and one screenshot (Burndown Chart, Sprint board, or the live UI change).

## Evidence

#### LinkedIn Post URL

https://www.linkedin.com/posts/evangeline-obeta-067089193_as-part-of-my-task-this-week-i-ran-a-focused-share-7491418198509207552-B6ix/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAC1lNQ8BKNctpF5K7KkXcW9PlnRd3JAwP3E


---

#### Screenshot 14 — Published LinkedIn post

![Published LinkedIn post](screenshots/Wk-05-Ass-4-LinkedInPost.png)

---

# Submission Instructions

- Add all 14 required screenshots
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Task 1: Team Mode or Solo Mode selected and all four roles documented (Screenshot 1 & Notes)
- [ ] Task 2: Team-managed Scrum project created with the required name (Screenshot 2)
- [ ] Task 3: UI improvement Epic created (Screenshot 3)
- [ ] Task 4: 6–8 Stories added under the Epic and ranked by value (Screenshots 4 & 5)
- [ ] Task 5: Story Points set (1, 2, or 3) with reasoning recorded (Screenshot 6 & Notes)
- [ ] Task 6: Sprint 1 created with Sprint Goal, 3–4 Stories, and Sub-tasks (Screenshots 7 & 8)
- [ ] Task 7: Burndown Chart opened (Screenshot 9)
- [ ] Task 8: One UI-only increment implemented, committed, deployed, and verified (Screenshots 10–12)
- [ ] Task 9: Retro comment with one Scrum pillar and one Scrum value (Screenshot 13)
- [ ] Task 10: Mandatory LinkedIn post published with the live URL, backlog refinement, Sprint planning, one shipped increment, proof, and Screenshot 14
- [ ] Full Name visible in required screenshots
- [ ] No sensitive data exposed

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

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
