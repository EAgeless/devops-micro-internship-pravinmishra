# Assignment 6 — AI-Assisted Terraform Drift and Policy Review

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Student Details

**Full Name:** Evangeline Obeta  
**GitHub Repository/Folder URL:** https://github.com/EAgeless/devops-micro-internship-pravinmishra.git

---

## Purpose

Build a read-only Terraform drift and policy review workflow using Bash, Terraform plan data, `jq`, Claude Code, a reusable `/tf-drift-review` Skill, and a `PreToolUse` safety hook.

The workflow must follow this pattern:

```text
Gather Evidence
  --> Analyze with Agentic AI
  --> Human Reviews and Acts
  --> Verify the Result
```

The `/tf-drift-review` Skill and `tf-drift-check.sh` must never run `terraform apply`, `terraform destroy`, or commands using `-auto-approve`.

---

# Task 1 — Confirm the Clean Baseline and Create the Workspace

## Goal

Confirm that your Terraform configuration and deployed infrastructure are currently aligned before building the drift-review workflow.

## Evidence

### Screenshot 1 — Clean Terraform Plan

Screenshot of `terraform plan` showing no pending changes.

![Terraform Plan](screenshots/Wk-08-Ass-4-scrn-20.png)

---

### Screenshot 2 — Assignment Workspace

Screenshot of the folder structure showing `AI Assignment/`, `reports/`, and the Terraform project.



## Questions

### 1. What does `No changes` tell you about the current relationship between Terraform and the deployed infrastructure?

“No changes” means that the deployed infrastructure matches the desired state defined in the Terraform configuration and state file. Terraform did not detect any updates, additions, deletions, or replacements that needed to be applied.

### 2. Why is a clean baseline important before introducing a test change?

A clean baseline ensures that any later change detected by Terraform can be attributed to the controlled test change. Without a clean starting point, it would be difficult to distinguish an existing problem from the difference intentionally introduced for the assignment.

---

# Task 2 — Create Project Context and Safety Rules in `CLAUDE.md`

## Goal

Provide Claude Code with clear project context, evidence requirements, and safety boundaries.

## Evidence

### Screenshot 3 — Project Context and Safety Rules

Screenshot of `CLAUDE.md` open in VS Code showing the Project Overview, Review Workflow, Safety Rules, and Output Rules.



## Questions

### 1. Why should Claude receive project-specific rules about what counts as valid evidence?

Project-specific rules tell Claude which files and outputs it must trust during the review. In this assignment, the Terraform plan, plan JSON, and generated drift report are the approved evidence sources. This reduces the risk of Claude making unsupported conclusions from assumptions or incomplete information.

### 2. Why must the human remain responsible for running `terraform apply`?

Terraform apply can create, modify, replace, or delete real cloud resources. A human must review the proposed actions, confirm that they are expected, consider their business and security impact, and then approve the change deliberately.

### 3. Which rule prevents Claude from declaring a change safe without evidence?

The rule is:

Do not claim a change is safe unless the available evidence supports that conclusion.

This forces Claude to base its recommendation on the generated report and Terraform plan evidence.

---

# Task 3 — Build the Terraform Drift and Policy Check Script

## Goal

Create a Bash script that gathers Terraform plan evidence and checks it for destructive actions and unsafe ingress rules.

## Evidence

### Screenshot 4 — Script Variables and Checks Array

Screenshot of the top section of `tf-drift-check.sh` showing the variables and `checks` array.



---

### Screenshot 5 — Destructive-Action and Open-Ingress Checks

Screenshot showing `check_destructive_actions` and `check_open_ingress`, including the `jq` checks.



---

### Screenshot 6 — Script Validation and Permissions

Screenshot showing successful `bash -n` and `ls -l` output.



## Questions

### 1. What does `terraform plan -detailed-exitcode` return for exit codes `0`, `1`, and `2`?

Exit code	Meaning
0	The plan completed successfully and no changes are pending.
1	Terraform encountered an error while creating the plan.
2	The plan completed successfully, but changes are pending and require review.
Terraform documents exit code 2 as a successful plan with a non-empty difference. It also represents replacement using a destroy-and-create action, displayed as -/+ in normal plan output

### 2. Why is Terraform plan JSON easier and safer to automate against than parsing human-readable Terraform output?

