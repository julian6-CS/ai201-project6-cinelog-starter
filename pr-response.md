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
duplicates. After the change, I tried to trigger the same bug and I received an exception when trying to add the same film a second time to the same watchlist. I did this specifically by making a file similar to test_watchlist.py that also calls the add_to_watchlist within the pytest context, but it instead ensures that a AlreadyInWatchlistError is raised when calling add_to_watchlist with the same film id "padington". I didn't this file to my commit since it would only serve as a point of confusion when verifying the commit history.

## Comment 3 — Missing test
**What I did:**
Created a test file named "test_watchlist.py" within the tests folder called "test_add_to_watchlist_nonexistent_film_raises" whose behavior is modeled after the "test_add_to_collection_nonexistent_film_raises" in test_collection.py. The test aims to determine whether adding a nonexistent film using the add_to_watchlist raises the expected FilmNotFoundError as a response. This mimicks the test in test_collection since they both examine whether the expected exception is raise by placing the error case within "with pytest.raises(FilmNotFoundError)" logic.

**How I verified:**
I ran the test to confirm that it was working as intended and that it raised the expected exception/error.

## Comment 4 — Default visibility
**My position:**
I choose to change the default to public=false within the models.py file.


**Reasoning:**

From a user standpoint, the expected behavior from a watch list is for it to be a private list where a user can keep track of media they would like to consume in the near future. This behavior is shown in this application since any form of interaction with a watchlist or its' entries is tied directly to a user and there is no workflow for a collaborative effort from other users. The established workflows imply a more personal relationship between the user and their watchlist. This behavior is expected by the user since it's established by other media platforms like YouTube whose Watch Later list is set to private or IMDb whose watchlist is also set to private by default. This choice maximizes the users' privacy and more closely aligns with the users' expectations.

**Tradeoff acknowledged:**

The biggest tradeoff is that setting entries to public by default would allow for a greater user-to-user interaction and makes sharing a watchlist easier, making social discovery another reason to engage with this platform since it can be used to find other users' with similar tastes. As a user, there are already so many movie or show tracking platforms like imdB that makes this application null. A more social approach would give prospective users more of a reason to engage with the platform. These features would be a great addition in the future, but as the application currently stands it makes the most sense to set the default to private. Therefore, I believe prioritizing user privacy to be more appropriate given the current design of the application.

## Comment 5 — Sort order
**My position:**
I agree that the watchlist should be ordered by date added by default rather than ordering it alphabetically.

**Reasoning:**
The primary and expected purpose of a watchlist is to act as a queue of media that a user intends to watch at a later date rather than a catalogue tracking their movie tastes.
The interaction of tracking a users' movie tastes or potential tastes is already provided by the collections workflow, a dedicated tool provided by this application where a user can rate past media and reflect on their tastes. When a user returns to a watchlist, they are often deciding what to watch next rather than finding a specific title in their list. Alphabetical ordering is still useful when a user wants to find a specific film in their large list, but I belive this a secondary use case compared to the expected value of a watchlist as helping users decide what to watch next. This decision optimizes the watchlist for assiting users with decision making rather than retrieving entries in their list.

**Engagement with reviewer's point:**

I agree with the mantainers observation that "Most users want to see what they added recently" since it supports the idea that a watchlist is meant to functions as a tool to aid decision making. Displaying the newest entries to the watchlist first makes it more likely that the user still recognizes the media and can base their decision much easier in comparison. If an older entry were shown at the top, it would be more likely that a user would forget about this entry and would instead have to research the entry, making the barrier for entry much higher. This behavior is present in established platform implementations of the watchlist, like Letterboxd which by default orders entries by date in descending order instead.

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->