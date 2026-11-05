# Assignment 5 — AI-Assisted Azure DevOps Dual-Pipeline Failure Triage

Part of the DevOps Micro Internship (DMI) — Agentic AI Track

---

## Student Information

**Full Name:** Evangeline Obeta

**GitHub Repository or Fork URL:** https://github.com/EAgeless/theepicbook.git
                                https://github.com/EAgeless/infra-epicbook.git

**Public LinkedIn Post URL:** https://lnkd.in/p/dHbXks7a

---

## Purpose

In this assignment, I configured an AI-assisted, read-only failure-triage workflow for the EpicBook Infrastructure and Application Pipelines. The workflow uses Bash to gather Azure DevOps pipeline evidence and Claude Code to analyze the evidence and recommend a recovery action while keeping all changes under human control.

---

# Task 0 — Verify Tools, Authentication, and Pipeline Details

## Goal

Verify the required tools, Azure DevOps authentication, organization and project details, and numeric pipeline IDs.

No screenshot is required for this task.

---

# Task 1 — Capture the Healthy Baseline and Prepare the Supplied Files

## Goal

Confirm that both EpicBook pipelines are healthy and place the supplied assignment files in the correct repository locations.

## Evidence

### Screenshot 1 — Healthy Baseline for Both Pipelines

Terminal output showing the latest completed Infrastructure and Application Pipeline runs with successful results.

![Healthy Baseline for Both Pipelines](screenshots/Wk-10-Ass-5-scrn-1.png)

## Notes

### 1. What proves that both pipelines were healthy before the drill?

Before introducing the controlled failure, I checked the latest completed runs of both EpicBook pipelines in Azure DevOps. The Infrastructure Pipeline, epicbook-infra-pipeline (pipeline ID 4), had run ID 16, and the Application Pipeline, EAgeless.theepicbook (pipeline ID 5), had run ID 25. Both runs were on main and showed status completed with result succeeded.

The initial triage report also showed both pipelines as successful, with Overall Status: HEALTHY and a captured script exit code of 0. I preserved screenshots of these results as evidence of the healthy baseline.

### 2. Why is a healthy baseline necessary before introducing a controlled failure?

A healthy baseline establishes that both pipelines were working before I introduced the deliberate failure. This helps distinguish the controlled incident from an existing pipeline, configuration, or authentication problem.

It also provides a clear comparison for recovery verification. After manually correcting the deliberate failure, I could compare the new results with the baseline and confirm that both pipelines had returned to successful states. Without a baseline, I could not confidently show that the drill introduced a new failure or that the correction restored the expected pipeline health.

---

# Task 2 — Configure and Review the Supplied CLAUDE.md

## Goal

Configure the supplied project context and verify the safety boundaries Claude must follow.

## Evidence

### Screenshot 2 — CLAUDE.md Context and Safety Rules

`CLAUDE.md` open in the editor with the Project Overview, Incident Workflow, Safety Rules, and Output Rules visible.

![CLAUDE.md Context and Safety Rules](screenshots/Wk-10-Ass-5-scrn-2.png)

![CLAUDE.md Context and Safety Rules](screenshots/Wk-10-Ass-5-scrn-2b.png)

## Notes

### 1. Why does Claude need project-specific operational context?

Claude needs project-specific context to understand what each EpicBook pipeline does and how to interpret its results. The Infrastructure Pipeline provisions Azure resources using Terraform, while the Application Pipeline uses Ansible to configure the server and deploy EpicBook.

My CLAUDE.md explains these responsibilities, identifies the report and sanitized logs as the primary evidence sources, and defines Claude’s role as a diagnostic assistant. This helps Claude provide relevant recommendations without assuming that it has permission to modify the environment or treating a successful pipeline run as proof that the deployed application is working.

### 2. Which rules keep the human responsible for the recovery action?

The Gather → Analyze → Human Act → Verify workflow explicitly separates diagnosis from recovery. Claude may explain the evidence and recommend a correction, but the engineer must review and apply it manually.

The safety rules prohibit Claude from editing project files, pushing changes, running Terraform or Ansible, modifying Azure resources, or triggering, retrying, canceling, deleting, or approving pipeline runs. These restrictions kept me responsible for reviewing the recommendation, removing the deliberate assertion, committing and pushing the correction, and manually running the corrected Application Pipeline. Claude then analyzed the new evidence to verify recovery.

### 3. Which rules protect pipeline credentials and application secrets?