Terraform plan JSON has a structured and predictable format. The script can query fields such as resource_changes, change.actions, and change.after with jq instead of relying on text formatting, symbols, spacing, or wording that may change between Terraform versions.

### 3. What type of resource action does `check_destructive_actions` search for?

It searches for a delete action inside the Terraform plan JSON.

### 4. Why does finding a `delete` action also help detect replacements?

Terraform represents a replacement as both a delete and a create action. Therefore, a resource with actions such as ["delete","create"] is being replaced. Searching for delete identifies both direct deletions and replacements

### 5. Why must this script never run `terraform apply`?

The script is an evidence-gathering and policy-review tool. Running terraform apply would change real infrastructure without completing the required human review and approval process. Keeping the script read-only preserves the separation between gathering evidence and taking action.

---

# Task 4 — Run the Script Against the Clean Baseline

## Goal

Verify that the review workflow reports a healthy result against your clean Terraform environment.

## Evidence

### Screenshot 7 — Healthy Baseline Report

Screenshot of the drift script output showing your full name and a `HEALTHY` result.



---

### Screenshot 8 — Baseline Script Exit Code

Screenshot showing the captured script exit code `0`.



## Questions

### 1. What is the Overall Status of your baseline?

HEALTHY

### 2. Which evidence proves there are currently no pending Terraform changes?

terraform plan exit code 0 — no pending changes
Overall Status: HEALTHY
Script Exit Code: 0
The output will: No changes. Your infrastructure matches the configuration.

### 3. Was `reports/tfplan.json` created? Explain why or why not.

No. The script creates the JSON plan only when Terraform returns exit code 2, meaning that changes are pending. Since the clean baseline returned exit code 0, there was no pending plan to convert into JSON.


---

# Task 5 — Create and Run the `/tf-drift-review` Claude Code Skill

## Goal

Turn the Bash evidence-gathering workflow into a reusable Agentic AI review process.

## Evidence

### Screenshot 9 — `/tf-drift-review` Skill Configuration

Screenshot of `SKILL.md` showing the frontmatter, allowed tools, and safety rules.



---

### Screenshot 10 — Clean Agentic AI Review

Screenshot of `/tf-drift-review` showing the clean `HEALTHY` result.



## Questions

### 1. Why does this Skill have `Bash`, `Read`, and `Grep`, but not `Write`?

Bash is required to run the read-only drift-check script. Read and Grep are required to inspect the report, Terraform plan JSON, and project rules. Write is intentionally excluded so the Skill cannot modify Terraform files, reports, or other project files during the review.

### 2. Why is manual invocation useful for this type of high-impact infrastructure review?

Manual invocation ensures that the review starts only when the engineer intentionally requests it. This prevents the workflow from running unexpectedly during unrelated tasks and makes the review a deliberate checkpoint before infrastructure changes.

### 3. Which part of the workflow is deterministic Bash automation?

The Bash script performs the deterministic work:

Runs terraform plan -detailed-exitcode.

Saves the binary plan.

Converts the plan to JSON when changes exist.

Checks for delete and replacement actions.

Checks for open ingress through 0.0.0.0/0 or *.

Generates the structured report.

Returns a predictable script exit code.

### 4. Which part requires Claude's reasoning?

Claude interprets the evidence in plain language. It identifies the affected resources, explains what Terraform intends to do, describes the risk, determines whether an apply appears safe based on the evidence, and recommends the next human action.

### 5. Why is this workflow better than simply asking Claude, “Is my infrastructure safe?”

A general question gives Claude little verifiable evidence and may lead to assumptions. This workflow provides a repeatable Terraform plan, structured JSON, deterministic policy checks, a written report, and a human approval step. Claude analyzes facts collected by the script instead of making an unsupported safety judgment.

---

# Task 6 — Introduce a Controlled Difference and Detect It

## Goal

Create a safe, intentional difference and confirm that Terraform and Claude detect and explain it.

## Evidence

### Screenshot 11 — Controlled Difference

Sreenshot of the controlled change you introduced, with sensitive details hidden.



---

### Screenshot 12 — Detected Difference and Risk Assessment

Sreenshot of `/tf-drift-review` showing the detected difference and risk assessment.



---

### Screenshot 13 — Detected Drift Report

Sreenshot of `drift-detected-report.txt` showing your full name and the `WARN` or `FAIL` result.



## Questions

### 1. What change did you introduce?

