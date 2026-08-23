# Assignment 7 — AI-Assisted Azure Security Posture Audit

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build a read-only Bash script that audits the Azure resources you deployed earlier this week — a virtual machine, a three-tier network with a Load Balancer, a Storage Account, and an Azure Database for MySQL server — for common security misconfigurations. You will connect that script to Claude Code as a reusable `/azure-audit` skill that explains findings and recommends a fix without ever running it, then fix one real finding yourself and prove the fix with a second audit run. This is the same read-only-evidence-then-human-fixes discipline from Week 3, now applied to Azure with the `az` CLI instead of Linux commands — and the cloud-agnostic counterpart to the AWS audit you built in Week 6.

---

# Task 1 — Confirm Your Resources and Create the Workspace

## Goal

Confirm your Azure CLI is authenticated and can see the VM, network, storage account, and MySQL server you built this week, then set up a workspace folder for the audit.

### Evidence

#### Screenshot 1 — `az account show` and `az vm list -d -o table` confirming your subscription and running VM (subscription ID partially blurred)

![alt text](image-51.png)

---

# Task 2 — Create Project Context and Safety Rules in CLAUDE.md

## Goal

Create a `CLAUDE.md` for this workspace that tells Claude what the audit covers and the safety rules it must follow: never run a mutating `az` command, never claim a finding without report evidence, and always let the human review and run any remediation.

### Evidence

#### Screenshot 2 — `CLAUDE.md` open in your editor showing the project overview, audit workflow, and safety rules

![alt text](image-61.png)

---

# Task 3 — Use Agentic AI to Plan the Audit Before Writing the Script

## Goal

Ask Claude Code to read `CLAUDE.md` and propose a read-only, four-check audit plan (NSG rules open to `0.0.0.0/0` on port 22 or 3389, storage account public blob access, VM disk encryption status, and Azure Database for MySQL public network access) — without creating or editing any file yet.

### Evidence

#### Screenshot 3 — Claude Code showing the four-check plan, with no files created or modified

![alt text](image-52.png)

---

# Task 4 — Build the Azure Audit Bash Script

## Goal

Write a Bash script that runs the four checks from Task 3 using read-only `az` commands, writes a PASS/WARN/FAIL report with your Full Name, and exits with a different code for a healthy, warning, or failing result. Validate it with `bash -n` and make it executable.

### Evidence

#### Screenshot 4 — Your script open in your editor, showing the check functions and the `az` commands they call

![alt text](image-60.png)

---

#### Screenshot 5 — Output of `bash -n` (no syntax errors) and `ls -l` showing the script is executable

![alt text](image-59.png)

---

# Task 5 — Run the Script and Review the Baseline Report

## Goal

Run the script against your live resources and read the report honestly, even if it shows a real finding — do not fix anything yet.

### Evidence

#### Screenshot 6 — Script output showing your Full Name and all four checks with a PASS, WARN, or FAIL result

![alt text](image-53.png)

---

# Task 6 — Create and Run the /azure-audit Skill

## Goal

Create a Claude Code skill restricted to read-only tools (no `Write`) that runs your script, reads the report, and explains every finding with the risk of leaving it unresolved — without ever running a remediation command itself.

### Evidence

#### Screenshot 7 — Your skill file's frontmatter showing `allowed-tools` without `Write`

![alt text](image-54.png)

---

#### Screenshot 8 — `/azure-audit` output showing the baseline findings and Claude's explanation

A![alt text](image-55.png)

---

# Task 7 — Fix a Real Finding and Re-Verify

## Goal

Pick one WARN or FAIL finding (or deliberately open an NSG rule to port 22 from `0.0.0.0/0` if your baseline was already clean), save that failing report, run the remediation command yourself — scoped to your own IP, not left open — and confirm the second audit run shows it resolved.

### Evidence

#### Screenshot 9 — Saved report showing the original finding before the fix

![alt text](image-56.png)

---

