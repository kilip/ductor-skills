---
name: email-processor
description: Workflow for processing email notes in Obsidian (replying, archiving, deleting) and moving them out of the Inbox.
---

# ⚙️ Email Processor Workflow

This skill handles the execution of inbox actions (Reply, Archive, Delete) based on markers set by Ibu Bos in Obsidian email notes.

## 🎯 Step 1: Scan for Actions

Identify all email notes in `00-Inbox/Emails/` that have completion markers `[x]` in the `## 🔄 Status Penyelesaian` section.

## 🎯 Step 2: Execution Logic

### 1. Action: Reply (`[x] 💬 Reply`)
*   **Data Extraction:**
    *   `Thread ID`: From frontmatter `id`.
    *   `Recipient`: From frontmatter `email-dari`.
    *   `Account`: From frontmatter `kepada`.
    *   `Body`: Extract content from `## 💬 Reply` section (removing prompt text).
*   **Execute:** `gog gmail send --thread <id> -a <account> --body "<extracted_body>"`
*   **Update:** 
    *   Log: "Email dibalas" with timestamp.
    *   Status: `status: dibalas` in frontmatter.
*   **Next:** Automatically proceed to **Archive**.

### 2. Action: Archive (`[x] 🗄️ Diarsipkan`)
*   **Data Extraction:**
    *   `Year`: From frontmatter `tanggal-terima` (YYYY).
*   **Update:**
    *   Log: "Diarsipkan" with timestamp.
    *   Status: `status: diarsipkan` in frontmatter.
*   **Execute:** Move file to `04-Arsip/Emails/YYYY/`. (Create directory if needed).

### 3. Action: Delete (`[x] 🗑️ Hapus`)
*   **Execute:** Permanently delete the file from `00-Inbox/Emails/`.

## 🎯 Step 3: Git Synchronization

**MANDATORY:** After any processing session, Gem must perform a Git sync:
```bash
git add .
git commit -m "chore: process email actions (reply/archive/delete)"
git push
```

## 🛠️ Tools & Resources
- **Gmail Tool:** `gog` CLI
- **Inbox Path:** `00-Inbox/Emails/`
- **Archive Path:** `04-Arsip/Emails/`
- **Git:** Standard git sync rules.
