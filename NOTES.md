# Submission Notes

## Summary of changes

1. **SQL operator precedence** — Fixed the search query in `TaskRepository.java` (and mirrored in `db/queries/search_tasks.sql` and `db/oracle/task_search_package.sql`). Missing parentheses caused `AND`/`OR` to bind incorrectly, so status and archived filters were bypassed for title matches and archived tasks could appear in results.
2. **Artificial API delay** — Removed `Thread.sleep` in `TaskController.java` that added up to 1 second of latency on short/empty searches.
3. **Frontend fetch state** — Fixed `useTasks.js` so errors clear on retry, loading resets on failure, and in-flight requests are cancelled on dependency change.
4. **Search debounce + page reset** — Added `useDebouncedValue` (300 ms) to reduce API calls while typing, and reset pagination when search or status filters change.

## What I chose not to change

- **In-memory pagination** — The controller loads all matching rows then slices in Java. Fixing this properly needs DB-level `LIMIT`/`OFFSET`; left as-is to keep the diff small.
- **Invalid status handling** — Bad `status` values still throw from `TaskStatus.valueOf`; a 400 response would be nicer but is lower impact than the SQL bug.
- **Console logging in `api.js`** — Harmless in dev; removing it is cosmetic.
- **Full Oracle package rewrite** — Only corrected the same precedence bug in the reference artifact.

## Biggest remaining risk

The search endpoint still loads the full result set into memory before paginating. As task volume grows, this will cause memory pressure and slow responses even without the removed `Thread.sleep`. Production fix: push pagination and filtering into the SQL query.

## tools/AI 

chatGPT & Gemini
## Assumptions

- Handwritten explanation photos are added by me in `handwritten/` before final submission (see `handwritten/README.md` for the checklist).
- Focused on correctness and performance over feature additions within the ~90 minute timebox.
