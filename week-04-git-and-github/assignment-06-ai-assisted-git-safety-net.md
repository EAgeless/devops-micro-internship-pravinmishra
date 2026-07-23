# Assignment 6 — Building an AI-Assisted Git Safety Net (PR Ready Check)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In Week 2 you built Claude Code hooks that block a dangerous action *before* it happens (`PreToolUse`), and a restricted skill that could look but not touch (`allowed-tools` without `Write`). In this assignment you will discover that Git has the exact same idea, decades older: a **pre-commit hook** that blocks a commit before it's created.

You will build both halves of a real "PR Ready" workflow:

1. A **Git hook that follows fixed rules** — scans staged changes for hardcoded secrets and oversized files and refuses the commit. No AI involved, no guessing, just a rule that gives the same answer every time.
2. A **restricted Claude Code skill** (`/pr-ready`) that reads your staged diff and drafts a Pull Request title, description, and a short list of things worth a second look — the kind of judgment a fixed rule can't make (mixed changes, missing context, unclear intent). The skill never commits, pushes, or opens the PR. You do that yourself, using its draft as a starting point.

This mirrors the Agentic Loop from Week 3's Linux triage assignment: **Gather → Analyze → Human Act → Verify**. The hook and the skill both gather and analyze; only you act.

---

# Task 0 — Confirm Your Fork and Create a Feature Branch

## Goal

Confirm you are working in your own fork, then create a dedicated branch for this assignment.

### Evidence

#### Screenshot 1 — Output of git remote -v and git branch showing the new branch

![Output of git remote -v and git branch](screenshots/Wk-04-Ass-6-scrn-1.png)

---

### Notes

**1. Why create a dedicated branch instead of doing this work on main?**

Creating a dedicated branch is so that your change stays isolated from main, which keeps the main branch stable and makes review easier. It also lets you work safely, test your edit, and open a Pull Request without affecting everyone else’s work.

---

# Task 1 — Stage a Change With Realistic Risk

## Goal