The safety rules prohibit Claude from inspecting, printing, storing, exposing, or changing tokens, passwords, SSH private keys, authorization headers, database credentials, and Service Connection credentials. Claude must never ask me to paste a secret into its session. It is also prohibited from changing Service Connections, Secure Files, Variable Groups, or pipeline permissions.

In my adapted workflow, the reviewed Bash script receives its read-only PAT through a protected shell environment variable rather than a hardcoded project value. The script may use that credential internally for authentication, but Claude must not inspect or display it. Shell tracing is prohibited, retrieved logs are sanitized before persistent output, and I must manually inspect reports and screenshots before submission because sanitization is not guaranteed to catch every sensitive value.

---

# Task 3 — Configure and Validate the Supplied Pipeline Triage Script

## Goal

Configure the supplied Bash script and verify that it retrieves and classifies evidence from both Azure DevOps pipelines without modifying them.

## Evidence

### Screenshot 3 — Pipeline Triage Script Configuration

Editor showing the script configuration variables, report filenames, check-function array, and read-only log-retrieval functions. Ensure that no token is visible.

![Pipeline Triage Script Configuration](screenshots/Wk-10-Ass-5-Scrn-3.png)

![Pipeline Triage Script Configuration](screenshots/wK-10-Ass-5-Scrn-3b.png)

---

### Screenshot 4 — Script Validation

Terminal showing successful Bash syntax validation and executable file permission.

![Script Validation](screenshots/Wk-10-Ass-5-scrn-4.png)

## Notes

### 1. Why are pipeline metadata and step console logs handled separately?

Pipeline metadata describes a run, including its run ID, branch, status, result, and completion time. This establishes which run the script is inspecting and whether it completed successfully, failed, or remains unfinished.

Step console logs contain the detailed output produced by individual pipeline tasks. A failed result confirms that something went wrong, but the logs provide evidence of what happened. My script retrieves metadata first and downloads console logs when the selected run has failed, so it can classify the failure using actual task output rather than the result alone.

### 2. How does the script obtain the actual console logs?

The script uses the Azure DevOps Build Logs REST API. It first sends a read-only request to retrieve the list of logs associated with the failed build. It then loops through the returned log IDs and downloads each individual log as text.

In my adapted implementation, authentication uses a Build Read PAT supplied through the AZURE_DEVOPS_EXT_PAT environment variable. The script prepares the authentication header internally without printing the credential. It checks the HTTP responses, validates the log-list JSON, and sanitizes downloaded content before saving it to the appropriate pipeline log file: infra-last-run.log or app-last-run.log.

### 3. How does the check-function array control the classification loop?

The supplied version uses a category-pattern array, named categories, rather than an array of check-function names. Each entry contains a failure-category name followed by regular-expression patterns associated with that category.

The run_category_checks() function loops through the array, separates each category name from its patterns, and searches the retrieved log using case-insensitive matching. When a match is found, it records the category and the first matching sanitized evidence line. The loop continues through the remaining categories, so one failed run can produce multiple findings. The counters therefore represent findings, not the number of affected pipelines.

### 4. What prevents a failed but unmatched run from being reported as healthy?

The script treats the Azure DevOps run result separately from its pattern matches. If Azure DevOps reports a failed run but no known category matches the retrieved logs, the script records an Unclassified Pipeline Failure and requires manual log review. It does not interpret the absence of a familiar error pattern as success.

If log retrieval itself fails, the script records both the confirmed pipeline failure and a tool error. It states that the root cause is unconfirmed and returns exit code 3, rather than reporting a healthy result.

### 5. Why are different exit codes useful to another automation tool?

Different exit codes allow another tool to distinguish the outcome without relying only on interpreting the report’s wording:

Exit code	Meaning
0	        Both selected pipeline runs completed successfully
1	        A warning or incomplete state requires attention
2	        At least one pipeline failure was detected
3	        A configuration, authentication, API, or processing error prevented reliable triage
These distinctions help automation respond appropriately. For example, an unfinished run should be checked again later, while a failed run needs diagnosis and a log-retrieval error needs investigation of the triage workflow. They also prevent an authentication or API problem from being mistaken for pipeline health.

---

# Task 4 — Run and Understand the Healthy-State Report

## Goal

Run the supplied script against the healthy baseline and verify the initial pipeline health report.

## Evidence

### Screenshot 5 — Healthy Pipeline Report

