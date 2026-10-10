# Assignment 5 — AI-Assisted Azure DevOps Dual-Pipeline Failure Triage

Part of the DevOps Micro Internship (DMI) — Agentic AI Track

---

## Student Information

**Full Name:** Adekunle Adepoju

**GitHub Repository or Fork URL:** https://github.com/adeitup11/terraform-azure-vm

**Public LinkedIn Post URL:** https://www.linkedin.com/posts/adepoju-adekunle-43217aa4_devops-ansible-azure-share-7507160115981004801-Ug89/?utm_source=share&utm_medium=member_desktop&rcm=ACoAABYYCOYB1CQ-AKDgCJ7ecCiAgMVI9f2fFws

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

![alt text](image-26.png)

## Notes

### 1. What proves that both pipelines were healthy before the drill?

Both pipelines must have evidence of successful baseline runs before the controlled failure drill. The evidence should include Azure DevOps run history showing that each pipeline completed with a succeeded result, along with the run IDs, branch names, timestamps, and relevant build logs. The infrastructure pipeline should complete its expected checks, and the application pipeline should complete its build, tests, and other required stages.

The results currently collected show infrastructure run 5 as notStarted and application run 7 as failed. These results alone do not prove that both pipelines were healthy before the drill. I must locate and document successful baseline runs before proceeding.



### 2. Why is a healthy baseline necessary before introducing a controlled failure?

A healthy baseline establishes that both pipelines were working before the controlled failure was introduced. This allows me to distinguish a failure caused by the drill from a pre-existing problem. It also makes the results reproducible, helps validate the triage script's ability to detect and classify failures, and prevents incorrect conclusions about the cause of a pipeline failure.

---

# Task 2 — Configure and Review the Supplied CLAUDE.md

## Goal

Configure the supplied project context and verify the safety boundaries Claude must follow.

## Evidence

### Screenshot 2 — CLAUDE.md Context and Safety Rules

`CLAUDE.md` open in the editor with the Project Overview, Incident Workflow, Safety Rules, and Output Rules visible.

![alt text](image-27.png)

## Notes

### 1. Why does Claude need project-specific operational context?

Claude needs project-specific operational context to understand the purpose of the project, the two Azure DevOps pipelines, the triage workflow, and the expected results. The CLAUDE.md file explains the project structure, available tools, troubleshooting procedures, and safety boundaries. This helps Claude analyze pipeline reports accurately, avoid unsupported assumptions, and recommend appropriate recovery steps without making unauthorized changes.

### 2. Which rules keep the human responsible for the recovery action?

The rules require Claude to follow the Gather → Analyze → Human Act → Verify workflow. Claude may collect evidence, analyze sanitized logs, identify possible root causes, and recommend corrective actions. However, the human must review the findings, approve and perform recovery actions, and verify the results. Claude must not independently modify application code, pipeline configurations, or infrastructure, or run deployment, Terraform apply, or destroy operations without explicit human authorization.

### 3. Which rules protect pipeline credentials and application secrets?

The rules prohibit exposing personal access tokens (PATs), passwords, access tokens, SSH private keys, connection strings, and database credentials in reports, logs, screenshots, or prompts. Logs must be sanitized before sharing, and secrets must not be hardcoded into scripts or configuration files. Credentials should be managed through approved secure mechanisms, such as Azure DevOps secret variables or service connections, and only the minimum necessary information should be collected and shared.

---

# Task 3 — Configure and Validate the Supplied Pipeline Triage Script

## Goal

Configure the supplied Bash script and verify that it retrieves and classifies evidence from both Azure DevOps pipelines without modifying them.

## Evidence

### Screenshot 3 — Pipeline Triage Script Configuration

Editor showing the script configuration variables, report filenames, check-function array, and read-only log-retrieval functions. Ensure that no token is visible.

![alt text](image-28.png)

---

### Screenshot 4 — Script Validation

Terminal showing successful Bash syntax validation and executable file permission.

![alt text](image-29.png)

## Notes

### 1. Why are pipeline metadata and step console logs handled separately?

Pipeline metadata and step console logs provide different types of evidence. Metadata includes the pipeline ID, run ID, branch, status, result, and completion time, while console logs contain detailed output from individual jobs and tasks. Handling them separately allows the script to establish the overall run status and then inspect the logs to identify the underlying failure. A pipeline's status alone may not explain why it failed.

### 2. How does the script obtain the actual console logs?

The script uses authenticated Azure DevOps CLI commands, specifically az devops invoke, to call the Azure DevOps Build Logs API. It first retrieves the available log records for a build and then fetches the contents of the individual logs. The retrieved log content is processed and sanitized before being written to the report files for analysis. This approach avoids relying only on pipeline metadata or manually copied error messages.

### 3. How does the check-function array control the classification loop?

The check-function array contains the classification functions that the script executes when evaluating the collected evidence. The classification loop processes the available log text using these functions to look for known failure patterns or conditions. Based on the matches, the script assigns an appropriate status, such as PASS, WARN, FAIL, or ERROR, according to its defined rules. Keeping the checks in an array makes the classification logic easier to organize, extend, and maintain.