I introduced a controlled, harmless Terraform configuration change to a Terraform-managed Book Review App resource. The change was selected so that it did not expose a sensitive service publicly or create an uncontrolled security risk.

### 2. Was it true infrastructure drift or a Terraform configuration change?

It was a Terraform configuration change, not true infrastructure drift. I changed the desired Terraform configuration while leaving the deployed infrastructure unchanged.

True infrastructure drift would have occurred if someone had manually changed the cloud resource without updating the Terraform files.


### 3. What Terraform plan evidence proves that a change is pending?

terraform plan exit code 2
The plan also displayed the affected resource and the planned action. Exit code 2 confirms that Terraform completed successfully and found a non-empty difference.

### 4. Was the action an update, deletion, replacement, or security-rule change?

The controlled change was an update to the selected Terraform-managed resource. It did not introduce a deletion, replacement, or unsafe ingress rule.

### 5. What did Claude recommend?

Claude identified the affected resource, explained the proposed update, and recommended human review before applying it. Because the change was non-destructive and no unsafe ingress rule was detected, the change could be considered for application after confirming that it was intentional.

### 6. Why should you review the recommendation before taking action?

Claude’s recommendation is an analysis, not authorization. A human must confirm that the resource, values, environment, timing, and expected impact are correct. The human also needs to check for risks that may not be represented in the available plan evidence.

---

# Task 7 — Add a `PreToolUse` Hook to Block Unsafe Apply Attempts

## Goal

Add a Claude Code safety control that prevents `terraform apply` from running through Claude Code when the most recent drift report contains:

```text
Overall Status: FAIL
```

## Evidence

### Screenshot 14 — `PreToolUse` Safety Hook

Sreenshot of `.claude/settings.json` showing the `PreToolUse` safety hook.



---

### Screenshot 15 — Blocked Apply Attempt

Sreenshot of Claude Code showing the blocked `terraform apply` attempt.



## Questions

### 1. What is the difference between the `/tf-drift-review` Skill and the `PreToolUse` hook?

The Skill is a reusable analysis workflow. It runs the Bash review, reads the evidence, and explains the findings. The hook is an enforcement mechanism that checks a proposed Bash command before execution and can block it.

### 2. Which component performs analysis?

Claude Code, using the /tf-drift-review Skill, performs the analysis and risk explanation.

### 3. Which component enforces the safety gate?

The deterministic PreToolUse hook enforces the safety gate. It blocks a terraform apply request when the latest report contains:

text
Overall Status: FAIL
Claude Code hooks run before a tool call and can deny the call by setting a blocking decision.

### 4. Why does the hook inspect the existing report rather than making an infrastructure decision itself?

The hook has a narrow responsibility: enforce a predetermined safety rule. The report already contains the results of the Terraform plan and policy checks. By inspecting that report, the hook can block unsafe behavior without trying to understand the entire infrastructure plan or replace human judgment.

### 5. Why is a deterministic guard useful for high-impact commands?

A deterministic guard applies the same rule every time. It does not depend on Claude’s interpretation, memory, or wording, so it provides a consistent barrier against accidentally executing a high-impact command. Claude Code guidance recommends hooks for actions that must happen every time with no exceptions.

---

# Task 8 — Resolve the Difference and Verify the Final State

## Goal

Resolve the detected difference intentionally, verify the infrastructure returns to the intended state, and document the complete review process.

## Evidence

### Screenshot 16 — Human-Reviewed Resolution

Sreenshot of the human-reviewed resolution or `terraform apply` output where applicable.



---

### Screenshot 17 — Final Healthy Review

Sreenshot of the final `/tf-drift-review` showing `HEALTHY`.



---

### Screenshot 18 — Saved Reports

Sreenshot of `ls -lah reports` showing both:

- `drift-detected-report.txt`
- `resolved-report.txt`



---

### Screenshot 19 — Drift Review Summary

Sreenshot of `drift-review-summary.md` showing all required sections and your full name.



## Terraform Drift Review Summary

### 1. Change Introduced

Explain the controlled change you introduced.

State whether it was:

- True infrastructure drift, or
- A Terraform configuration change

I introduced a controlled change to one of the Terraform-managed resources in the Book Review App project. The change was harmless and intentional, and it was created to test whether the Terraform drift-review workflow could detect a difference before any infrastructure action was taken.

