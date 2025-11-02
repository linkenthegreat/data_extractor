---
### [Cell 6_glossary] (./Cell 6_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Provide consolidated data extraction and minimal cleaning utilities to transform raw table-extracted results into cleaned, deduplicated records ready for downstream storage or analysis.

**function name**:
- `extract_and_clean_results`, `minimal_clean_data`, `display_processing_principles` (helper that prints principles).

**Description**

This cell focuses on consolidating extraction and cleaning logic to avoid transformation conflicts and preserve raw WCAG values:

1. `extract_and_clean_results(matching_tables, audit_id)`
   - Consolidated pipeline: extracts SC/result rows from provided `matching_tables`, performs minimal cleaning and validation, detects duplicates within the document (per audit_id + sc), and builds a list of processed result records.
   - Keeps `raw_result` in the record for traceability while creating a `final_result` value normalized to PASS/FAIL/NA.
   - Tracks issues (format problems, missing values, duplicates) and prints a summary with counts and simple statistics (PASS/FAIL/NA distribution).
   - Uses a `duplicate_tracker` to avoid adding the same SC twice and uses a `Counter` for summary stats.
2. `minimal_clean_data(org_info, audit_info, audit_results)`
   - Applies only essential fixes: trims whitespace, ensures site URLs have a scheme, normalizes a few ID/date fields, and preserves raw WCAG values.
   - Returns cleaned versions of organization, audit, and results objects but intentionally avoids heavy normalization to preserve source fidelity.
3. `display_processing_principles()`
   - Prints the processing principles used by the pipeline (preserve raw data, minimal transformation, transparency, etc.).

**Parameter**
1. `matching_tables`: list of tables extracted from a document (format: list of row-lists). Typically produced by `extract_tables_from_docx` and `identify_audit_result_tables` from the previous cell.
2. `audit_id`: identifier for the audit—used to detect duplicates and to generate result IDs.
3. `org_info`, `audit_info`, `audit_results`: objects for minimal cleaning.

**Output**
1. `extract_and_clean_results` returns `processed_results`: list of dicts with fields such as `result_id`, `audit_id`, `sc_id`, `result`, `table_source`, `row_source`, and `raw_result`.
2. `minimal_clean_data` returns `(cleaned_org, cleaned_audit, cleaned_results)`.
3. Console output summarizing totals, issues, and top problem entries.

**Library used/import** 
1. collections.Counter
   - Description: Simple counting helper used for PASS/FAIL/NA tallies.
2. re
   - Description: Regular expressions used for validating SC formats and cleaning.
3. pandas (if further DataFrame operations are needed when writing to sheets).

Notes & Tips:
- The function intentionally preserves original `raw_result` for traceability — downstream consumers can apply stricter normalization if desired.
- The `result_id` format in this cell uses a derived scheme (`RES-{audit_id.replace('AUD-', '')}-{index}`) — ensure downstream code that references `result_id` is compatible or adapt the ID generator to centralize formatting.
- Review `issues` printed at the end of the run and inspect up to the first 10 to debug parsing edge cases.

---
