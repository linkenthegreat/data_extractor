---
### [Cell 7_glossary] (./Cell 7_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Provide comprehensive data cleaning and validation utilities for organization, audit, and audit result data extracted from documents. These functions standardize fields, validate formats, detect duplicates, and provide a single entry point to clean and summarize extracted data.

**function name**:
- `clean_organization_data`
- `clean_audit_data`
- `clean_audit_results`
- `validate_data_completeness`
- `display_data_summary`
- `clean_extracted_data` (main orchestration, includes AAA detection step)

**Description**

This cell contains data cleaning and validation logic used after extraction and before persistence:
1. Organization cleaning (`clean_organization_data`) — normalizes org names (removing extra spaces, common suffixes), standardizes URLs (adds protocol, strips trailing slashes), and trims fields such as `sector` and `org_size_group`. Reports issues found.
2. Audit cleaning (`clean_audit_data`) — standardizes `audit_date` into a consistent format (tries several parse formats), normalizes `WCAG_vers` to `WCAG X.Y`, validates `conformance_target` (A/AA/AAA), and validates `retest_count`. Reports parsing or validation warnings.
3. Results cleaning (`clean_audit_results`) — normalizes SC IDs and `result` values (PASS/FAIL/NA), removes parenthetical text, enforces uppercase result values, detects duplicates (same audit_id + sc_id) and skips them, and reports summary statistics and issues.
4. Validation (`validate_data_completeness`) — checks for missing required fields across organization, audit, and results; computes warnings for abnormal distributions (e.g., no FAILs, many N/As); cross-validates org_id between org_info and audit_info, and ensures audit_id matches results.
5. Orchestration (`clean_extracted_data`) — top-level function that can perform early AAA criteria detection (if helper functions exist) and then call the heavy cleaning functions. Returns cleaned objects and validation status.

**Parameter**
1. `org_info` (dict): Extracted organization metadata (org_id, org_name, site_url, sector, etc.).
2. `audit_info` (dict): Extracted audit metadata (audit_id, audit_date, WCAG_vers, conformance_target, retest_count, document_name, doc_hashing, etc.).
3. `audit_results` (list[dict]): Raw or partially processed audit result records; each should at least contain `sc_id`, `result`, and `audit_id`.
4. `clean_extracted_data` optionally consults global helper functions for AAA detection: `detect_aaa_criteria` and `notify_aaa_detection`.

**Output**
1. Cleaned versions of the inputs: dicts/lists with standardized fields. E.g., cleaned org dict, cleaned audit dict, and cleaned results list.
2. `clean_audit_results` returns a list of cleaned result dicts; `display_data_summary` returns validation status and lists of issues/warnings.
3. Console output summarizing cleaned counts, validation warnings, and any skipped/invalid rows.

**Library used/import** 
1. pandas — for timestamps and potential DataFrame operations.
2. re — regular expressions for parsing and cleaning (dates, SC IDs, URLs).
3. datetime — parsing and standardizing dates.
4. collections.Counter — statistics on PASS/FAIL/NA counts.
5. loguru (`logger`) — logging throughout for info/warning/error messages.

Notes & Tips:
- `clean_audit_data` supports several date formats; if a date cannot be parsed, it preserves the original value and logs a warning.
- `clean_audit_results` intentionally preserves `raw_result` semantics elsewhere (some pipelines keep raw values for traceability); this cell standardizes a `result` field to canonical values.
- `clean_extracted_data` attempts early AAA detection to avoid expensive cleaning when the user chooses to abort; ensure the helper functions for AAA detection are available in the environment if you want that optimization.
- The cell is interactive only indirectly (through potential AAA notification prompts) and otherwise deterministic.

---
