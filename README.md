# Terraform Practical - Exercise 2

This repo is a **timed, hands-on Terraform exercise**. It contains a working-but-substandard
Terraform repo (`app-platform/`) that you have to **refactor and harden**, live, exactly like a
real piece of work.

There is **no solution in this repo on purpose** - you clone it and work through it yourself.

> This is the second exercise. Exercise 1 was "the pipeline is red, fix it". This one is
> different: the code **validates and the plan would succeed**, but it is not up to standard and
> security would reject it. Your job is to make it good, not just make it green.

---

## 1. What the exercise is (the brief)

> "Here's a repository for the nonprod application platform. It deploys, but a colleague has
> flagged that the code isn't up to standard and security won't sign it off. You've got
> **30 minutes**."
>
> - **Refactor** it to our standards (prefer `for_each` over `count`, no hard-coded values,
>   no repetition).
> - **Harden** it - fix anything a security reviewer would object to.
> - Add a **security-scan stage** to the pipeline.
> - Raise a **merge request** with your changes.

You will **not** have live AWS credentials, so `terraform plan` against the real API won't
work. Focus on **`init`, `fmt` and `validate`**, and *say out loud* what you'd expect a real
`plan` to show - especially that a good refactor shows **no destroy/recreate**.

There's also a **10-minute follow-on**: you'll be shown a `terraform plan` for a *production*
change and asked *"Is this safe to apply?"* - talk through your reasoning.

### The rules on the day
- **Time:** 30 minutes. You'll be told at 15, 25 and 28 minutes.
- **Tools:** Terraform CLI + Git. **No** live cloud credentials.
- **Narrate:** talk through your reasoning the whole time - *silence scores nothing*.
- **Deliver:** a feature branch, small meaningful commits, and a merge request with a real description.

---

## 2. What you need installed (one-time setup)

You need **two** tools: **Git** and **Terraform**. Here's the idiot-proof way on Windows.

### 2a. Install Git (if `git --version` fails)
Download and run the installer: <https://git-scm.com/download/win>
Accept all the defaults. Done.

### 2b. Install Terraform (the easy way)
Open **PowerShell** and run:

```powershell
winget install HashiCorp.Terraform
```

Then **close and reopen PowerShell** so it picks up the new command.

> No `winget`? Manual way: download the Windows **AMD64** zip from
> <https://developer.hashicorp.com/terraform/install>, unzip it, put `terraform.exe`
> in a folder like `C:\terraform`, then add that folder to your PATH.

### 2c. (Optional) Install Checkov for the security scan
The task asks you to add a scan stage. To try it locally you can install Checkov:

```powershell
pip install checkov
```

You don't strictly need it running locally - being able to *add the CI stage* and explain it is
what scores. `tfsec` or `trivy config` are equally good answers.

### 2d. Check your tools work
```powershell
git --version
terraform version
```
Both should print a version number. If they do, you're ready.

---

## 3. Get the exercise onto your machine

Clone it, then move into the folder:

```powershell
git clone https://github.com/akabii/terraform-exercise-2.git
cd terraform-exercise-2
```

The exercise lives in the **`app-platform/`** folder.

---

## 4. How to actually do the exercise (step by step)

Do these in order. **Narrate every step out loud** - practise talking as you type.

### Step 1 - Look before you touch
```powershell
cd app-platform\terraform
```
Read every file. Say what each one does. Note anything that smells wrong - both **quality**
(repetition, hard-coding, `count`) and **security** (secrets, open ports, public buckets).

### Step 2 - Get a clean baseline
```powershell
terraform init -backend=false
terraform fmt -check -recursive
terraform validate
```
- `validate` should **pass** here - this is a refactor, not a bug-hunt.
- `fmt -check` will **fail** - that's your first easy win.
- Say out loud what you'd expect a real `plan` to show.

### Step 3 - Make a feature branch (never work on main)
```powershell
git checkout -b refactor/app-platform-harden
```

### Step 4 - Refactor and harden, committing in small logical chunks
Work in increments, running `fmt` + `validate` after each, and commit each working change:
```powershell
git add terraform/main.tf
git commit -m "refactor: key subnets by AZ with for_each and a moved block"
```
When you convert `count` to `for_each`, add a `moved` block so the plan shows **no destroy**.
Say that out loud - it's the whole point of the refactor.

### Step 5 - Prove it's clean
```powershell
terraform fmt -recursive
terraform validate
```
`validate` should say **"Success!"** and `fmt` should make no further changes.

### Step 6 - Push and raise a merge/pull request
```powershell
git push -u origin refactor/app-platform-harden
```
Then open a PR/MR with a **real description**: what you refactored, what you hardened,
and what you'd still do with more time.

---

## 5. What you're being marked on

You do **not** need to finish everything. It's about your **method**:

- Do you run `init` / `validate` **before** changing things? (clean baseline first)
- Feature branch + small meaningful commits + a real MR description.
- **Refactor quality:** `for_each` over `count`, a `moved` block so nothing is destroyed,
  no hard-coded CIDRs, no repeated blocks.
- **Security eye:** spotting anything you wouldn't show a security reviewer, and saying a leaked
  secret must be **rotated**, not just deleted.
- Adding a **scan stage** (checkov/tfsec/trivy) to the pipeline.
- Being able to **explain every choice**.

---

## 6. Golden rules for the day

- **Talk constantly.** Explain what you're reading, thinking, and doing.
- **Baseline first.** Run `init`/`validate` before editing anything.
- **A good refactor destroys nothing.** Prove it with `moved` blocks and the plan.
- **Security is half the test.** Hunt for secrets, open rules, and public storage.
- **Small commits, clear messages.** Each commit should tell a story.
- **Manage the clock.** Don't rabbit-hole; a partial-but-clean answer beats a rushed mess.
- **It's fine not to finish.** Method beats completion.

Good luck - think out loud and back yourself.
