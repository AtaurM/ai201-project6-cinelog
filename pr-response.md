# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` to match the project's `verb_to_noun` convention used by `add_to_collection()` / `remove_from_collection()` / `get_collection()` in `services/collection_service.py`. Updated the call in `routes/watchlist/watchlist.py` (both the import and the call inside `add_film()`).
**How I verified:** Ran a project-wide search (`grep -rn "save_to_watchlist" --include="*.py" .`) after the rename and confirmed zero remaining references anywhere in the codebase. The only caller was the route handler, which is now updated. Also re-ran the full test suite (`pytest tests/ -v`) to confirm nothing else depended on the old name.

## Comment 2 — Deduplication
**What I did:** Followed the exact pattern `add_to_collection()` uses in `services/collection_service.py`: before inserting a new `WatchlistEntry`, query for an existing `(user_id, film_id)` pair (`WatchlistEntry.query.filter_by(user_id=user_id, film_id=film_id).first()`), and if one exists, raise a new `AlreadyInWatchlistError` instead of inserting. I added `AlreadyInWatchlistError` as a sibling to `AlreadyInCollectionError` rather than reusing that class, since it's watchlist-specific and callers need to be able to catch it independently. In `routes/watchlist/watchlist.py`, I wrapped the `add_to_watchlist()` call in a try/except and mapped `FilmNotFoundError` → 404 and `AlreadyInWatchlistError` → 409, matching the status codes `routes/collection.py` already uses for the equivalent errors.

Note: unlike `CollectionEntry`, `WatchlistEntry` doesn't have a `UniqueConstraint("user_id", "film_id")` at the DB level, so the application-level check is the only thing preventing duplicates. I left the DB schema as-is to keep this change scoped to what the comment asked for, but a follow-up could add the constraint for defense-in-depth.
**How I verified:** Compared side-by-side with `add_to_collection()` (services/collection_service.py:27-58) before writing the check, to confirm the query shape and exception-then-return ordering matched. Ran the full test suite (`pytest tests/ -v`) to confirm the existing collection tests still pass and nothing regressed; the watchlist-specific duplicate test is added in Comment 3/stretch work.

## Comment 3 — Missing test
**What I did:** Created `tests/test_watchlist.py`, modeled directly on `tests/test_collection.py`. I reused the same `app`, `sample_user`, and `sample_film` fixtures verbatim (same in-memory `sqlite:///:memory:` app factory pattern), then wrote `test_add_to_watchlist_nonexistent_film_raises`, which is a direct port of `test_add_to_collection_nonexistent_film_raises`: it calls `add_to_watchlist()` with a well-formed but nonexistent `film_id` (`"00000000-0000-0000-0000-000000000000"`) and asserts `FilmNotFoundError` is raised via `pytest.raises`. I also added `test_add_to_watchlist_creates_entry` as a basic happy-path check (mirroring `test_add_to_collection_creates_entry`) so the new test file isn't just testing the one failure case in isolation.
**How I verified:** Ran `pytest tests/test_watchlist.py -v`. Both tests pass. Then ran the full suite `pytest tests/ -v` (6 tests total across both files) to confirm no regressions.

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