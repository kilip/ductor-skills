---
name: email-reader
description: Workflow for discovering and recording new unread emails from Google accounts into the Obsidian inbox.
---

# 📥 Email Reader Workflow

This skill automates the intake of new emails into the Obsidian inbox and links them to the daily journal.

## 🎯 Step 1: Email Discovery & Intake

1.  **Account Listing:**
    *   Use `gog auth list` to identify all authenticated accounts.
2.  **Unread Sweep:**
    *   For each account, list unread threads: `gog gmail list "is:unread" -a <account> --json`.
3.  **Promotional Cleanup:**
    *   Identify promotional emails: `gog gmail list "label:CATEGORY_PROMOTIONS is:unread" -a <account> --json`.
    *   **Action:** Add to `00-Inbox/Unsubscribe List.md` in format `[Sender](unsubscribe-link)`.
    *   **Mark Read:** Immediately mark as read using `gog gmail mark-read`.
4.  **Regular Email Recording:**
    *   For non-promotional unread threads:
        *   Fetch full content: `gog gmail get <id> -a <account> --json`.
        *   Create note in `00-Inbox/Emails/` using `03-Referensi/Template/TPL-Email-Inbox.md`.
        *   **Filename:** `YYYY-MM-DD-<id>-<slug-subject>.md`.
        *   **Frontmatter:** Fill all fields (`kepada` = recipient account).
        *   **Mark Read:** Mark as read in Gmail after recording.

## 🎯 Step 2: Daily Journal Synchronization

For every new regular email note created in Step 1:
1.  **Locate Journal:** Find today's journal entry in `05-Jurnal-Harian/YYYY-MM-DD.md`.
2.  **Locate Section:** Find the `## 📧 Emails` section.
3.  **Link Email:** Append a link to the note under that section:
    *   Format: `- [[YYYY-MM-DD-<id>-<slug-subject>|📧 <Subject> (Dari: <Nama/Instansi> - <email@pengirim>)]]`

## 🎯 Step 3: Git Synchronization

**MANDATORY:** After any intake session, Gem must perform a Git sync:
```bash
git add .
git commit -m "feat: record new emails and sync to daily journal with sender info"
git push
```

## 🛠️ Tools & Resources
- **Template:** `03-Referensi/Template/TPL-Email-Inbox.md`
- **Inbox Path:** `00-Inbox/Emails/`
- **Journal Path:** `05-Jurnal-Harian/`
- **Unsubscribe List:** `00-Inbox/Unsubscribe List.md`
- **Gmail Tool:** `gog` CLI
