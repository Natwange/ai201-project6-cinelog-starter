# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->
**Verified with AI whether I changed all the save_to_watchlist function defs to add_to_watchlist**


## Comment 1 — Rename
**What I did: Renamed save_to_watchlist → add_to_watchlist and updated every call site.**
**Call sites checked/updated:**
**services/watchlist_service.py — the function definition**
**routes/watchlist/watchlist.py — the import and the call inside the POST /watchlist/<user_id>/add handler** 
**How I verified: Used Find All References (Shift+F12) on the function and Ctrl+Shift+F (Find in Files) to search the whole project for the old name save_to_watchlist — 0 matches remain, confirming no call site was missed. Confirmed the new name resolves correctly by running pytest tests/test_watchlist.py -v, which imports and calls add_to_watchlist — 1 passed.**

## Comment 2 — Deduplication
**What I did: Imported AlreadyInCollectionError from services.collection_service to prevent a film being added to a collection twice. A variable existing queries the CollectionEntry table with .filter_by(user_id, film_id).first(), returning the matching collection entry or None. If existing holds an entry (i.e. the user has already logged this film), AlreadyInCollectionError is raised.**
**How I verified: by running pytest tests/test_collection.py::test_add_to_collection_duplicate_raises -v**

## Comment 3 — Missing test
**What I did: I added tests/test_watchlist.py with test_add_to_watchlist_nonexistent_film_raises, mirroring the collection test's fixtures and assertions.**
**How I verified: pytest tests/test_watchlist.py → 1 passed.**

## Comment 4 — Default visibility
**My position: Watchlists should default to public=True. This is an intentional choice for CineLog specifically, not an inherited framework default.**

**Reasoning: CineLog is a community film-tracking app, so I'm optimizing for discovery and social engagement. A new user's account starts empty.The fastest way to give them value is letting them browse what others are planning to watch and pull ideas into their own list. Public-by-default makes that the norm rather than the exception: if visibility defaulted to private, the shared library that makes the app worth joining would stay mostly empty even as users added films, because most people never change a default. Defaulting to public keeps the community feed populated and makes the social loop (browse → discover → add) work out of the box.**

**Tradeoff acknowledged: The cost is user privacy. Defaulting to public means a user could expose their viewing intentions before realizing their list is visible. Someone may treat a watchlist as private by assumption. A private-by-default option would protect that user but at the expense of a near-empty community view. I think public-by-default is the right call for this product, and the risk is bounded because public is a per-entry field (models.py:80) . Users can mark individual films private rather than being locked in. If we later add sensitive-content handling or an account-level "private profile" mode, this default is worth revisiting.**

## Comment 5 — Sort order
**My position: Keep the watchlist default sorted alphabetically by title. (No code change — get_watchlist already orders by Film.title.asc().)**

**Reasoning: A watchlist is a reference list you return to, not a feed you scroll once. Its whole purpose is "I've got two hours free — what did I mean to watch?" That's a lookup task, and alphabetical order gives every film a stable, predictable position: if a title starts with "Ab," the user knows roughly where it sits without scanning the whole list or opening search. This advantage compounds as the list grows — at 50+ films, date-added order becomes an undifferentiated pile where the only way to find a specific title is to remember when you added it.**

**Engagement with reviewer's point: You're right that recency matters — for the newest couple of entries, and right after adding something. But I'd push back on it being the default for two reasons. First, that recency need is transient (it fades once the film has sat on the list a day), while the findability need is permanent and grows with the list. Second, "what did I add recently" is a narrower question than "where is the film I'm looking for," which alphabetical answers better for a longer watchlist. So I think we're weighing a strong-but-short-lived benefit against a modest-but-durable one, and I'd favor the durable one for the default.**

**If you'd still prefer recency (a fair call), the cleanest resolution is to keep alphabetical as the default display order but add a sort toggle (?sort=date_added) so users get recency on demand — that honors your use case without making it the default. Happy to implement that instead if you'd rather.**

## Comment 6 — Rebase
**What conflicted: .gitignore (a trivial add/add overlap). The UUID change did not surface as a Git conflict — instead, main's rewrite of models.py silently dropped my WatchlistEntry class during the rebase.**
**How I resolved it: Resolved .gitignore by keeping all entries. Then caught the silent loss when pytest failed with ImportError: cannot import name 'WatchlistEntry'. Re-added the WatchlistEntry model with film_id as db.String(36) (UUID) to match main's refactor, and updated the watchlist docstrings from int to UUID.**
**How I verified: git log --oneline --graph shows a linear history with no merge commits; pytest -v → 5 passed.**
## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->