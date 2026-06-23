# Handwritten explanations (required for submission)

Add clear photos or scans of your handwritten notes here before submitting.

Each note should cover one fix and include:

1. **Where** — file, line, layer (frontend / backend / SQL)
2. **How you found it** — what you tested or observed
3. **Root cause** — why it broke
4. **Fix** — what you changed and why

## Suggested pages (one per bug)

### Bug 1 — SQL operator precedence
- **Where:** `TaskRepository.java` line 14, `db/queries/search_tasks.sql`
- **Discovery:** `curl ".../api/tasks?q=api&status=OPEN"` returned task #2 with status `IN_PROGRESS`
- **Cause:** `AND` binds tighter than `OR`; status/archived filters only applied to part of the `WHERE` clause
- **Fix:** Wrap title/description `LIKE` in parentheses: `AND (title LIKE ... OR description LIKE ...)`

### Bug 2 — Artificial API delay
- **Where:** `TaskController.java` lines 35–42 (removed)
- **Discovery:** Empty search took ~1s; `time curl` showed delay scaled with query length
- **Cause:** `Thread.sleep((10 - query.length()) * 100)` — shorter queries slept longer
- **Fix:** Removed the sleep block entirely

### Bug 3 — Loading stuck on error
- **Where:** `frontend/src/hooks/useTasks.js`
- **Discovery:** `TaskTable` checks `loading` before `error`, so failed requests never show the error message
- **Cause:** `setLoading(false)` missing in `.catch`; `setError(null)` missing on new fetch
- **Fix:** Clear error and set loading false in both success and error paths; cancel stale requests

### Bug 4 — Debounce + pagination reset
- **Where:** `App.jsx`, new `useDebouncedValue.js`
- **Discovery:** Every keystroke hit the API; changing filters on page 2 could show empty results
- **Fix:** 300ms debounce on search; reset `page` to 1 when debounced query or status changes
