📬 Reminder: Project 6 is due by Monday, July 13th at 2:59AM EDT.
Show What You Know: CineLog
⏰ ~3 hours

Contributing to an open source project is participating in a professional conversation. Maintainers push back. Reviewers ask questions. Design decisions need to be defended in writing. Conflicts happen because the codebase didn't wait for you.

In this project, you'll step into the role of a contributor who has already submitted a pull request to CineLog, a community film tracking app. A maintainer named @dev-lead has reviewed your PR and left six comments — some are straightforward fixes, one requires a written design decision, and one is a genuine disagreement you're allowed to push back on. While your PR was open, a refactor also merged to main that creates a real conflict you'll need to resolve.

Your job: address all six comments, resolve the conflict, and rewrite your commit history to meet the project's standards before resubmitting.

🎯 Goals
By completing this project, you will be able to:

Respond professionally to code review feedback, including feedback you disagree with.
Navigate an unfamiliar codebase to understand context before making changes.
Resolve a real merge conflict using rebase.
Rewrite commit history into clean, conventional commit format.
Make and document design decisions under the pressure of a real review cycle.

✅ Features
Required Features

Address all six review comments: For each comment, make the required code change or write the required decision. Document every response in your PR Response Doc — not just what you did, but why.


Rebase on updated main: While your PR was open, a refactor merged to main that changed film IDs from integers to UUIDs. Rebase your feature/watchlist branch on the updated main and resolve the conflict. Your final branch must have no merge commits.


Rewrite commit history: After addressing all six comments, use an interactive rebase to rewrite your commit history to conventional commit format. Each commit should represent one logical change.


Write a PR Response Doc: A file named pr-response.md committed to the root of your repo that covers all six comments — what you did, your reasoning for each code change, and your written arguments for the two design decisions (Comments 4 and 5).

Stretch Features

Add remove_from_watchlist(): Following the project's existing naming and deduplication patterns, implement a remove_from_watchlist(user_id, film_id) function. Include a test for it and document it in your PR Response Doc.


Write a second test: Write an additional test for the watchlist that wasn't requested in the review — your choice of edge case. Briefly explain why you chose that case in your PR Response Doc.


Add a visibility toggle: Add a public parameter to the add_to_watchlist() endpoint so callers can set visibility explicitly rather than relying on the default. Document the change in your PR Response Doc.

💡 Hints
Read the codebase before reading the review comments. Understanding the existing patterns — how add_to_collection() works, how tests are structured — makes the comments much clearer. Students who read the comments first often misunderstand what the reviewer is asking.
Read all six comments before addressing any of them. Comments 1–5 are independent, but Comment 6 (the rebase) is easier after the others are stable. Do it last.
Comments 4 and 5 are not code problems — they're design conversations. A one-liner won't earn credit here. Explain the tradeoff, engage with the reviewer's point directly.
For the interactive rebase, git rebase -i HEAD~N lets you edit, rename, and reorder commits. If you've never used it, practice on a scratch branch first — you can always git reflog your way back if something goes wrong.
AI tools can help with orientation (understanding what files do, what existing functions return) and with hygiene (verifying conventional commit format). They can also stress-test your design arguments — write your response first, then ask AI what counterarguments a reviewer might raise. What they cannot do is make the design decisions for you. Comments 4 and 5 require reasoning grounded in CineLog's specific context. If you ask an AI to write either response, it will produce a generic argument. The rubric rewards specificity — and graders who read many of these submissions can tell the difference.

Milestone 1: Set Up, Checkout, and Orient
⏰ ~30–45 min

Before reading any review comments, spend time understanding the codebase. The review comments will make much more sense once you know what add_to_collection() does, how the tests are structured, and where the watchlist code lives. Developers who read the comments first often misunderstand what the reviewer is asking — they don't have the context to see why the naming matters or where the pattern they should follow already exists.


Fork the CineLog starter repo and clone your fork locally.

When forking, uncheck "Copy the main branch only." If you leave this checked, GitHub will only copy main and you won't be able to check out the feature/watchlist branch that you'll need to work on.
After cloning, set up the environment and check out the working branch:

# Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate          # macOS/Linux
# .venv\Scripts\activate.bat       # Windows Command Prompt
# source .venv/Scripts/activate    # Windows Git Bash

pip install -r requirements.txt

# Check out the feature branch where your work lives
git checkout feature/watchlist
Start the app:

python app.py
The app will start at http://127.0.0.1:5000. Note that there is no frontend — opening that URL in a browser returns a 404, which is expected. The app exposes REST API endpoints (e.g., GET /watchlist/<user_id>, POST /watchlist/<user_id>/add) that you test manually with curl or verify through pytest. You'll confirm the app works correctly by running the test suite, not by browsing to a URL.


Add a .gitignore. This starter doesn't ship with one, so your virtual environment, Python caches, and the app's database can end up staged for commit — and this project grades your commit history (Milestone 4). Create a .gitignore in the repo root before you start making changes:

.env
*.db
*.db-journal
instance/
__pycache__/
*.pyc
.venv/
venv/
With it in place before your first commit, your virtual environment, caches, and the database stay out of the commit history you'll rewrite and screenshot in Milestone 4.


Before looking at any review comments, read models.py, services/collection_service.py, and tests/test_collection.py. Note the naming conventions, how deduplication is handled, and how the existing tests are structured.

Where to find the PR review comments: The six review comments live on the open PR in the original CineLog repo — forking copies the code branches, but not the pull request, so you won't see this PR on your fork. Read the comments there: some are inline on specific code lines (visible in the "Files changed" tab), others are general comments in the "Conversation" tab. (@dev-lead is the maintainer persona for this scenario; the comments appear under the repo owner's GitHub account.) You'll make all of your changes on the feature/watchlist branch of your own fork.
🤖 Using AI tools for orientation
You're reading someone else's codebase under the pressure of a review cycle. AI tools can speed up orientation significantly — but the output needs to be verified against the actual code. Three patterns that work well here:

File summary: Give the AI a file's full content and ask: "Summarize what this file is responsible for, what its main functions do, and what other parts of the codebase it depends on." Run this on models.py and the collection service file before reading the comments.
Function explanation: Give the AI a function and ask: "What does this function do? Walk me through what happens at each step, and what it returns if the film_id doesn't exist." Use this on add_to_collection() before implementing your own deduplication in Comment 2.
Test structure: Give the AI the test_collection.py file and ask: "What pattern does each test follow? What do I need to provide to write a test in the same style?" This will help you move faster on Comment 4.
Note: AI explanations require verification. If an AI summary of add_to_collection() doesn't match what the code actually does — trust the code.


Read through all six review comments in their entirety. Before addressing any of them, note what each one requires — a code change, a written response, or a decision. Some comments set up patterns you'll follow for others.

📍 Checkpoint
Your branch is checked out and the app runs. You've read the existing service code and test patterns before looking at the PR comments. You've read all six comments and have a plan for the order you'll address them in.



📄 What is the PR Response Doc?
Create a file named pr-response.md in the root of your repo right now, before you start fixing anything. You'll fill it in as you work through each comment. This file is your written record of the code review — it documents what you changed, why, and your reasoning for the two design decisions.

Minimum structure for pr-response.md:

# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:**
**How I verified:**

## Comment 2 — Deduplication
**What I did:**
**How I verified:**

## Comment 3 — Missing test
**What I did:**
**How I verified:**

## Comment 4 — Default visibility
**My position:**
**Reasoning:**
**Tradeoff acknowledged:**

## Comment 5 — Sort order
**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->
Write entries as you go — don't leave this to the end. Documentation written immediately after a fix is far more accurate than documentation written from memory.



Milestone 2: Address the Code Changes
⏰ ~45 min – 1.5 hours

Comments 1, 2, and 3 require code changes. Address them in this order: rename first (Comment 1), then deduplication (Comment 2), then the missing test (Comment 3). Comment 6 also involves code changes, but it requires a rebase — save it for Milestone 3 after your other changes are stable. Write one commit per change as you go; you'll refine the messages in Milestone 4.


Comment 1 — Rename: Rename save_to_watchlist() to add_to_watchlist() in services/watchlist_service.py and update all call sites (there is one in routes/watchlist/watchlist.py). Use your editor's find-all-references or a project-wide search to confirm you haven't missed any. Commit this change.


