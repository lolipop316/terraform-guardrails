# My First Terraform Lab — Encryption Guardrail

This was my first real Terraform lab.  
The goal was simple: create an S3 bucket and make sure nothing can be uploaded unless it’s encrypted.

---

## What I Did

**1. Made the test file**  
I ran:
```bash
echo "test data" > test.txt
```
Just a small file to test the policy.

**2. Tried uploading without encryption**  
```bash
aws s3 cp test.txt s3://tf-least-priv-17d52a0c/test.txt
```
Got hit with:
```
AccessDenied … explicit deny
```
That’s actually good — it means the Terraform policy blocked my upload because it wasn’t encrypted.

**3. Tried again with encryption**  
```bash
aws s3 cp test.txt s3://tf-least-priv-17d52a0c/test.txt --sse AES256
```
This time it worked. The `--sse AES256` flag adds the encryption header, so S3 allows the upload.

---

## What Happened Behind the Scenes

Terraform created the bucket and attached a policy that checks every upload.  
If there’s no `"s3:x-amz-server-side-encryption": "AES256"` header, it denies the request.  
That means AWS itself enforces the rule — not me, not the CLI — the bucket’s own policy does.

---

## What I Learned

- Terraform talks to AWS and builds everything automatically once you apply.  
- S3 bucket policies can block uploads if conditions aren’t met.  
- “AccessDenied” isn’t always an error — sometimes it’s proof that your security rule is working.  
- Terraform keeps track of all deployed resources in its state file (`terraform.tfstate`).

---

## Why It’s Cool

This one lab showed me how to **turn a security rule into code**.  
It’s not just “remember to encrypt uploads” — AWS now enforces it every single time.  
That’s *policy as code*, and this was my first time building one from scratch.

---

## 🧭 Next Lab: Python CloudTrail Parser

Next up, I’ll build a small Python script to scan CloudTrail logs and detect root logins or unauthorized API calls.  
This will connect the dots between Terraform-built guardrails and AWS activity monitoring.

**Folder:** `automation_lab_wed/`  
**Artifact:** `cloudtrail_parser.py` + `automation_lab_wed.md`