Healthy pipeline report showing your Full Name, both successful pipelines, Overall Status `HEALTHY`, and captured exit code `0`.

![Healthy Pipeline Report](screenshots/Wk-10-Ass-5-scrn-5.png)

## Notes

### 1. What evidence proves that both pipelines are healthy?

The baseline health report showed that the Infrastructure Pipeline (ID 4, run 16) and the Application Pipeline (ID 5, run 25) both had status completed and result succeeded. The report included my full name, a timestamp, each run ID, branch, and completion time.

Its summary showed two passing findings, no warnings, failures, or errors, and Overall Status: HEALTHY. I also captured the script’s exit code immediately after execution and confirmed it was 0. Together, these results established a healthy baseline for the latest pipeline runs. They did not, by themselves, prove that the deployed application was functioning correctly.

### 2. Why must the baseline exit code be verified before the incident drill?

The baseline exit code must be verified to confirm that the triage script completed successfully and that its execution result agrees with the health report. Checking for exit code 0 helps ensure that an existing configuration, authentication, API, or processing error is not overlooked before introducing the controlled failure.

It also provides a reliable comparison for the drill: the workflow starts with a healthy result, detects the deliberate pipeline failure, and returns to exit code 0 after the engineer applies the correction and verifies recovery.

---

# Task 5 — Configure and Test the Supplied /pipeline-triage Skill

## Goal

Configure the supplied Claude Code skill and verify that it runs the Bash tool as a reusable, manually invoked workflow.

## Evidence

### Screenshot 6 — Pipeline-Triage Skill Definition

`SKILL.md` showing the frontmatter, manual-invocation setting, narrowly scoped tools, safety rules, and required output structure.

![Pipeline-Triage Skill Definition](screenshots/Wk-10-Ass-5-scrn-6.png)

![Pipeline-Triage Skill Definition](screenshots/Wk-10-Ass-5-scrn-6b.png)

---

### Screenshot 7 — Healthy Skill Result

Healthy `/pipeline-triage` result showing that both pipelines are healthy and no fix is required.

![Healthy Skill Result](screenshots/Wk-10-Ass-5-scrn-7.png)

## Notes

### 1. Why is `disable-model-invocation: true` appropriate for this skill?

disable-model-invocation: true is appropriate because this skill is intended to be run deliberately by the engineer, not automatically invoked by Claude whenever it thinks it is relevant. The skill performs a sensitive operational task: it queries Azure DevOps, downloads pipeline logs, sanitizes evidence, and produces a health or incident report.

Requiring explicit human invocation keeps the engineer in control of when the triage workflow runs. It also prevents Claude from deciding on its own to execute the Bash script or treat the skill as a general-purpose way to interact with the pipeline environment.

### 2. Why should the skill avoid broad Bash approval?

Broad Bash approval would allow Claude to run arbitrary shell commands, which could expose secrets, alter files, trigger pipelines, or modify Azure resources. That would conflict with the skill’s safety model, which requires Claude to analyze evidence rather than perform recovery actions.

The skill should instead request only narrowly scoped, read-only commands needed to execute the supplied triage script and inspect its generated report. This limits the blast radius of a mistake or prompt-injection attempt while still allowing the required diagnostic workflow.

### 3. What work is performed by Bash, and what work is performed by Claude?

Bash performs the deterministic operational work. It authenticates to Azure DevOps using the protected PAT environment variable, retrieves run metadata, downloads and sanitizes console logs, runs the classification checks, writes the report, and returns an exit code that reflects the outcome.

Claude performs the reasoning and communication work. It reads the sanitized report and log evidence, explains what the findings mean, identifies likely causes, recommends a human-approved correction, and verifies recovery after the engineer applies it. Claude does not run Terraform or Ansible, edit project files, or trigger pipeline runs.

### 4. Why are permission rules required in addition to written safety instructions?

Written safety instructions tell Claude what it should and should not do, but permission rules enforce those limits at the tool level. Instructions can be misunderstood, forgotten, or bypassed through an unexpected request, whereas permission rules can block unauthorized Bash commands before they execute.

Together, the two layers provide defense in depth. The CLAUDE.md and skill instructions define the intended workflow, while narrowly scoped permissions ensure that Claude can execute only the approved triage command and cannot obtain broad control over the shell, repository, or Azure DevOps environment.

---

# Task 6 — Introduce a Safe Failure in the Application Pipeline

## Goal

