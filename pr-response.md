# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:**
Renamed save_to_watchlist() in watchlist_service.py and all calls to it found in watchlist.py to add_to_watchlist() to be more aligned with the established naming convention.
**How I verified:**
Utilized the global search shortcut within my IDE to search for all instances of save_to_watchlist() as well as checking manually to ensure that there were no calls left.

## Comment 2 — Deduplication
**What I did:**
The bulk of the deduplication logic is present in watchlist_service.py and designed to resemble the add_to_collection() workflow, within add_to_watchlist the logic queries to the database for any WatchlistEntry with the same film or user_id. Much like add_to_collection, if there already is an entry with the same parameters an AlreadyInWatchlist exception will be raised so the caller can catch it. Within collection.py, any caller to the add_to_collection must wrap the call in a try catch statement, this is mimicked in watchlist.py where it also returns an error to the user if an exception is raised.

**How I verified:**
Prior to implementing the change, I triggered the error by adding the same film to a watchlist twice and then requesting the watchlist entries to see if there are any 
duplicates. After the change, I tried to trigger the same bug and I received an exception when trying to add the same film a second time to the same watchlist.

## Comment 3 — Missing test
**What I did:**
Created a test file named "test_watchlist.py" within the tests folder called "test_add_to_watchlist_nonexistent_film_raises" whose behavior is modeled after the "test_add_to_collection_nonexistent_film_raises" in test_collection.py. The test aims to determine whether adding a nonexistent film using the add_to_watchlist raises the expected FilmNotFoundError as a response. This mimicks the test in test_collection since they both examine whether the expected exception is raise by placing the error case within "with pytest.raises(FilmNotFoundError)" logic.

**How I verified:**
I ran the test to verify to confirm that it was working as intended and that it raised the expected exception/error.

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