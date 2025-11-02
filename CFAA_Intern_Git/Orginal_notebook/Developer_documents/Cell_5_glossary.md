---
### [Cell 5_glossary] (./Cell 5_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Full document upload, validation, conversion, text extraction, organization and audit metadata extraction, table extraction, and orchestration of the end-to-end document processing pipeline.
- Environment: Designed for interactive runs (Google Colab recommended for file uploads). Assumes earlier authentication cell created `gc` and `sheet` objects and that `sc_lookup` is available from the success criteria cell.

**function name**:
- Multiple helper functions are defined; main orchestrator is `process_document_complete`.
- Other functions: `upload_and_validate_document`, `validate_file_type_and_confirm`, `check_filename_for_duplicates`, `convert_doc_to_docx`, `extract_text_from_docx`, `extract_org_name_from_text`, `handle_organization_extraction_failure`, `extract_site_url`, `verify_organization_info`, `extract_audit_date`, `detect_retest_count`, `extract_wcag_version_and_target`, `verify_audit_details`, `extract_tables_from_docx`, `identify_audit_result_tables`, `extract_results_from_tables`.

**Description**

This cell provides the document handling and processing pipeline for WCAG audit reports:
1. Upload & validation
   - `upload_and_validate_document()` uses Colab's `files.upload()` to accept `.doc`/`.docx` files and returns the uploaded path and filename.
   - `validate_file_type_and_confirm()` checks file extension, size, and prompts the user to confirm processing (or restart/cancel). It also runs a lightweight filename-duplicate check.
2. Duplicate detection and hashing
   - `calculate_document_hash()` (from earlier cell) is called to compute SHA256 for comprehensive duplicate detection.
   - Duplicate detection functions consult Google Sheets tables (via `sheet`) to determine soft vs hard duplicates.
3. Conversion & text extraction
   - `convert_doc_to_docx()` attempts conversion using `pypandoc` or falls back to LibreOffice headless conversion.
   - `extract_text_from_docx()` reads `.docx` paragraphs using `python-docx` and returns combined text.
4. Organization extraction & verification
   - `extract_org_name_from_text()` heuristically parses the first page to find the organization name (looks for patterns like 'for <organization>').
   - `handle_organization_extraction_failure()` provides interactive options (manual input, reselect document, cancel) when automated extraction fails.
   - `verify_organization_info()` compares extracted org name and URL to existing `organization_info` records (fuzzy matching) and prompts the user to use an existing org or create a new one.
5. Audit metadata extraction
   - `extract_audit_date()`, `detect_retest_count()`, and `extract_wcag_version_and_target()` find audit date, retest count, WCAG version, and conformance target.
   - `verify_audit_details()` prompts the user to correct missing/ambiguous metadata and supports a skip option for problematic documents.
6. Table extraction & result parsing
   - `extract_tables_from_docx()` pulls all tables from the `.docx` file.
   - `identify_audit_result_tables()` identifies tables likely containing success criteria and result columns.
   - `extract_results_from_tables()` extracts SC IDs and results, normalizes values (PASS/FAIL/NA), validates formats, generates `result_id`s, and returns a list of result dicts.
7. Orchestration
   - `process_document_complete()` runs the full pipeline: upload → duplicate check → text extraction → org/audit extraction → table/result extraction → final summary and returns `(org_info, audit_info, audit_results)`.

**Parameter**
1. Interactive inputs: confirmations and corrections via `input()` at multiple steps (upload confirmation, duplicate handling, organization selection, audit metadata corrections, validation decisions).
2. File path / name: `process_document_complete` expects to operate on the uploaded file path provided by `upload_and_validate_document()`.
3. Google Sheet context: the functions consult `sheet` and related worksheets for duplicate checks and existing organization data.

**Output**
1. Returns a tuple: `(org_info: dict, audit_info: dict, audit_results: list[dict])` on success, or `None`/error codes if processing is cancelled or fails.
2. Writes are not automatic in this cell — it prepares the objects for downstream persistence (saving to worksheets may be done elsewhere).
3. Console/log messages provide processing progress, warnings, validation issues, and summary counts.

**Library used/import** 
1. google.colab.files (Colab-specific)
   - Description: Interactive file upload helper in Colab.
   - Notes: Replace with local file path handling for non-Colab environments.
2. python-docx (`docx.Document`)
   - Description: Read `.docx` files and extract paragraphs/tables.
   - Docs: https://python-docx.readthedocs.io/
3. pypandoc
   - Description: Wrapper for pandoc conversions; used for `.doc` → `.docx` conversion.
   - Notes: May require system `pandoc` binary; LibreOffice fallback is implemented.
4. subprocess, os
   - Description: Used for calling LibreOffice conversion and filesystem checks.
5. re
   - Description: Regex parsing for dates, patterns, and metadata.
6. datetime
   - Description: Date and timestamp parsing/formatting.
7. fuzzywuzzy
   - Description: Fuzzy string matching used to compare organization names and find similar entries.
8. tqdm (used elsewhere) and pandas (for DataFrame conversions when interacting with sheets).

Notes & Tips:
- For non-interactive or bulk processing, automate inputs (avoid `input()` prompts) and ensure the `sheet` environment and `sc_lookup` exist before running.
- Document conversion relies on either `pypandoc` (and pandoc) or LibreOffice; ensure appropriate system binaries if running locally.
- Keep original files (or store hashes) to facilitate duplicate detection and audits.
- The cell is the main entry point for processing a single document; wrap calls to `process_document_complete()` when integrating into batch jobs.

---
