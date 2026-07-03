# Lab Document: FirstYearBatch Attendance Automation

**Environment:** ClaudeLabs (local filesystem connector + n8n connector)
**Date:** July 3, 2026
**Objective:** Build a student attendance record, generate a visual report for the Principal, and automate low-attendance email alerts using n8n.

---

## Step 1 — Create a Test File on the Local System

**Goal:** Verify the Filesystem connector could read/write to the local `ClaudeLabs` folder.

**Actions:**
- Used the **Filesystem connector** to confirm the allowed directory: `C:\Users\NinadSamudre\Downloads\ClaudeLabs`
- Created a plain text file `hello.txt` containing the message `greeting message`

**Outcome:** Confirmed write access to the local folder. This connector can reliably write **text-based files** (`.txt`, `.csv`) but not binary formats like `.xlsx` — the underlying write tool encodes content as text, which would corrupt a binary file.

---

## Step 2 — Create the Attendance Sheet (50 Students)

**Goal:** Build a first-year student attendance roster.

**Actions:**
- Generated 50 dummy student records with the columns:
  `Name, Roll Number, Mobile Number, Email, City`
- Used realistic Indian names, roll numbers (`FY2026001`–`FY2026050`), 10-digit mobile numbers, and city names
- Saved as **`FirstYearBatch.csv`** directly into the ClaudeLabs folder

**Design decision:** Delivered as `.csv` instead of `.xlsx` because the Filesystem write tool only reliably handles text content — CSV opens natively in Excel with no data loss, while a true binary `.xlsx` written the same way risked corruption.

---

## Step 3 — Add an Attendance Column

**Goal:** Track attendance out of a 120-day term.

**Actions:**
- Added a 6th column: `Attendance (out of 120 days)`
- Assigned each student a random value between 70–120 (later expanded)
- Overwrote `FirstYearBatch.csv` in the ClaudeLabs folder with the updated data

**Note:** The first save attempt failed with a Windows file-lock error (`EPERM`) because the CSV was open in Excel at the time. Retried successfully once the file was closed.

---

## Step 4 — Build a Visual Attendance Report for the Principal

**Goal:** Turn the raw roster into a shareable, visual report.

**Actions:**
- Computed summary statistics: batch average attendance, and a 3-tier status split
  (Excellent ≥90%, Satisfactory 75–89%, Needs Attention <75%)
- Built a self-contained **HTML artifact** (`FirstYearBatch_Attendance_Report.html`) including:
  - A formal "letterhead" header with batch metadata and average attendance seal
  - Summary stat cards
  - **Charts** (via Chart.js): attendance distribution histogram, status-split donut chart, average attendance by city
  - A searchable, sortable **student roster table** with color-coded status badges
  - Signature blocks for Class Coordinator and Principal

**Outcome:** A polished, presentation-ready report rendered inline in the chat as an artifact, ready to share with the Principal.

---

## Step 5 — Build an n8n Workflow for Low-Attendance Alerts

**Goal:** Automatically email any student with fewer than 50 days of attendance (out of 120).

**Tooling:** n8n connector (workflow created via the n8n Workflow SDK)

**Workflow structure — "FirstYearBatch - Low Attendance Email Alert":**

| # | Node | Purpose |
|---|------|---------|
| 1 | **Start** (Manual Trigger) | Runs the workflow on demand |
| 2 | **Load FirstYearBatch Roster** (Code node) | Loads the 50-student dataset as workflow items |
| 3 | **Below 50 Days Attendance** (Filter node) | Keeps only students with `days < 50` |
| 4 | **For Each Low-Attendance Student** (Loop / Split in Batches) | Iterates one student at a time |
| 5 | **Send Low Attendance Alert** (Gmail node) | Sends a personalized HTML email per matching student |

- Email includes the student's name, roll number, attendance count, and city, and asks them to contact the coordination office.
- A sticky note documents setup requirements directly on the canvas.
- At creation time, **no student in the dataset had attendance below 50 days**, so the workflow correctly produced zero matches (expected "0-item" behavior, not a bug).

**Workflow link:** `https://ninjacloud.app.n8n.cloud/workflow/tXvOvEzCkzFbWnrg`

---

## Step 6 — Add a New Student Below the Threshold

**Goal:** Add a real test case that the alert workflow should catch.

**Actions:**
- Added **Ninad Samudre** (Roll `FY2026051`) with:
  - Email: `ninadsamudre14@gmail.com`
  - Attendance: `40 / 120 days` (33.3%)
  - City and mobile number: dummy values
- Updated in **three places** to keep everything in sync:
  1. `FirstYearBatch.csv` on the local ClaudeLabs folder
  2. The embedded dataset inside the HTML attendance report artifact
  3. The roster data inside the n8n workflow's Code node

---

## Step 7 — Test the Workflow

**Goal:** Confirm the alert logic and email delivery work end-to-end.

**Actions & Results:**

| Attempt | Result | Notes |
|---------|--------|-------|
| Test run #1 | ❌ Error | Filter correctly isolated Ninad Samudre as the only low-attendance student, but execution stopped at the Gmail node: *"Node does not have any credentials set."* |
| — | — | Gmail OAuth2 credential connected in n8n |
| Test run #2 | ✅ Success | Full workflow completed in ~2 seconds; alert email sent to `ninadsamudre14@gmail.com` |

**Verification:** Confirmed via `search_executions` — execution #2 shows `status: success`, immediately following the credential fix.

---

## Summary of Deliverables

| Artifact | Location |
|---|---|
| `hello.txt` | ClaudeLabs folder (local) |
| `FirstYearBatch.csv` (51 students, with attendance) | ClaudeLabs folder (local) |
| Attendance Report (HTML, with charts) | Shared as in-chat artifact |
| n8n workflow: Low Attendance Email Alert | n8n personal project |
| This lab document | ClaudeLabs folder / chat artifact |

## Key Learnings

1. The local Filesystem connector reliably writes **text formats only** (CSV, TXT) — binary Office formats risk corruption if written the same way.
2. n8n's **zero-item safety pattern** means a filter that matches nothing simply halts the chain — no error, no wasted email sends.
3. Credential errors in n8n are self-explanatory and non-destructive — the workflow logic can be fully validated before any real credential is attached.
4. Keeping source data in sync across multiple deliverables (CSV, report artifact, workflow) required deliberate, repeated updates — a natural point to eventually centralize with a shared data source (e.g. Google Sheets) instead of duplicated hardcoded copies.
