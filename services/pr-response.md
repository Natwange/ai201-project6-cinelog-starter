# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->
**Verified with AI whether I changed all the save_to_watchlist function defs to add_to_watchlist**


## Comment 1 — Rename
**What I did: changed save_to_watchlist to add_to_watchlist** 
**How I verified: I used find all refernces and Ctlr + F and ran pytest tests/test_collection.py::test_add_to_collection_creates_entry -v**

## Comment 2 — Deduplication
**What I did: Imported AlreadyInCollectionError from services.collection_service to prevent a film being added to a collection twice. A variable existing queries the CollectionEntry table with .filter_by(user_id, film_id).first(), returning the matching collection entry or None. If existing holds an entry (i.e. the user has already logged this film), AlreadyInCollectionError is raised.**
**How I verified: by running pytest tests/test_collection.py::test_add_to_collection_duplicate_raises -v**

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