#### Screenshot 10 — Terminal output of the remediation command you ran yourself

![alt text](image-57.png)

---

#### Screenshot 11 — Second `/azure-audit` run (or report) showing the finding resolved

![alt text](image-58.png)

---

### Notes

Compare this assignment to the AWS audit you built in Week 6: which finding categories map to each other across the two clouds, and what stayed exactly the same about the workflow even though the `az`/`aws` commands are completely different?

es. The Week 6 AWS audit and this Azure audit follow essentially the same security-audit pattern, even though the resource names and CLI syntax differ.

Audit category	AWS Week 6 equivalent	Azure assignment equivalent
Network exposure	Security Groups / NACLs allowing 0.0.0.0/0 to SSH/RDP	NSG rules allowing 0.0.0.0/0 to ports 22/3389
Storage exposure	S3 bucket public access / bucket policy	Storage Account public blob access
Disk encryption	EBS volume encryption / KMS configuration	Azure VM OS/data disk encryption
Database exposure	RDS public accessibility / security-group access to 3306	Azure Database for MySQL public network access
Monitoring	CloudWatch / CloudTrail evidence	Azure Monitor / diagnostic settings
Backups	RDS automated backups / retention	Azure Database for MySQL backup/retention
Evidence	CLI output + screenshots	CLI output + Azure Portal screenshots
What stayed exactly the same

The workflow is almost cloud-independent:

Read the project instructions first (CLAUDE.md).
Identify the resources belonging to the deployment.
Inspect current configuration using read-only commands.
Look specifically for security exposure, rather than changing anything immediately.
Compare the actual configuration against a secure expected state.
Classify findings by severity.
Capture evidence/screenshots showing the finding or secure configuration.
Do not remediate during the audit unless remediation is explicitly requested.
Protect secrets—don't expose credentials, tokens, private keys, or full sensitive identifiers in the evidence.
Summarize findings and recommendations after the inspection.

The commands changed:

AWS                         Azure
────────────────────────────────────────
aws ec2 ...                 az vm ...
aws ec2 describe-...        az network nsg ...
aws s3api ...               az storage ...
aws ec2 describe-volumes    az vm show / disk ...
aws rds describe-...        az mysql flexible-server ...

But the reasoning didn't change:

Identify resource
      ↓
Inspect configuration
      ↓
Find exposure/misconfiguration
      ↓
Compare with secure baseline
      ↓
Assign severity
      ↓
Capture evidence
      ↓
Recommend remediation
The biggest lesson

The cloud provider is not the audit methodology.

AWS and Azure use different APIs and terminology, but you're still asking the same fundamental security questions:

Can someone reach something they shouldn't? Is sensitive data exposed? Is data encrypted? Is the database unnecessarily public? Are backups and monitoring configured?

That's why the Week 6 AWS audit transfers well to this Azure assignment: the commands change, but the security thinking and evidence-driven workflow stay the same.

---

# Submission Instructions

Complete all tasks in sequence.

Your submission must include:
- All 11 required screenshots
- Do not expose your Azure subscription ID, tenant ID, client secrets, or connection strings

---

# Completion Checklist

- [ ] Task 1: Azure resources confirmed and workspace created (Screenshot 1)
- [ ] Task 2: `CLAUDE.md` created with project context and safety rules (Screenshot 2)
- [ ] Task 3: Claude produced a read-only four-check plan before any script existed (Screenshot 3)
- [ ] Task 4: Audit script built, syntax-checked, and executable (Screenshots 4–5)
- [ ] Task 5: Baseline audit run and reviewed honestly (Screenshot 6)
- [ ] Task 6: `/azure-audit` skill created with no `Write` permission and run successfully (Screenshots 7–8)
- [ ] Task 7: A real finding fixed by you (not Claude) and re-verified as resolved (Screenshots 9–11)
- [ ] Notes comparing this to the Week 6 AWS audit completed
- [ ] No subscription IDs, tenant IDs, or credentials exposed

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
