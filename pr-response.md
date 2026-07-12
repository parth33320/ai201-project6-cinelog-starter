# PR Response Doc — CineLog Watchlist Feature

## AI Usage
During this project, AI was utilized for:
1. **Codebase Orientation:** Summarized and analyzed existing patterns in `services/collection_service.py` and `tests/test_collection.py` to ensure high fidelity to the CineLog architecture.
2. **Design Stress-Testing:** We used AI as a "Planning Critic" to stress-test our design decisions for Comments 4 and 5. The AI challenged our "private-by-default" and "newest-first" arguments by presenting counterarguments (e.g., public watchlists fostering social collaboration, alphabetical sorting aiding in quick lookup). This prompted us to address the tradeoffs explicitly by highlighting how our optional visibility toggle and recency-bias arguments resolve those counterarguments.
3. **Git Hygiene Verification:** Assessed the final proposed commit messages to confirm full compliance with the Conventional Commits specification.

---

## Comment 1 — Rename
* **What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` to align with the project's verb_to_noun convention.
* **How I verified:** I conducted a project-wide search (`git grep` and project-wide text search) for any references to `save_to_watchlist`. The only call site was in `routes/watchlist/watchlist.py`, which was successfully updated to `add_to_watchlist`. No other call sites existed in the codebase. All tests passed.

---

## Comment 2 — Deduplication
* **What I did:** Added a deduplication check inside `add_to_watchlist()`.
* **What it does:** The check queries the `WatchlistEntry` table for an existing record with the same `user_id` and `film_id`. If one is found, it raises a custom `AlreadyInWatchlistError`. The API endpoint catches this exception and returns a `409 Conflict` response with an informative error message, preventing duplicate records.
* **Pattern reference:** This pattern is directly modeled on the existing `add_to_collection()` function in `services/collection_service.py`, which raises `AlreadyInCollectionError` when a duplicate watched film is detected.

---

## Comment 3 — Missing test
* **What I did:** Created a new test file `tests/test_watchlist.py` containing `test_add_to_watchlist_nonexistent_film_raises`.
* **What it checks:** It verifies that attempting to add a nonexistent film (using a fake UUID string) raises a `FilmNotFoundError` instead of failing with a database foreign key constraint error.
* **Existing model:** This test was modeled after `test_add_to_collection_nonexistent_film_raises` in `tests/test_collection.py`.

---

## Comment 4 — Default visibility
* **My position:** Default to **Private (`public=False`)**.
* **Reasoning:** A "Watchlist" represents a user's unpolished backlog and future intent. Unlike a "Collection"—which serves as a public log of films a user has already watched to showcase taste and social proof—a watchlist is highly personal. Defaulting it to private protects users from social scrutiny over their aspirational viewing list (which might contain guilty pleasures or unreleased films) and lowers the friction to adding films.
* **Tradeoff acknowledged:** While defaulting to public would optimize for collaborative social features (e.g., friends suggesting what to watch next together), prioritizing user privacy by default is preferable. We mitigate the tradeoff by providing a visibility parameter toggle so users can explicitly choose to make an entry public if desired.

---

## Comment 5 — Sort order
* **My position:** Default to **Date Added (Newest First)**.
* **Reasoning:** Watchlists are dynamic lists where users frequently add films they have recently discovered and want to watch soon. Sorting newest-first optimizes for this "Recency Bias," allowing users to instantly see and select their most relevant interests. Alphabetical sorting creates a high-density catalog experience where new items are buried under A–Z metadata, requiring unnecessary scrolling.
* **Engagement with reviewer's point:** I agree with @dev-lead that date-added sorting matches modern tracking behavior. In an active film tracking community like CineLog, a chronological queue provides superior utility compared to a flat alphabetical index. However, to keep the history clean and flexible, we chose date-added descending as our default, directly engaging and agreeing with the maintainer's recommendation.

---

## Comment 6 — Rebase
* **What conflicted:** A refactor on `main` migrated film IDs from integers to UUID strings (`db.String(36)`), which conflicted with our branch's integer-based `film_id` column in `WatchlistEntry`.
* **How I resolved it:**
  1. Ran `git fetch origin` and `git rebase origin/main`.
  2. Updated the `film_id` column in `WatchlistEntry` to be a `db.String(36)` referencing `film.id`.
  3. Resolved merge conflicts in `app.py` (registering our blueprint cleanly) and `services/collection_service.py` (using `db.session.get`).
  4. Updated test fixtures in `tests/test_watchlist.py` to use UUID-formatted strings rather than integer IDs.
* **How I verified no conflict remains:** Ran `pytest tests/ -v` which verified 100% of the test suite (11 tests) pass.

---

## Stretch Features
### 1. Remove from Watchlist
* **Implementation:** Implemented `remove_from_watchlist(user_id, film_id)` in `services/watchlist_service.py`. It queries for the watchlist entry; if none is found, it raises a custom `NotInWatchlistError` (which the route catches and returns as a `404 Not Found`). If found, it deletes the entry.
* **Tests:** Written and verified in `test_remove_from_watchlist_success` and `test_remove_from_watchlist_not_found_raises`.

### 2. Second Test
* **Details:** Added `test_get_watchlist_returns_sorted_order` in `tests/test_watchlist.py` to verify that our sorting decision (Comment 5) of newest-first is perfectly respected by the retrieval service.

### 3. Visibility Toggle Endpoint
* **Details:** Updated `POST /watchlist/<user_id>/add` to check for an optional `public` boolean key in the JSON request. Callers can supply `{"film_id": "<uuid>", "public": true}` to create a public watchlist entry. If omitted, it defaults to `False` (Private). Covered in `test_add_to_watchlist_respects_visibility_param`.

---

## PR Description

### Feature Overview
The Watchlist feature allows users to save films they intend to watch in the future. Users can view their watchlist, add new entries with optional public/private visibility, and remove items once watched. It provides a crucial vertical slice for backlog management in CineLog.

### Design Decisions
1. **Default Visibility:** We default watchlists to **Private (`public=False`)** to safeguard user privacy and unpolished backlogs, while letting them opt-in to public sharing.
2. **Sort Order:** We default watchlist retrieval to **Date Added (Newest First)** to optimize for user recency bias, keeping newly added films at the top of their queue.

### Manual Testing Instructions
1. **Add to Watchlist (Private by default):**
   ```bash
   curl -X POST http://localhost:5000/watchlist/<user_id>/add \
     -H "Content-Type: application/json" \
     -d '{"film_id": "<film_uuid>"}'
   ```
2. **Add with Public Toggle:**
   ```bash
   curl -X POST http://localhost:5000/watchlist/<user_id>/add \
     -H "Content-Type: application/json" \
     -d '{"film_id": "<film_uuid>", "public": true}'
   ```
3. **Get Watchlist (Newest first):**
   ```bash
   curl -X GET http://localhost:5000/watchlist/<user_id>
   ```
4. **Remove from Watchlist:**
   ```bash
   curl -X DELETE http://localhost:5000/watchlist/<user_id>/remove \
     -H "Content-Type: application/json" \
     -d '{"film_id": "<film_uuid>"}'
   ```

---

## Git History (Linear History)

```
git log --oneline
545b6ab (HEAD -> feature/watchlist) docs: finalize setup instructions and skills records
1f00808 docs: add pr-response.md with design decisions and feature map
1a51df0 fix: update film_id to UUID format after main refactor
a7e84e8 test: add test for nonexistent film_id in add_to_watchlist
5e4d5b2 feat: implement remove_from_watchlist, visibility toggle, and date-added sorting
b8d0516 fix: add deduplication logic to prevent duplicate watchlist entries
3bdf513 fix: rename save_to_watchlist to add_to_watchlist per naming convention
b2e5759 fix: update film retrieval to use db.session.get
6383e8f (origin/main, origin/HEAD, main) Add files via upload
6b26726 Document grading criteria and project requirements for CineLog
a1bcf27 Create cinelog.md
bbe206c (upstream/main, upstream/HEAD) Merge pull request #2 from ascherj/chore/add-gitignore
718a9a8 chore: add .gitignore for generated files
07ca580 refactor: migrate film IDs from integer to UUID
```
