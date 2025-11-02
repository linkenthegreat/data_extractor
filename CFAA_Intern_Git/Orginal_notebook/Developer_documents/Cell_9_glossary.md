---
### [Cell 9_glossary] (./Cell 9_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Provide an optimized data removal workflow and utility functions for searching, previewing, and deleting audit-related data from the Google Sheets database. This cell implements a single, efficient row-based deletion method and supporting verification and preview utilities.

**function name**:
- `search_audit_data`
- `debug_audit_data`
- `find_rows_to_delete`
- `delete_rows_efficiently`
- `verify_deletion_completion`
- `optimized_remove_audit_data`
- `preview_removal_impact`
- `remove_audit_by_id`
- `generate_log_id` (utility)
- `validate_removal_readiness`

**Description**

This cell contains the deletion and search features needed to safely remove audit data from the system:
1. `search_audit_data(search_criteria)` — flexible multi-step search across worksheets (audit_info, organization_info, audit_results, history_log) using criteria such as `audit_id`, `org_name`, `document_name`, and `wcag_version`. Returns matching organizations, audits, results, logs, and totals.
2. `debug_audit_data(audit_id_to_find)` — debugging helper to show available worksheet names, sample audit IDs, and exact/similar matches for a given audit_id.
3. Row-finding & deletion helpers — `find_rows_to_delete` identifies 1-based row numbers to delete in a worksheet given a set of target IDs (returns sorted descending order to avoid index shifting). `delete_rows_efficiently` deletes rows bottom-up, counting successes.
4. Verification helpers — `verify_deletion_completion` checks that target IDs no longer exist in a worksheet, returning detailed verification status and any remaining IDs.
5. High-level removal — `optimized_remove_audit_data` orchestrates backup log creation, deletes dependent fact records, results, and audit_info rows in phases, verifies deletions, and returns a detailed result summary including API-calls-saved heuristics and errors.
6. `preview_removal_impact` gives a non-destructive report estimating the records affected and whether double confirmation is recommended.
7. Wrapper helpers — `remove_audit_by_id` offers a convenient single-call interface to search + optimized removal; `validate_removal_readiness` verifies the environment and required tables are present.

**Parameter**
1. `search_criteria` (dict): filters for `search_audit_data`.
2. `audit_id` (str): ID to remove via `remove_audit_by_id`.
3. `target_ids` (set): set of identifiers for row deletion functions.
4. Flags: `confirm_removal` (bool), `preview_only` (bool) to control destructive actions.

**Output**
1. `search_audit_data` returns a dict with `organizations`, `audits`, `results`, `history_logs`, and `total_records` found.
2. `optimized_remove_audit_data` returns a dict detailing counts removed per table, verification results, created backup log entries indicator, and any errors.
3. `preview_removal_impact` returns an `impact` dict summarizing counts and warnings to guide confirmation.
4. Console outputs include debug info, found rows, deletion progress, and verification messages.

**Library used/import** 
1. datetime, timezone — timestamps for backup and history log entries.
2. logging — for internal diagnostic messages.
3. pandas — used for DataFrame operations when reading/writing sheets.
4. gspread (via `sheet`) and `gspread_dataframe` — read/write operations on Google Sheets worksheets.

Notes & Tips:
- The optimized deletion method always creates a backup history_log entry per audit removal for traceability.
- Deletions are performed bottom-up (descending row indices) to avoid row shifting issues.
- Use `preview_only=True` to see what would be removed before calling removal with `confirm_removal=True`.
- For very large deletion operations, watch for API quotas — this workflow attempts to minimize calls by deleting rows directly and avoiding full-table rewrites when possible.

---