Comment 2 — Deduplication: Add deduplication logic to add_to_watchlist() in services/watchlist_service.py. Look at how add_to_collection() in services/collection_service.py handles this — follow the same pattern. Commit this change separately from the rename.

🤖 Using AI tools
If you used AI in Milestone 1 to understand add_to_collection(), you already have what you need. If not, do it now: give the AI the function and ask what the deduplication check does and what it returns when a duplicate is detected. Then write your own version of the check in add_to_watchlist(). Don't ask AI to write the deduplication code for you!


Comment 3 — Missing test: Create a new file tests/test_watchlist.py. Read tests/test_collection.py and find test_add_to_collection_nonexistent_film_raises — write the equivalent test for add_to_watchlist() following the same fixture and assertion structure. Run the test to confirm it passes:

pytest tests/test_watchlist.py -v
Commit your test file.


After each of the three code changes, run the full test suite to confirm nothing is broken:

pytest tests/ -v

For each comment, write your response in pr-response.md — not just "I renamed it," but where you looked to find all call sites, how you verified the deduplication logic works, and which test you used as your model.

📍 Checkpoint
Comments 1, 2, and 3 are addressed with separate commits. pytest tests/ -v passes. You have substantive entries for all three in pr-response.md. You haven't touched Comments 4, 5, or 6 yet.



Milestone 3: Document Your Design Decisions and Rebase
⏰ ~30–60 min

Comments 4 and 5 aren't code problems — they're design conversations. Both require written arguments, not just code. After you've written those responses, address Comment 6 (the rebase). Rebasing is easier when your code changes are already committed and stable.


Comment 4 — Default visibility: Write your response in pr-response.md. Take a clear position on the public=True default. Explain what user behavior you're optimizing for, and acknowledge the tradeoff of the other option. A one-liner won't earn credit here.


Comment 5 — Sort order: Write your response in pr-response.md. You can implement the maintainer's preference (date-added), keep alphabetical, or propose a third option — but you must argue for your choice and directly engage with the maintainer's reasoning, not just state a preference.

🤖 Using AI to stress-test your reasoning (Comments 4 and 5)
These two comments require reasoning specific to CineLog — the platform context, the users, the tradeoffs. Once you've written a draft response, use AI as a devil's advocate before finalizing it:

Give the AI your draft and ask: "What counterargument would a careful code reviewer raise against this position? What tradeoff am I not acknowledging?"

If the AI surfaces something real, revise your response. If it gives you something you'd already addressed, you're in good shape. The goal is to catch gaps in your reasoning before the maintainer does — not to have AI write the argument for you. Your final response must be your own reasoning, grounded in CineLog's context.

Include a brief note in your AI Usage section (see pr-response.md) if you used AI this way — what you asked and what, if anything, you changed as a result.


Comment 6 — Rebase: Fetch the updated main branch and rebase feature/watchlist on it:

git fetch origin
git rebase origin/main
Resolve the UUID conflict by updating your watchlist code to use UUIDs where it still references integer IDs. Confirm no merge commits remain in your branch history.


Document your rebase process in pr-response.md — what conflicted, how you resolved it, and how you confirmed the conflict was fully addressed.

📍 Checkpoint
All six review comments are addressed. pr-response.md has substantive entries for all of them, including reasoned written arguments for Comments 4 and 5. Your branch is rebased on main with no merge commits. You're ready to clean up your commit history.



Milestone 4: Rewrite Commit History and Finalize
⏰ ~45–60 min

Your commit history documents your work as a professional contributor. Rewrite it to be clean, conventional, and readable before submitting. Then write your PR description and do a final review of your PR Response Doc — someone who hasn't seen the codebase should be able to understand what you changed and why from your documentation alone.


Use git rebase -i to rewrite your commit history. Here's how to do it:

# See how many commits are on your branch relative to main
git log --oneline origin/main..HEAD

# Open the interactive rebase editor for all those commits
# Replace N with the number of commits you saw above
git rebase -i HEAD~N
In the editor that opens, you can:

Change pick to reword to rename a commit message
Change pick to squash or fixup to combine commits (if you have "fixed stuff" commits to fold in)
Change pick to drop to remove a commit entirely
Leave pick as-is to keep a commit unchanged
Save and close the editor. Git will walk you through renaming any commits you marked reword. If something goes wrong, git rebase --abort returns you to where you started.

Ensure every commit uses conventional format (feat:, fix:, test:, docs:), represents one logical change, and has a clear, imperative message.

📋 CineLog's Contribution Standards
Use conventional commit format: feat:, fix:, test:, docs: prefixes
Each commit should represent one logical change (rename is one commit, dedup fix is another, etc.)
No merge commits — the final branch must be rebased, not merged
PR description must include: what the feature does, the design decisions you documented, and how to manually test the feature
What an acceptable commit history looks like (your messages will be specific to your actual changes — don't copy these verbatim):

feat: add watchlist model and add_to_watchlist endpoint
fix: rename save_to_watchlist to add_to_watchlist per naming convention
fix: add deduplication check to prevent duplicate watchlist entries
fix: update WatchlistEntry film_id to UUID after main branch refactor
test: add test for nonexistent film_id in add_to_watchlist
docs: add pr-response.md with visibility and sort order decisions

Before taking your screenshot, give your git log --oneline output to an AI tool and ask: "Do these commit messages follow conventional commit format? Are any messages bundling multiple logical changes that should be separate commits?" Use this as a final check — then verify the output yourself against the conventional commits spec before finalizing.


Run git log --oneline and take a screenshot. Your final history should show at least 4 separate commits with conventional messages and no merge commits. Include this screenshot in your pr-response.md.


Push your cleaned-up branch to GitHub:

git push origin feature/watchlist --force-with-lease
(--force-with-lease is safer than --force — it will refuse to push if someone else has pushed to the branch since your last fetch.)


Open a pull request from feature/watchlist → main on your fork if one doesn't already exist. (If it was auto-created when you pushed earlier, it's already there — just check that it points to your fork's main, not the upstream repo.)


Write your PR description directly in the GitHub PR, and also include it at the bottom of your pr-response.md. It must: explain what the watchlist feature does, explicitly name both design decisions you made (visibility default and sort order), and include step-by-step instructions for manually testing the feature.


Fill in the AI Usage section at the top of pr-response.md. Document at least one specific way you used AI tools during this project — codebase orientation, understanding a pattern, stress-testing a design argument, verifying commit format, or another legitimate use. If you didn't use AI at any point, write a brief note saying so and what you relied on instead. If you used AI when drafting your Comment 4 or Comment 5 responses, describe what you asked and how your final argument differs from or builds on what the AI returned.


Review your entire pr-response.md one more time. Check that all six comments have substantive entries and your AI Usage section is complete. A grader who hasn't seen the codebase should be able to understand what you changed and why from your responses alone.

📍 Checkpoint

git log --oneline screenshot (in pr-response.md) shows at least 4 commits with conventional format and no merge commits. Your PR is open from feature/watchlist → main. Your PR description covers feature overview, both design decisions, and testing instructions. pr-response.md has complete entries for all six comments and includes an AI Usage section.

📬 Submitting Your Project
Submit all of the following through the Course Portal:

Link to the feature/watchlist branch of your forked GitHub repository — the URL must include the branch, e.g. https://github.com/<your-username>/ai201-project6-cinelog-starter/tree/feature/watchlist (with an open PR from feature/watchlist → main). A bare repository link points the grader at main, which won't contain your changes.
pr-response.md (committed to the root of your repo) containing:
Your response to each of the six review comments — what you did and why
Your written explanation for Comment 4 (default visibility decision and reasoning)
Your position on Comment 5 (sort order) and the argument for your choice
Your PR description: what the watchlist feature does, both design decisions made, and step-by-step instructions for manually testing the feature
An AI Usage section: at least one specific use of AI tools during the project, OR a brief note that AI was not used. If AI was consulted on Comment 4 or Comment 5 responses, describe what was asked and how the final reasoning differs from the AI's output.
Screenshot of git log --oneline on your feature/watchlist branch (included in pr-response.md) showing your rewritten conventional commits with no merge commits
🗺️ How It's Graded
A detailed breakdown of graded features and points can be found on the course grading page.
