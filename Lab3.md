## Before You Begin (5 min — do this with the whole room together)

1. Open **Firefox** (or Chrome) on the Ubuntu machine.
2. Go to **claude.ai** → Sign up with a Google account, institutional email, or personal email. No credit card needed.
3. Once logged in, point out the interface:
   - **New Chat** (top left) — starts a fresh conversation
   - **Projects** (left sidebar) — a folder that keeps related chats + reference files + custom instructions together (e.g., one Project per course)
   - **Paperclip icon** — upload files (PDF, DOCX, CSV, images, up to 20 files/chat)
   - **Search toggle** — lets Claude look up current information from the web
   - **Settings → Memory** — Claude can remember facts across chats (optional, can be turned off)
4. Mention the free-tier limits honestly: message caps refresh on a rolling 5-hour window, so if Claude says "you've reached your limit," just wait or continue later. This is expected behavior, not an error.

---

## Lab 1 — Classroom & Course Management Assistant
**Goal:** Use a Claude *Project* to manage one course end-to-end — lesson plans, quizzes, rubrics — all in one organized place.

**Time:** 25 minutes

### Steps
1. Click **Projects → Create Project**. Name it after a real course, e.g. *"Data Structures – Sem 3"*.
2. Under **Project instructions**, add something like:
   > "You are helping me teach Data Structures to 3rd-semester engineering students. Keep explanations practical, use simple English, and always suggest an example problem after any concept."
3. Inside the project, start a new chat and ask:
   > "Create a 1-hour lesson plan on Binary Search Trees for 3rd-semester students, including a 5-minute warm-up activity, key teaching points, and a closing exercise."
4. In the same chat, ask:
   > "Now create a 10-question quiz (mix of MCQ and short answer) based on that lesson, with an answer key."
5. Ask Claude to turn the quiz into a clean, printable format:
   > "Format that quiz as a document I can print — with space for student name and roll number at the top."
   - Claude will render this as an **Artifact** (a document panel on the right). Show staff the **Copy** and **Download** buttons on the artifact.
6. Ask for a grading rubric:
   > "Create a rubric to grade the short-answer questions out of 5 marks each."

### Try it yourself
Pick your own subject and repeat steps 3–6 for a topic you actually teach next week.

### Teaching point
Everything stays inside the Project, so next week you can open the same Project and ask "What did we cover last time?" — Claude can see the chat history in that Project.

---

## Lab 2 — Building a Student/Staff Survey
**Goal:** Design a feedback or research survey, then turn it into an actual fillable form using Artifacts.

**Time:** 25 minutes

### Steps
1. Start a new chat and ask:
   > "I need to survey final-year students about their placement readiness. Draft 10 survey questions — a mix of rating scale, multiple choice, and one open-ended question."
2. Review the questions together as a class; ask Claude to refine one:
   > "Make question 4 less leading and more neutral."
3. Now ask Claude to build it as an interactive web form:
   > "Turn this into a simple HTML survey form with a submit button that shows a 'Thank you' message. Keep it clean and mobile-friendly."
   - This produces an **HTML Artifact** — a working form rendered right in the browser.
4. Show staff how to **Download** the HTML file (saves to `~/Downloads` on Ubuntu by default) — they can open it with any browser or host it on a college webpage.
5. Practical note: since the free tier can't send form data to a live database, mention the realistic path — use this Claude-drafted question set inside **Google Forms** or **Microsoft Forms** for actual data collection. Claude is best used here as the *question designer*, not the data collector.

### Try it yourself
Design a 5-question survey for something relevant to your department (lab feedback, event feedback, alumni survey).

---

## Lab 3 — Research & Literature Assistant
**Goal:** Use Claude's web search to speed up literature review, technical research, and staying current — while respecting copyright (no copy-pasting full paper text).

**Time:** 25 minutes

### Steps
1. Turn on the **web search toggle** if it isn't already on.
2. Ask a research question relevant to their field, e.g.:
   > "What are the recent developments in edge AI for IoT devices in 2026? Summarize the key trends with sources."
3. Point out that Claude cites sources — click a citation to see where information came from.
4. Upload a PDF (have staff bring a sample research paper, or use one from `/usr/share/doc` as a stand-in): drag it into the chat, or use the paperclip icon.
5. Ask:
   > "Summarize this paper's methodology and findings in plain language. What are its limitations?"
6. Ask Claude to compare two papers (upload a second PDF):
   > "Compare these two papers' approaches and tell me which is more relevant to a low-power embedded systems course."
7. Important teaching point: Claude **will not** reproduce large verbatim chunks of copyrighted text (this is by design) — it paraphrases and summarizes instead. This is actually good academic practice to model for students too.

### Try it yourself
Research a topic for an upcoming guest lecture or department seminar and ask Claude to draft a one-page briefing note with sources.

---

## Lab 4 — Writing Blog Posts & Department Content
**Goal:** Draft blog posts, newsletters, or event announcements for the college website/social media, with a consistent voice.

**Time:** 20 minutes

### Steps
1. Start a new chat:
   > "Write a 400-word blog post about our department's robotics workshop held last week. Tone: engaging and student-friendly, for our college website."
2. Iterate on tone:
   > "Make it punchier — add a strong opening line and end with a call-to-action for students to join the robotics club."
3. Ask for a variant for a different channel:
   > "Now rewrite that as a 3-line LinkedIn post announcing the same event."
4. Show staff the **Artifact** panel again if the post is long — it can be exported as a `.md` or copied directly into a CMS/WordPress editor.
5. Optional: set up a **Project** called "College Communications" with instructions like:
   > "Always write in a warm, professional tone suited to an engineering college audience. Avoid jargon. Keep paragraphs short."
   This keeps every future blog/announcement consistent without re-explaining tone each time.

### Try it yourself
Write a short announcement for a real upcoming event in your department.

---

## Lab 5 — Working with Real Files: Attendance, Marks & Reports
**Goal:** Upload everyday spreadsheets/PDFs (attendance sheets, marks lists, question papers) and have Claude analyze or reformat them — a huge time-saver for administrative work.

**Time:** 25 minutes

### Steps
1. Have each participant open the Ubuntu **Files** app, and locate or create a simple `.csv` file (e.g., a mock attendance sheet with columns: Roll No, Name, Days Present, Total Days).
2. Upload the CSV into a new Claude chat using the paperclip icon.
3. Ask:
   > "Calculate the attendance percentage for each student and list anyone below 75%."
4. Ask for a written summary:
   > "Write a short summary I can send to the HOD about overall class attendance this month."
5. Try a second file type — upload a scanned/PDF question paper and ask:
   > "Check this question paper for any duplicate or unclear questions."
6. Show that Claude **cannot** save changes back into the original Excel/Word file on the free plan the way Pro users can with Claude for Excel/Word — but it can generate a clean replacement version as text or an Artifact, which they then re-save manually via **File → Save As** in LibreOffice Calc/Writer (pre-installed on most Ubuntu college labs).

