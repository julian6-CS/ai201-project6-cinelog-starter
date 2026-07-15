# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->
AI was not used in this project and I instead relied on YouTube videos or the slides provided by the class to learn more about Git, specifically git rebase.

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
Nothing conflicted while I rebased, in all honestly, when I rebased it erased a portion of WatchListEntry within the models.py and I panicked since I thought it was unintentional. Afterwards, I investigated the main github branch and I saw that model.py was changed to reflect it. I also investigated the git log to see that the rebase worked as expected and it did.
**How I resolved it:**
Git automatically erased the WatchListEntry portion of the code in models.py
**How I verified no conflict remains:**
I verified that no conflicts remained by trying to produce another commit by altering another file, if there were any conflicts present it would've been represented as I tried to commit to the branch.

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->

Watchlist Feature OverView:

The watchlist features functions in the same manner as it does in platforms like LetterBoxd or IMDb, where a user can first query for all the eligible films in the database by calling get_films to get a list of all the registered films from the database. The user can then add any of these films to a watchlist registered specifically for them by calling the add_to_watchlist() function or visiting the "/<user_id>/add" POST endpoint and providing the specific films id. This film is then entered into their watchlist, where they can later examine these changes or their many entries by calling on the get_watchlist function or visiting the "/<user_id>" GET endpoint to receive the long list of film entries entered in their watchlist.

Design Decisions:

I made two import design decisions for the implementation of the project. The first decision is advocating for WatchlistEntries to have their public variable set to false by default since the current project archetecture reafirms the private nature of creating a watchlist and that it's industry precedent established by media platforms like YouTube or IMDb to set watchlists private by default so the user expects this behavior. I also advocated for the output of get_watchlist to be ordered by the added_date since it is more aligned with the intended use case of providing a list of user picked media options that users utilize at a later date to base their media choices on, the more recent additions are more aligned with the users current tastes and have a higher likelyhood of being chosen since the user is more likely to remember it.

Testing Instructions:

First and foremost, there are some very thorough tests provided in the test folder which can all be run by putting "pytest tests/ -v" into your powershell. In addition to this, any tests can be created within the test_watchlist.py file by utilizing the established app instance to write any desired test. Related to testing, a new python file can be created where it imports the create_app(), calls the function and saves the return value to a variable named app. To then use "with app.app_context():" statement to query into the database and call the functions without engaging with the endpoints. Personally, within the app context I added a couple films to the database and custom made user to the database by creating the corresponding object and adding it by calling db.session.add(object); db.session.commit(). With these object, I was able to call the fuctions within watchlist_service.py by providing the id value corresponding to the created objects to add_to_watchlist() and then calling get_watchlist() to see if the change was reflected. In addition to this method, the apps server endpoints can be called by providing using the curl function. First you must run the app by using the command "python app.py" to run the server. Then, using gitbash you can run curl command that follow this format of "curl -X (POST | GET| DELETE) local_url_of_the_app.py_server/ (desired endpoint | watchlist)/(User_ID)/(add | nothing) -H "Content-Type: application/json" \-d '{"film_id": (film_id)}' " to complete command. Using curl -X POST (url)/watchlist/(user_id)/add -H "Content-Type: application/json" -d '{"film_id": (film_id)}' to add a specific movie to the watchlist and then using curl -X GET (url)/watchlist/(user_id) to receive the users' watchlist to verify the changes taken place.