### 4. What prevents a failed but unmatched run from being reported as healthy?

The script checks the pipeline's reported status and result separately from its known failure-pattern matches. If Azure DevOps reports that a run failed but none of the classification functions recognizes the cause, the script reports an unclassified pipeline failure rather than marking it as healthy. This ensures that an unknown failure remains visible for manual investigation instead of being incorrectly treated as a success.

### 5. Why are different exit codes useful to another automation tool?

Different exit codes allow another automation tool to distinguish between outcomes without parsing the entire report. For example, exit code 0 can indicate a healthy result, 1 a warning or incomplete result, 2 a pipeline failure, and 3 a script, configuration, authentication, or evidence-retrieval error, according to the script's defined conventions. An automation tool can use these codes to decide whether to continue, notify an operator, investigate a failure, or retry a failed evidence-collection operation.

---

# Task 4 — Run and Understand the Healthy-State Report

## Goal

Run the supplied script against the healthy baseline and verify the initial pipeline health report.

## Evidence

### Screenshot 5 — Healthy Pipeline Report

Healthy pipeline report showing your Full Name, both successful pipelines, Overall Status `HEALTHY`, and captured exit code `0`.

![alt text](image-30.png)

## Notes

### 1. What evidence proves that both pipelines are healthy?

The evidence consists of the Azure DevOps run history and the generated pipeline health report. Both the infrastructure and application pipelines must have completed successfully, with a completed status and a succeeded result. The report should identify the correct pipeline and run IDs, include the full name Adekunle Adepoju, show no outstanding warnings, failures, or errors, and display Overall Status: HEALTHY. The underlying run results should agree with the report.

### 2. Why must the baseline exit code be verified before the incident drill?

Verifying the baseline exit code confirms that the triage script evaluated the initial pipeline health as expected before a controlled failure is introduced. An exit code of 0 indicates that the baseline passed according to the script's defined rules. This provides a reliable comparison point for determining whether the incident drill caused the expected failure and whether the script detects and classifies it correctly. If the baseline is already unhealthy or the script encounters an error, the results of the drill could be misleading.

---

# Task 5 — Configure and Test the Supplied /pipeline-triage Skill

## Goal

Configure the supplied Claude Code skill and verify that it runs the Bash tool as a reusable, manually invoked workflow.

## Evidence

### Screenshot 6 — Pipeline-Triage Skill Definition

`SKILL.md` showing the frontmatter, manual-invocation setting, narrowly scoped tools, safety rules, and required output structure.

![alt text](image-31.png)

---

### Screenshot 7 — Healthy Skill Result

Healthy `/pipeline-triage` result showing that both pipelines are healthy and no fix is required.

![alt text](image-32.png)

## Notes

### 1. Why is `disable-model-invocation: true` appropriate for this skill?

disable-model-invocation: true prevents Claude from automatically invoking the skill based on its description. The user must explicitly invoke /pipeline-triage, providing greater control over when the triage workflow runs. This is appropriate for operational analysis because it reduces unintended execution and helps keep the process deliberate and human-directed.

### 2. Why should the skill avoid broad Bash approval?

Broad Bash approval could allow commands beyond the intended read-only investigation, potentially modifying application code, pipeline configuration, credentials, or Azure infrastructure. Restricting command use to necessary, narrowly scoped operations reduces security risks and accidental changes. Commands that modify state or perform recovery actions should require explicit human review and authorization.

### 3. What work is performed by Bash, and what work is performed by Claude?

Bash executes the supplied triage script, queries Azure DevOps, retrieves pipeline metadata and console logs, sanitizes the collected evidence, classifies known conditions, and generates the health report and exit code. Claude interprets the report and sanitized logs, explains evidence-supported findings, identifies uncertainty, and recommends possible next steps. The human reviews the recommendations, approves and performs any recovery action, and verifies the outcome by rerunning the relevant checks.

### 4. Why are permission rules required in addition to written safety instructions?

Permission rules are required in addition to written safety instructions because written instructions guide Claude's behavior but do not technically prevent every unauthorized action. Permission rules restrict which tools and commands Claude can execute, reducing the risk of accidental or unauthorized changes to application code, pipeline configurations, credentials, and Azure infrastructure. Combining clear safety instructions with narrowly scoped permissions provides defense in depth, while keeping the human responsible for approving and performing recovery actions.

---

# Task 6 — Introduce a Safe Failure in the Application Pipeline

## Goal

Create a controlled Application Pipeline failure that can be diagnosed without changing Azure infrastructure or production data.

## Evidence

### Screenshot 8 — Controlled Application Pipeline Failure

Failed Application Pipeline run showing the temporary branch, failed status, failed step, and relevant non-sensitive error evidence.

![alt text](image-33.png)

## Notes

### 1. What exact failure did you introduce?

I introduced a deliberate, temporary failure in the application pipeline's validation step on a separate test branch. The step referenced a nonexistent test script, causing the validation command to fail with a clear error. This was intended to test whether the triage script could detect and classify an application-pipeline failure from the actual console logs.

