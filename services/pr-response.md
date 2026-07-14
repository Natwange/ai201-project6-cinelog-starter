# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->
**Verified with AI whether I changed all the save_to_watchlist function defs to add_to_watchlist**


## Comment 1 — Rename
**What I did: Renamed save_to_watchlist → add_to_watchlist and updated every call site.
Call sites checked/updated:
services/watchlist_service.py — the function definition
routes/watchlist/watchlist.py — the import and the call inside the POST /watchlist/<user_id>/add handler 
**How I verified: Used Find All References (Shift+F12) on the function and Ctrl+Shift+F (Find in Files) to search the whole project for the old name save_to_watchlist — 0 matches remain, confirming no call site was missed. Confirmed the new name resolves correctly by running pytest tests/test_watchlist.py -v, which imports and calls add_to_watchlist — 1 passed.**

## Comment 2 — Deduplication
**What I did: Imported AlreadyInCollectionError from services.collection_service to prevent a film being added to a collection twice. A variable existing queries the CollectionEntry table with .filter_by(user_id, film_id).first(), returning the matching collection entry or None. If existing holds an entry (i.e. the user has already logged this film), AlreadyInCollectionError is raised.**
**How I verified: by running pytest tests/test_collection.py::test_add_to_collection_duplicate_raises -v**

## Comment 3 — Missing test
**What I did: I added tests/test_watchlist.py with test_add_to_watchlist_nonexistent_film_raises, mirroring the collection test's fixtures and assertions.**
**How I verified: pytest tests/test_watchlist.py → 1 passed.**

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