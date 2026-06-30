**1. Vague vs specific prompting**

Vague (what most first-timers type): "Make some notes on data structures."

Specific (what gets a usable draft): "Create lecture notes on Binary Search Trees for second-year Computer Engineering students who already know arrays and linked lists. Include: a real-world analogy, time complexity for insert/delete/search, one worked example with a diagram description, and 3 practice questions with answers. Keep it to one A4 page, formal academic tone."

The point to make live: run both prompts in front of them and let them see the gap themselves — that's more convincing than you explaining it.

**2. Reusable template habit**

Instead of re-explaining context every time, they save a "skeleton prompt" like:

"You are helping me create [lecture notes / question paper / lab manual] for [subject] for [year/branch] students. Audience level: [beginner/intermediate]. Format: [bullet notes / step-by-step / Q&A]. Length: [X pages/minutes]. Topic: [insert topic]. Always include a real-world example relevant to Indian engineering industry context."

They just swap the bracketed parts each time instead of rewriting from scratch — saves 5+ minutes per use.

**3. Iterate instead of one-shot perfection**

Turn 1: "Draft 5 exam questions on thermodynamics, second law, for mechanical engineering finals."
Turn 2: "Make question 3 numerical instead of theoretical."
Turn 3: "Add marking scheme for each question, 10 marks total per question."
Turn 4: "Now generate a second equivalent set for the re-exam, same difficulty, different numbers."

Point to make: this is faster than trying to cram everything into one perfect prompt, and it mirrors how they'd brief a TA.

**4. Uploading reference material instead of generating blind**

Bad: "Write me a question paper on Operating Systems."

Good: upload last year's OS question paper PDF + the syllabus PDF, then prompt: "Using the attached previous paper as a style and difficulty reference and the attached syllabus for topic coverage, generate a fresh question paper for this semester. Keep the same marks distribution and paper pattern, but don't repeat exact questions."

This is the one I'd spend the most demo time on — professors immediately see that Claude is using *their* material, not inventing generic content, and it's a strong "this could actually save me hours" moment.

Want me to turn these four into a one-page handout they can keep, or build a live demo artifact (like the template-prompt) they can copy-paste during the session?




Useful for the professors specifically since they'll likely ask about this during your session. Important framing upfront: most journals/conferences now require disclosure of AI tool use, and Claude should be positioned as a drafting and editing assistant — not a literature source or co-author. Worth stating that explicitly to avoid integrity issues.

**Where Claude genuinely helps**

*Structuring and outlining* — give Claude your research idea, methodology, and key results, and ask for a standard IMRaD structure (Introduction, Methods, Results, Discussion) tailored to the target journal/conference's typical format. This turns a blank page into a skeleton in minutes.

*Improving clarity and language* — paste a rough paragraph and ask Claude to tighten it for academic tone, fix grammar, or reduce word count to fit a page limit, while preserving technical meaning. Good prompt: "Rewrite this paragraph in formal academic English, keep all technical claims unchanged, reduce to under 150 words."

*Abstract and title polishing* — abstracts are often the hardest part to get crisp; feed Claude the full paper draft and ask for 2-3 abstract options at different lengths (150/250 word limits are common).

*Literature review drafting from their own sources* — this is the critical caveat: have them upload the actual papers (PDFs) they want cited, and ask Claude to synthesize and compare those specific papers. Never ask Claude to "find relevant papers and cite them" from memory — it can hallucinate citations or get details wrong. Real citation discovery should go through Google Scholar/IEEE Xplore/etc., or Claude's web search with verification.

*Response to reviewer comments* — drafting polite, structured responses to peer review feedback, point by point, is a big time-saver and something Claude is genuinely strong at.

*Formatting* — converting a draft into a specific format (IEEE, Springer, ACM templates) or generating LaTeX/Word with correct section numbering.

**Critical guardrails to teach explicitly**

Never let Claude invent data, results, or citations — it should only rephrase, structure, or analyze what they provide. Tell them to treat any factual claim or citation Claude produces without their source material as unverified until checked.

Disclose AI assistance per the target venue's policy — many journals (IEEE, Elsevier, Springer) now require an AI-use statement in acknowledgments.

Keep authorship and intellectual contribution human — Claude can sharpen prose and structure, but the ideas, methodology, and conclusions need to be theirs, both for integrity and because reviewers can usually tell when content is generic AI filler.

**One practical workflow to demo**

Draft methodology section in bullet points (human) → ask Claude to expand into formal prose → upload 2-3 related papers and ask Claude to draft a comparison table or related-work paragraph from those specific sources → human verifies every citation against the original PDF → Claude polishes language pass at the end.

Want me to put together a one-page "Claude for paper writing — do's and don'ts" handout, or draft a sample prompt set for each paper section (intro, related work, methodology, conclusion)?
