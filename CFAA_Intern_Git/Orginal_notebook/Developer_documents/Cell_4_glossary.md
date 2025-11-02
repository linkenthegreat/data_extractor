---
### [Cell 4_glossary] (./Cell 4_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Define core utility functions used across the notebook for ID generation, validation, document hashing, and duplicate detection. These functions are foundational and used by document processing, audit creation, and logging.

**function name**:
- Several utility functions are defined: `generate_org_id`, `generate_audit_id`, `generate_result_id`, `generate_fact_id`, `generate_log_id`, `validate_id_format`, `validate_sc_id`, `calculate_document_hash`, `check_document_duplicate`, `display_duplicate_warning`, `prompt_duplicate_action`, `display_existing_audit_summary`, and `test_id_generation`.

**Description**

This cell implements the low-level utilities:
- ID generation: deterministic IDs using SHA1 hashing (with prefixes like `org-`, `aud-`, `res-`, `fact-`) and fallback UUIDs on error.
- Validation: `validate_id_format` checks prefixes and length; `validate_sc_id` ensures success criteria strings match pattern `digit.digit.digit`.
- Document hashing: `calculate_document_hash` computes SHA256 over file bytes (chunked read for large files).
- Duplicate detection: `check_document_duplicate` runs a series of checks against `audit_info`, `audit_results`, `fact_auditing_table`, and `history_log` worksheets to categorize duplicates as HARD (stop processing) or SOFT (notify + allow continue). Returns a boolean and a details dict when duplicates are found.
- Warning & action prompts: `display_duplicate_warning` and `prompt_duplicate_action` present the findings to the user and determine whether to abort, skip, or continue processing.
- `test_id_generation` exercises the ID generation and validation functions and asserts expected formats. The cell calls this test during initialization and logs readiness.

**Parameter**
1. For ID generation functions: inputs such as `org_name`, `org_id`, `audit_date`, `retest_count`, `audit_id`, `sc_id`.
2. For duplicate checks: `doc_hash` and `filename`; these functions also consult the Google Sheet (`sheet`) for existing entries.
3. For hashing: `file_path` (local path to the document file).

**Output**
1. Generated ID strings (e.g., `org-XXXXXXXX`, `aud-XXXXXXXX`, `res-...`, `fact-...`).
2. `calculate_document_hash` returns SHA256 hex digest or None if file missing.
3. `check_document_duplicate` returns (is_duplicate: bool, details: dict or None) where details includes type `hard`/`soft`, `source` table, and an example entry.
4. Console/log outputs for debug, info, warnings, and test results. `test_id_generation()` returns True/False and prints pass/fail.

**Library used/import** 
1. hashlib
   - Description: Hash functions (SHA1, SHA256) used for deterministic ID derivation and document hashing.
   - Docs: https://docs.python.org/3/library/hashlib.html
2. uuid
   - Description: Fallback unique ID generation.
   - Docs: https://docs.python.org/3/library/uuid.html
3. os
   - Description: File existence checks and file operations.
   - Docs: https://docs.python.org/3/library/os.html
4. re
   - Description: Regular expressions used for validation and parsing.
   - Docs: https://docs.python.org/3/library/re.html
5. datetime
   - Description: Timestamps for logs and ID generation fallback values.
   - Docs: https://docs.python.org/3/library/datetime.html
6. loguru (used indirectly via logger)
   - Description: Logging helper used throughout these utilities for consistent messages.

Notes & Tips:
- The duplicate check reads several worksheets; if those worksheets don't exist the code handles WorksheetNotFound gracefully but duplicate checking will be limited.
- The hashing function expects a local file path; in Colab the file lives under `/content/...` after upload.
- `generate_result_id` uses a new format `res-{audit_id}-{sc_id}` — ensure downstream code expects this format.
- `test_id_generation` is a helpful sanity check and is run in the cell — review its output to confirm the utility functions are working.

---
