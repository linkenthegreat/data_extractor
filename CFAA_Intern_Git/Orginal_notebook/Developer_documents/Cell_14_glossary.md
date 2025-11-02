### [Cell 14_glossary] (./Cell 14_glossary.md)

**Request/Configuration information (If need)**
Simplified Bulk Controller Interface (Bulk Processing - Cell 3 of 3). Provides a single unified entry point that runs upload → process → report. Also includes a unit-test helper for bulk components.

**function name**: bulk_processing_controller
**Description**
Unified controller that runs the full bulk pipeline in three phases:
1. Upload & validation (calls `bulk_upload_documents()`)
2. Sequential processing (calls `bulk_process_batch()`)
3. Results & reporting (calls `bulk_generate_report()`)

It handles user confirmations, cleanup, and session tracking (timing, phases, user interactions). Returns a `session_results` dict describing the whole session.

**Parameter**
None (interactive control flow; relies on earlier module functions being loaded)

**Output**
1. **session_results (dict)** — Contains keys like `session_start_time`, `workflow_version`, `phases_completed`, `upload_results`, `processing_results`, `final_report`, `session_status`, `total_session_time_seconds`.

**Library used/import**
1. **datetime** — Session timing, timestamps.
2. **Builtins & previously defined bulk helpers** — Integrates the upload, processing and reporting functions from previous cells.

**Notes & tips**
- Interactive: prompts user to confirm processing after upload; it will clean up temp files on cancel or completion.
- Designed to be ADR-003 compliant (zero core changes to main workflow). For headless automation, you can programmatically call the inner functions and bypass interactive prompts.

---

**function name**: bulk_unit_test_controller
**Description**
A small test harness to verify availability and basic integration of bulk-related functions. Runs lightweight checks such as function availability, main-workflow integration score, and import availability.

**Parameter**
None

**Output**
1. **test_results (dict)** — Contains `tests_run`, `passed`, `failed`, and `errors` with a summary success rate.

**Notes & tips**
- Useful during development to ensure required functions and imports are loaded before running full bulk sessions.
- Recommended to run if any main-workflow cells were modified or not executed in the current session.