Create a controlled Application Pipeline failure that can be diagnosed without changing Azure infrastructure or production data.

## Evidence

### Screenshot 8 — Controlled Application Pipeline Failure

Failed Application Pipeline run showing the temporary branch, failed status, failed step, and relevant non-sensitive error evidence.

![Controlled Application Pipeline Failure](screenshots/Wk-10-Ass-5-scrn-8.png)

## Notes

### 1. What exact failure did you introduce?

I introduced a deliberate assertion failure in the EpicBook Application Pipeline on a feature branch. The change added a failing check to the pipeline’s validation step, causing the Application Pipeline run to fail while leaving the Infrastructure Pipeline unchanged.

### 2. Which category should detect it?

The Assertion Failure category should detect it. The triage script searches the Application Pipeline console logs for assertion-related patterns and records the matching category and sanitized evidence line in the incident report.

### 3. Why is the failure safe and easily reversible?

The failure is safe because it affects only a controlled validation assertion in a feature-branch pipeline run. It does not delete Azure resources, change infrastructure configuration, expose credentials, or alter the deployed EpicBook application.

It is easily reversible because the fix is a small, localized code change: remove the deliberate assertion, commit the correction, and push it to the same feature branch. The next Application Pipeline run can then return to a successful result.

### 4. How did you prevent the deliberate failure from reaching `main` or changing the deployed application?

I made the change on a dedicated feature branch rather than main. I committed and pushed only that branch, so the deliberate failure remained isolated from the protected main branch.

The failing assertion was part of pipeline validation, not the application source code or deployment configuration. Therefore, even while the pipeline failed, it did not introduce a runtime defect into the deployed EpicBook application.

---

# Task 7 — Diagnose and Save the Incident Evidence

## Goal

Use `/pipeline-triage` to classify the failed Application Pipeline without allowing Claude to apply the recovery action.

## Evidence

### Screenshot 9 — Failed-State Diagnosis and Incident Report

`/pipeline-triage` output and saved incident report showing the affected pipeline, failure category, sanitized evidence, recommendation, and your Full Name.

![Failed-State Diagnosis and Incident Report](screenshots/Wk-10-Ass-5-scrn-9.png)

## Notes

### 1. Which failure category was identified?

The triage report identified an Assertion Failure in the Application Pipeline.

### 2. What exact evidence supported the diagnosis?

The report showed that the latest Application Pipeline run had status completed and result failed. The retrieved and sanitized console log contained an assertion-related error message, and the report recorded Assertion Failure as the matched category along with the relevant evidence line.

The report also showed that the Infrastructure Pipeline remained successful, which helped confirm that the incident was isolated to the Application Pipeline rather than caused by an infrastructure or provisioning problem.

### 3. Did Claude apply the fix or rerun the pipeline? Why is that important?

No. Claude only analyzed the evidence and recommended the correction; I manually removed the deliberate assertion, committed and pushed the change, and reran the Application Pipeline.

This is important because it keeps the human responsible for every recovery action. It prevents Claude from changing source code, pushing commits, approving runs, or triggering deployments without explicit engineer review and action.

### 4. Which part represents Gather, and which part represents Analyze?

Gather is represented by the Bash triage script retrieving the latest pipeline metadata and downloading the sanitized Application Pipeline console logs. It collects the factual evidence needed for diagnosis.

Analyze is represented by Claude reviewing the generated incident report and explaining the failure category, likely cause, and recommended correction. Claude interprets the evidence but does not perform the recovery action.

---

# Task 8 — Apply the Human-Reviewed Fix and Verify Recovery

## Goal

Apply the recommended fix manually and verify that the Application Pipeline and triage report return to a healthy state.

## Evidence

### Screenshot 10 — Corrected Application Pipeline Run

Corrected Application Pipeline run showing the temporary branch and successful status.

![Corrected Application Pipeline Run](screenshots/Wk-10-Ass-5-scrn-10.png)

---

### Screenshot 11 — Recovery Triage Result

Recovery `/pipeline-triage` output showing Overall Status `HEALTHY`, exit code `0`, your Full Name, and both saved report filenames.

![Recovery Triage Result](screenshots/Wk-10-Ass-5-scrn-11.png)

![Recovery Triage Result](screenshots/Wk-10-Ass-5-scrn-11b.png)

## Notes

### 1. What exact fix did you apply?

I removed the deliberate assertion from the Application Pipeline validation step on the feature branch. I then committed the correction with a clear message and pushed it to the same feature branch.