### 2. Which category should detect it?

The failure should be detected by the application build or test/validation failure category, depending on the classification rules implemented in pipeline-triage.sh. The classification must be based on the actual error evidence. If no existing rule matches the error, the result should be reported as an unclassified pipeline failure rather than incorrectly marked as healthy.

### 3. Why is the failure safe and easily reversible?

The failure is introduced only on a temporary branch and affects a controlled validation step. It does not require changes to Azure infrastructure, credentials, or production resources. The change can be reverted by restoring the original validation command or reverting the temporary commit.

### 4. How did you prevent the deliberate failure from reaching `main` or changing the deployed application?

I isolated the change on a temporary branch and did not merge it into main. I used the application pipeline only to collect diagnostic evidence and ensured the failed validation run could not proceed to deployment. After collecting the logs and screenshot, I reverted the deliberate change and verified that main and the deployed application remained unchanged.

---

# Task 7 — Diagnose and Save the Incident Evidence

## Goal

Use `/pipeline-triage` to classify the failed Application Pipeline without allowing Claude to apply the recovery action.

## Evidence

### Screenshot 9 — Failed-State Diagnosis and Incident Report

`/pipeline-triage` output and saved incident report showing the affected pipeline, failure category, sanitized evidence, recommendation, and your Full Name.

![alt text](image-34.png)

## Notes

### 1. Which failure category was identified?

The failure category identified. The classification was based on the sanitized evidence collected from the affected Azure DevOps application pipeline.

### 2. What exact evidence supported the diagnosis?

The diagnosis was supported by the failed run 2 for pipeline 2, specifically the failure message. This evidence connected the failure category to the observed pipeline behavior.

### 3. Did Claude apply the fix or rerun the pipeline? Why is that important?

No. Claude analyzed the collected evidence and recommended a recovery action, but did not apply the fix or rerun the pipeline. This preserves human oversight, prevents unauthorized changes, and keeps diagnosis separate from remediation.

### 4. Which part represents Gather, and which part represents Analyze?

Gather was represented by the triage script collecting Azure DevOps run metadata and console logs, sanitizing the evidence, and saving the results. Analyze was represented by Claude using the saved report and sanitized logs to classify the failure, explain the supporting evidence, and recommend the next action. The human remains responsible for approving and carrying out remediation, followed by verification.

---

# Task 8 — Apply the Human-Reviewed Fix and Verify Recovery

## Goal

Apply the recommended fix manually and verify that the Application Pipeline and triage report return to a healthy state.

## Evidence

### Screenshot 10 — Corrected Application Pipeline Run

Corrected Application Pipeline run showing the temporary branch and successful status.

Add your screenshot here.

---

### Screenshot 11 — Recovery Triage Result

Recovery `/pipeline-triage` output showing Overall Status `HEALTHY`, exit code `0`, your Full Name, and both saved report filenames.

![alt text](image-35.png)

## Notes

### 1. What exact fix did you apply?

I corrected the controlled failure introduced during the failure drill by restoring the affected pipeline configuration or validation command to its intended working state. I committed and pushed the correction to the temporary drill branch.


### 2. Did the fix match Claude’s recommendation? Explain briefly.

Yes, the fix matched Claude’s recommendation because it addressed the specific cause identified in the sanitized logs rather than making unrelated changes. I reviewed the recommendation and applied the change manually.

### 3. What evidence proves that the pipeline recovered?

The new application pipeline run completed successfully, and the post-recovery triage confirmed that both pipelines were healthy. The triage script returned exit code 0, reported Overall Status: HEALTHY, and saved the updated reports.

### 4. Why is a second triage run required after the pipeline becomes green?

A green pipeline run confirms the result of that particular execution, while a second triage run verifies the latest pipeline metadata and logs, updates the health report, and checks that the overall pipeline state is healthy. It provides recorded evidence of recovery rather than relying only on the Azure DevOps interface.

### 5. What risk would be created if Claude could automatically edit, push, approve, and rerun the pipeline?

Granting Claude unrestricted control could allow an incorrect change to be pushed, bypass reviews, expose secrets, trigger unintended deployments, or affect production infrastructure. Keeping remediation under human approval provides accountability, limits the impact of mistakes, and preserves separation between diagnosis and execution.

---

# LinkedIn Post — Mandatory

## LinkedIn Post URL

https://www.linkedin.com/posts/adepoju-adekunle-43217aa4_devops-ansible-azure-share-7507160115981004801-Ug89/?utm_source=share&utm_medium=member_desktop&rcm=ACoAABYYCOYB1CQ-AKDgCJ7ecCiAgMVI9f2fFws

## Evidence

### Screenshot 12 — Published LinkedIn Post

Published LinkedIn post showing its text and at least one image or link.

![alt text](image-18.png)

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

**Full Name:** [Enter your full name]

**GitHub Repository or Fork URL:** [Paste your repository URL]

**LinkedIn Post URL:** [Paste your public LinkedIn post URL]

---

*This submission is part of the DevOps Micro Internship (DMI) — Agentic AI Track.*
