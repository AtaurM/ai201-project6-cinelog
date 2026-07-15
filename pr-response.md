# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` to match the project's `verb_to_noun` convention used by `add_to_collection()` / `remove_from_collection()` / `get_collection()` in `services/collection_service.py`. Updated the call in `routes/watchlist/watchlist.py` (both the import and the call inside `add_film()`).
**How I verified:** Ran a project-wide search (`grep -rn "save_to_watchlist" --include="*.py" .`) after the rename and confirmed zero remaining references anywhere in the codebase. The only caller was the route handler, which is now updated. Also re-ran the full test suite (`pytest tests/ -v`) to confirm nothing else depended on the old name.

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