This was a Terraform configuration change, not true infrastructure drift. I modified the desired configuration in a Terraform file while the deployed infrastructure remained unchanged. True infrastructure drift would have occurred if the resource had been changed manually in the cloud console without updating the Terraform configuration.

### 2. Evidence Collected

Describe the Terraform plan evidence and affected resource.
I ran the read-only drift-check script, which executed:

bash
terraform plan -detailed-exitcode -out=tfplan.out
Terraform returned detailed exit code 2, confirming that the plan completed successfully and that changes were pending. The affected Terraform-managed resource appeared in the plan with the proposed update.

The workflow also generated a machine-readable plan file, reports/tfplan.json, and a review report at:

reports/tf-drift-report.txt

The report recorded the Terraform exit code, the affected resource, the planned action, and the results of the destructive-action and open-ingress checks.


### 3. Risk Assessment

Explain the risk identified by the Bash check and Claude Code.

The Bash policy check confirmed that the plan contained a pending change, but it did not identify a delete or replacement action. It also did not detect an ingress rule exposing access through 0.0.0.0/0 or *.

Claude Code reviewed the report and explained that the proposed change was an update rather than a destructive operation. However, the change still required human review because even a normal update could affect application availability, configuration behavior, security, or dependent resources if it was not intentional.

### 4. Human-Approved Action

Explain the action you reviewed and executed manually.

I reviewed the Terraform plan, the generated drift report, and Claude Code’s analysis. After confirming that the proposed update was expected and safe for the assignment, I manually executed the approved Terraform action.

The action was not executed by Claude Code, the /tf-drift-review Skill, or the Bash script. The infrastructure-changing command was run manually only after reviewing the available evidence.

### 5. Verification

Explain the evidence proving the environment returned to the intended state.

After resolving the difference, I ran the Terraform drift-review workflow again. The final review returned:

terraform plan exit code 0 — no pending changes
Overall Status: HEALTHY
Script Exit Code: 0

The final report was saved as:
reports/resolved-report.txt

This evidence proves that the Terraform configuration, Terraform state, and deployed infrastructure were aligned again, with no unexpected changes remaining.

### 6. Safety Decision

Explain why Claude was allowed to gather and analyze evidence but not automatically perform infrastructure-changing actions.

Claude was allowed to gather and analyze evidence because these activities were read-only and did not change the infrastructure. The Skill could run the drift-check script, read the Terraform report and plan JSON, identify risks, and provide a recommendation.

Claude was not allowed to automatically run infrastructure-changing commands because terraform apply and terraform destroy can modify, replace, or delete real cloud resources. Human approval was required to confirm that the change was intentional and appropriate. The safety rules also prevented the use of -auto-approve, ensuring that the final infrastructure decision remained under human control.

### 7. Agentic Loop Mapping

Explain how your workflow followed:

```text
Gather --> Analyze --> Human Act --> Verify
```

The workflow followed the Agentic AI loop as follows:

Gather: The Bash script ran terraform plan -detailed-exitcode, created the Terraform plan JSON, checked for destructive actions and unsafe ingress rules, and generated a review report.

Analyze: Claude Code read CLAUDE.md, reviewed the report and plan evidence, explained the proposed change, assessed the risk, and recommended the next step.

Human Act: I reviewed the evidence and manually executed the approved Terraform action.

Verify: I ran the drift-review workflow again and confirmed a HEALTHY result with no pending Terraform changes.

## Questions

### 1. What action did you execute to resolve the difference?

I reviewed the proposed update and then manually ran the approved Terraform action to bring the deployed infrastructure in line with the intended Terraform configuration.

### 2. Did you review `terraform plan` before taking action?

Yes. I reviewed the Terraform plan before executing the approved action. I confirmed the affected resource, the proposed update, and that there were no unexpected deletions, replacements, or unsafe ingress changes.

### 3. What evidence proves the environment is now aligned?

The final drift review returned:

terraform plan exit code 0 — no pending changes
Overall Status: HEALTHY
Script Exit Code: 0

The existence of reports/resolved-report.txt also provides saved evidence of the final verification.

### 4. Why is a second drift review required after the fix?

A second drift review confirms that the intended action actually resolved the difference. It verifies that the Terraform configuration, Terraform state, and deployed infrastructure are aligned and that no unexpected changes remain.

### 5. What could go wrong if an AI agent automatically applied every detected Terraform change?