On your own fork of this repository (the one you've been submitting your DMI work in since onboarding), create a new branch and stage a change that a real reviewer should catch: a hardcoded-looking secret and a leftover debug statement.

### Evidence

#### Screenshot 1 — Output of  `git status` showing the staged file on feature/ai-pr-ready

![Output of  `git status`](screenshots/Wk-04-Ass-6-scrn-2.png)
---

### Notes

**1. Why does this assignment use an obviously fake key instead of a real one?**

The assignment uses an obviously fake key so you can test secret-detection safely without risking a real credential. That way, the hook can prove it blocks sensitive-looking content, while no real security secret is ever exposed.

---

# Task 2 — Write a Real Git Pre-Commit Hook

## Goal

Create a tracked, shareable pre-commit hook that blocks a commit containing secret-like patterns or files over 1MB.

### Evidence

#### Screenshot 2 — `hooks/pre-commit` open in VS Code showing the full script

![`hooks/pre-commit` open in VS Code](screenshots/Wk-04-Ass-6-scrn-3.png)

---

#### Screenshot 3 — Output of `git config core.hooksPath` confirming it points to `hooks`

![Output of `git config core.hooksPath`](screenshots/Wk-04-Ass-6-scrn-4.png)

---

### Notes

**1. Why is `hooks/pre-commit` tracked in the repo instead of living only in `.git/hooks/`?**

hooks/pre-commit is tracked in the repository so the hook script itself becomes part of the project and can be shared with other contributors, while .git/hooks/ is local to one clone and is not versioned with the repository. By storing the hook in a normal tracked folder and pointing Git to it with core.hooksPath, the team can use the same pre-commit gate instead of everyone having different or missing local hooks.

---

**2. Compare this to `PreToolUse` from Week 2 Assignment 6. What does each one intercept, and what do they have in common?**

the Git pre-commit hook intercepts a Git event right before a commit is created, while PreToolUse intercepts an AI tool action before the model is allowed to execute a command or use a tool. Both are safety controls that run before something risky happens, both enforce rules automatically, and both are meant to stop unsafe actions early instead of fixing damage afterward.

---

# Task 3 — Prove the Hook Blocks the Risky Commit

## Goal

Attempt to commit the staged file from Task 1 and show the hook rejecting it.

### Evidence

#### Screenshot 4 — Terminal showing `git commit` rejected with the hook's "BLOCKED" message naming the exact file

![Terminal showing `git commit` rejected](screenshots/Wk-04-Ass-6-scrn-5.png)

---

### Notes

**1. Which line in `hooks/pre-commit` matched your fake key, and why did it match?**

The hook matched the line AWS_ACCESS_KEY_ID=AKIAABCDEFGHIJKLMNOP because the value begins with AKIA and has the right uppercase alphanumeric pattern after it, which is exactly what the rule AKIA[0-9A-Z]{16} is looking for. That is why the commit was blocked with BLOCKED: possible secret in scripts/notify.sh

---

**2. Could this hook have caught a poorly-named variable that stores a secret without the `AKIA` prefix? What does that tell you about the limits of a fixed rule like this?**

No, this hook would not reliably catch a secret stored in a poorly named variable if the value does not match the pattern, such as a secret hidden without the AKIA prefix. That shows the limit of a fixed-rule check: it is fast and deterministic, but it only catches the patterns it was explicitly written to detect, so it can miss secrets that are named differently or formatted in a more subtle way.

---

# Task 4 — Build the `/pr-ready` Skill

## Goal

Create a manually invoked Claude Code skill that reads your staged changes and produces a PR-readiness report and a draft PR description — without writing, committing, or pushing anything itself.

### Evidence

#### Screenshot 5 — `SKILL.md` frontmatter showing `allowed-tools: Bash, Read, Grep` (no `Write`) and `disable-model-invocation: true`

![`SKILL.md` frontmatter showing `allowed-tools](screenshots/Wk-04-Ass-6-scrn-6.png)

---

#### Screenshot 6 — `/pr-ready` output while the risky file is still staged, showing it flagged the secret and/or debug statement

![`/pr-ready` output while the risky file](screenshots/WK-04-Ass-6-scrn-7.png)

---

### Notes

**1. Why does `/pr-ready` have `Bash` and `Read` but not `Write`?**

/pr-ready has Bash and Read because it needs to inspect the staged diff and read files, but it does not need Write because it must not change anything in the repo. That keeps it safely read-only, so it can analyze and draft a PR description without editing, committing, or pushing.

---

**2. The pre-commit hook and `/pr-ready` both looked at the same staged diff. Did they flag the same things? What did one catch that the other didn't?**

The pre-commit hook and /pr-ready did not flag the exact same things. The hook blocked the commit for the fake AWS-style key and only cares about hardcoded secret-like patterns and oversized files, while /pr-ready flagged the secret, the debug echo statement, and also gave extra review comments like the placeholder/demo note and the untracked .claude/ and hooks/ folders.

---

# Task 5 — Fix the Issues and Re-Verify

## Goal

Remove the secret and debug statement, then prove both gates now pass clean.

### Evidence

#### Screenshot 7 — `git commit` succeeding after the fix (no BLOCKED message)

![`git commit` succeeding after the fix](screenshots/Wk-04-Ass-6-scrn-8.png)

---

#### Screenshot 8 — Second `/pr-ready` run showing a clean risk report and a drafted PR title + description

![Second `/pr-ready` run showing a clean risk](screenshots/Wk-04-Ass-6-scrn-9.png)

---

### Notes

**1. What exactly did you change to satisfy the pre-commit hook?**

To satisfy the pre-commit hook, I removed the fake AWS access key line and the debug echo line from scripts/notify.sh. In other words, I deleted both the secret-looking value and the statement that printed it, so the staged file no longer contained either a credential-shaped string or debug output.

---

# Task 6 — Push and Open a Pull Request Using the AI Draft

## Goal

Push your branch and open a real Pull Request, using `/pr-ready`'s drafted title and description as your starting point — read it critically and edit before you use it.

**Important:** Open this Pull Request with base repository set to **your own fork** — not the shared upstream `pravinmishraaws/devops-micro-internship-pravinmishra` repository. This assignment's hook and skill files are your own practice work, not a change meant for the shared class repo.

### Evidence

#### Screenshot 9 — Your Pull Request showing the base repository is your own fork, plus the title and description, with the `/pr-ready` draft visible for comparison (paste it in the PR conversation or your notes below)

![Your Pull Request](screenshots/Wk-04-Ass-6-scrn-10.png)

---

#### PR Link

https://github.com/pravinmishraaws/devops-micro-internship-interviews/pull/355

---

### Notes

**1. What, if anything, did you edit in the AI's drafted PR description before using it? Why?**

I edited the AI draft to make sure the PR title and description matched the actual changes I made and did not include anything inaccurate or unnecessary. I also removed or adjusted any wording that was too vague, because the final PR should reflect my own review, not the AI output exactly.

---

**2. If you had blindly copy-pasted the AI's draft without reading it, what could go wrong?**

If I had copy-pasted the draft without reading it, I could have submitted a PR with wrong details, missed context, or misleading information. That could confuse the reviewer, reduce trust in the PR, and even cause the wrong change to be approved or merged.

---

**3. Why does this PR need to target your own fork instead of the shared upstream repository?**

This PR needs to target my own fork because this assignment is a fork-based collaboration workflow, not a direct contribution to the shared upstream repository. My fork is where my branch and changes belong, and the upstream repo should only receive the PR through the normal review process if that is what the assignment specifically asks for.

---

# Task 7 — Map the Workflow to the Agentic Loop

## Goal

Explain this assignment's workflow using the same Gather → Analyze → Human Act → Verify structure from Week 3.

### Notes

**1. Which step(s) represent Gather?**

The Gather step happened when the tools collected the facts about the change, especially when the pre-commit hook checked the staged diff and when /pr-ready ran git diff --cached and git status to inspect what was staged. In this assignment, Gather means the system looked at the actual code and repository state before anyone acted on it.

---

**2. Which step(s) represent Analyze?**

The Analyze step happened when the pre-commit hook evaluated the staged diff against fixed rules, and when /pr-ready reviewed the same staged change for broader risks such as secret-like strings, debug statements, mixed concerns, or weak PR context. The hook did deterministic rule-checking, while the AI skill added judgment and explanation.

---

**3. Which step is Human Act, and why must a human — not Claude — run `git commit`, `git push`, and open the PR?**

The Human Act step was when you personally edited scripts/notify.sh, re-staged the file, ran git commit, ran git push, and opened the Pull Request yourself. A human must do those actions because commits, pushes, and PR creation change the shared project history, so the engineer must stay accountable instead of letting AI execute repository-changing actions automatically.

---

**4. Which step is Verify?**

The Verify step happened when you reran the checks after fixing the file: the commit succeeded without the hook blocking it, and /pr-ready produced a clean review afterward. Verify means checking again after the human action to confirm the risk was actually removed and the workflow now passes cleanly.


---

**5. In one or two sentences: why do you need *both* the fixed-rule pre-commit hook and the AI skill? Isn't one enough?**

You need both because the pre-commit hook is fast, strict, and reliable for hard rules like known secret patterns or oversized files, while the AI skill is useful for context and judgment calls such as debug leftovers, mixed-purpose diffs, or misleading PR descriptions. One alone is not enough: fixed rules miss context, and AI advice is helpful but not dependable enough to be the only gate.

---

# Task 8 — LinkedIn Post

## Goal

Publish a LinkedIn post summarizing what you built and what you learned about combining fixed-rule safety checks with AI-assisted review.

### Evidence

#### LinkedIn Post URL

https://www.linkedin.com/posts/evangeline-obeta-067089193_today-i-built-an-ai-assisted-git-safety-net-ugcPost-7486095332141928448-uQsO/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAC1lNQ8BKNctpF5K7KkXcW9PlnRd3JAwP3E

---

## Key Learnings

Add 3-5 bullet points on what you learned this week.

Fixed-rule checks are best for hard safety limits, like blocking secret-like strings and oversized files before a commit lands.

AI review is best for context, like spotting debug leftovers, mixed concerns, or PR descriptions that do not match the diff.

Humans must stay in control of Git actions, because committing, pushing, and opening PRs are accountable changes to the shared codebase.

Good DevOps safety is layered: collect facts, let AI analyze, let a human act, then verify again automatically.
---

# Submission Instructions

- Ensure `hooks/pre-commit` and `.claude/skills/pr-ready/SKILL.md` are committed to your GitHub repository
- Add all required screenshots to your submission
- All written answers must be in your own words
- Do not use a real secret or credential anywhere in your submission — the fake key in Task 1 is intentional and must stay clearly fake
- Open your Pull Request against your own fork, not the shared upstream repository
- Push your final changes to your forked repository
- Include your PR link and LinkedIn post URL

---

## GitHub Repository URL

https://github.com/EAgeless/devops-micro-internship-interviews.git



---

# Completion Checklist

- [ ] Branch `feature/ai-pr-ready` created with a staged file containing a fake secret and a debug statement
- [ ] `hooks/pre-commit` created and tracked in the repo (not only in `.git/hooks/`)
- [ ] `core.hooksPath` configured to point at `hooks/`
- [ ] Pre-commit hook shown blocking the risky commit
- [ ] `.claude/skills/pr-ready/SKILL.md` created with correct `allowed-tools` (no `Write`) and `disable-model-invocation: true`
- [ ] `/pr-ready` run against the risky diff and shown flagging issues
- [ ] Risky file fixed; `git commit` succeeds cleanly
- [ ] `/pr-ready` re-run showing a clean report and drafted PR title/description
- [ ] Pull Request opened using the AI draft as a starting point, with your own fork as the base repository (not upstream), PR link included
- [ ] Agentic Loop mapping (Task 7) completed in your own words
- [ ] LinkedIn post published and URL submitted
- [ ] All required screenshots added
- [ ] GitHub repository URL provided

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