### 2. Did the fix match Claude’s recommendation? Explain briefly.

Yes. Claude identified an assertion failure and recommended removing the deliberately added assertion before rerunning the Application Pipeline. I reviewed that recommendation, confirmed it matched the change I had introduced, and applied it manually.

### 3. What evidence proves that the pipeline recovered?

The corrected Application Pipeline run completed with result succeeded. The second triage report showed both the Infrastructure Pipeline and Application Pipeline as healthy, with no warnings, failures, or errors, and returned exit code 0.

### 4. Why is a second triage run required after the pipeline becomes green?

The second triage run verifies recovery using fresh evidence rather than assuming that a green pipeline run is sufficient. It confirms that the latest run metadata and logs are healthy, that the earlier failure no longer appears, and that the workflow returns to its baseline exit code 0.

### 5. What risk would be created if Claude could automatically edit, push, approve, and rerun the pipeline?

Claude could modify source code or pipeline configuration, push unreviewed changes, approve or trigger deployments, and potentially affect the deployed application or Azure environment. An incorrect diagnosis, prompt-injection attempt, or misunderstood log message could therefore become an automated production change.

Allowing those actions would remove the human checkpoint from the recovery workflow. Keeping edit, push, approval, and rerun actions manual ensures that an engineer reviews the evidence and remains accountable for changes to the repository and pipeline.

---

# LinkedIn Post — Mandatory

## LinkedIn Post URL

https://lnkd.in/p/dHbXks7a

## Evidence

### Screenshot 12 — Published LinkedIn Post

Published LinkedIn post showing its text and at least one image or link.

![LinkedIn Post](screenshots/Wk-10-Ass-4-Lnked.png)

---

# Required Repository Files

Confirm that the following files are available in your repository:

* [ ] `CLAUDE.md`
* [ ] `pipeline-triage.sh`
* [ ] `.claude/skills/pipeline-triage/SKILL.md`
* [ ] `reports/incident-failure-report.txt`
* [ ] `reports/recovery-report.txt`

---

# Submission Instructions

* Complete all tasks in sequence.
* Include all 12 required screenshots.
* Answer every Notes question in your own words.
* Include your GitHub repository or fork URL.
* Include your public LinkedIn post URL.
* Ensure your Full Name appears in the required reports.
* Do not include raw logs containing sensitive information.
* Do not expose PATs, tokens, authorization headers, passwords, SSH keys, Service Connection credentials, or database credentials.

---

# Completion Checklist

* [ ] Both Azure DevOps pipelines were healthy before the drill.
* [ ] The supplied files were copied to the correct repository locations.
* [ ] Only the required student-specific placeholders were updated.
* [ ] `CLAUDE.md` contains the required context and safety rules.
* [ ] `pipeline-triage.sh` passed Bash syntax validation.
* [ ] The script has executable permission.
* [ ] The script uses read-only Azure DevOps operations.
* [ ] The script retrieves pipeline metadata and console logs.
* [ ] No token or password is stored in the script.
* [ ] The healthy baseline reported `HEALTHY` with exit code `0`.
* [ ] `/pipeline-triage` was invoked manually.
* [ ] The skill does not have broad Bash approval.
* [ ] The controlled failure affected only the Application Pipeline.
* [ ] The failure occurred before deployment changes were applied.
* [ ] The deliberate failure was not merged into `main`.
* [ ] The failed-state report was saved before applying the fix.
* [ ] Claude diagnosed the failure but did not apply the fix.
* [ ] The fix was reviewed and applied manually.
* [ ] The corrected Application Pipeline completed successfully.
* [ ] The recovery triage reported `HEALTHY` with exit code `0`.
* [ ] `incident-failure-report.txt` exists.
* [ ] `recovery-report.txt` exists.
* [ ] All Notes questions have been answered.
* [ ] All 12 screenshots have been added.
* [ ] The GitHub repository or fork URL has been included.
* [ ] The LinkedIn post is public.
* [ ] The LinkedIn post URL has been included.
* [ ] No sensitive information is exposed.

---

# Final Submission

**Full Name:** Evangeline Obeta

**GitHub Repository or Fork URL:** https://github.com/EAgeless/infra-epicbook.git

**LinkedIn Post URL:** https://lnkd.in/p/dHbXks7a

---

*This submission is part of the DevOps Micro Internship (DMI) — Agentic AI Track.*