An AI agent could unintentionally delete production resources, replace important infrastructure, expose a service to the internet, modify the wrong environment, cause application downtime, or apply an incorrect Terraform configuration. Automatic application would remove the human checkpoint required for high-impact infrastructure decisions.

### 6. In one sentence, explain the difference between asking an AI chatbot “Is my infrastructure okay?” and using this evidence-based Agentic AI workflow.

Asking an AI chatbot depends mainly on a general response, while the evidence-based Agentic AI workflow gathers deterministic Terraform evidence, performs policy checks, uses Claude to interpret the results, requires human approval, and verifies the final state.

---

# LinkedIn Post — Mandatory

## Goal

Publish a LinkedIn post in your own words describing:

- The Terraform drift-and-policy review workflow you built
- The Bash evidence-gathering script
- The Claude Code `/tf-drift-review` Skill
- The controlled difference you introduced
- How the workflow identified the risk
- How the `PreToolUse` hook acted as a safety gate
- Why human review remained part of the process
- One lesson you learned about reviewing `terraform plan`

Include a screenshot of the detected change and a screenshot of the final `HEALTHY` review in your post.

Suggested tags:

```text
#DMIByPravinMishra #Terraform #AgenticAI #ClaudeCode #DevOps
```

## LinkedIn Evidence

### LinkedIn Post URL

https://lnkd.in/p/d4yr9yaU


---

# Required Assignment Files

Confirm that the following files are included in your GitHub repository:

- `CLAUDE.md`
- `AI Assignment/tf-drift-check.sh`
- `.claude/skills/tf-drift-review/SKILL.md`
- `.claude/settings.json` containing the safety hook
- `reports/drift-detected-report.txt`
- `reports/resolved-report.txt`
- `drift-review-summary.md`

---

# Submission Instructions

- Complete Tasks 1–8 in sequence.
- Include Screenshots 1–19 exactly as specified.
- Answer every question under Tasks 1–8 in your own words.
- Complete all seven sections of the Terraform Drift Review Summary.
- Include the GitHub repository/folder URL containing the assignment files.
- Include your full name in the required reports and screenshots.
- Include the LinkedIn post URL and a screenshot of the published LinkedIn post.
- Do not expose access keys, passwords, tokens, account IDs, private keys, Terraform secrets, or other sensitive information.
- Review all screenshots carefully and hide or redact sensitive details where necessary.

---

# Completion Checklist

- [ ] Confirmed a clean Terraform baseline
- [ ] Created the required assignment workspace
- [ ] Created or updated `CLAUDE.md`
- [ ] Added project context and safety rules
- [ ] Created `tf-drift-check.sh`
- [ ] Added my full name to the report
- [ ] Validated the Bash script
- [ ] Made the script executable
- [ ] Used `terraform plan -detailed-exitcode`
- [ ] Used Terraform plan JSON
- [ ] Used `jq` to inspect destructive actions
- [ ] Used `jq` to inspect unsafe ingress
- [ ] Confirmed the baseline returns `HEALTHY`
- [ ] Created `/tf-drift-review`
- [ ] Restricted the Skill to appropriate tools
- [ ] Confirmed the Skill remains read-only
- [ ] Confirmed the Skill never runs `terraform apply`
- [ ] Confirmed the Skill never runs `terraform destroy`
- [ ] Introduced a controlled detectable difference
- [ ] Correctly identified whether it was true drift or a configuration change
- [ ] Saved `drift-detected-report.txt`
- [ ] Added the `PreToolUse` safety hook
- [ ] Verified the hook blocks `terraform apply` when the report is `FAIL`
- [ ] Reviewed the Terraform evidence before resolving the change
- [ ] Performed any infrastructure-changing action manually
- [ ] Ran the drift review again after resolution
- [ ] Confirmed the final status is `HEALTHY`
- [ ] Saved `resolved-report.txt`
- [ ] Completed `drift-review-summary.md`
- [ ] Mapped the workflow to `Gather --> Analyze --> Human Act --> Verify`
- [ ] Included all 19 numbered screenshots
- [ ] Answered all required questions
- [ ] Published the required LinkedIn post
- [ ] Added the LinkedIn post URL and screenshot
- [ ] Included the GitHub repository/folder URL
- [ ] Confirmed that no sensitive information is exposed

---

*This submission is part of the DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
