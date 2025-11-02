### [Cell 13_glossary] (./Cell 13_glossary.md)

**Request/Configuration information (If need)**
Bulk Processing - Processing Engine Module v5.4. This cell provides the sequential processing engine that runs the existing single-document workflow against each validated document in a batch and a comprehensive batch reporting utility.

**function name**: bulk_process_batch
**Description**
Sequentially processes a list of validated documents by invoking the same core processing steps used for single-document runs (text extraction, organization/audit extraction, results extraction, cleaning, and database save). Enhanced error handling and user mid-process controls (skip/stop) are provided. Collects per-document results and aggregates summary statistics.

**Parameter**
1. **validated_documents (list)** — List of validated document info dicts produced by `bulk_upload_documents()`.

**Output**
1. **batch_results (dict)** — Comprehensive batch-level result structure with keys such as `total_documents`, `processing_start_time`, `document_results` (list of per-document dicts), `summary_stats` (success, skipped_aaa, skipped_error, failed, stopped_by_user), `processing_errors`, `processing_end_time`, `total_processing_time_seconds`.

**Library used/import**
1. **datetime, os** — For timing and file checks.
2. **Integrations / dependencies** — This function relies heavily on many main-workflow functions to be present: `process_document_complete` or the individual components it calls (e.g., `extract_text_from_docx`, `extract_org_name_from_text`, `verify_organization_info`, `extract_audit_date`, `detect_retest_count`, `extract_wcag_version_and_target`, `verify_audit_details`, `generate_audit_id`, `extract_tables_from_docx`, `identify_audit_result_tables`, `extract_results_from_tables`, `clean_extracted_data`, `save_extracted_data_to_database`).

**Notes & tips**
- The engine avoids changing core workflow logic; it reuses existing functions, enabling zero core changes while enabling batch processing.
- Contains interactive mid-process controls: prompts before each document (continue/skip/stop) and on errors (stop/skip).
- Supports AAA-skip handling: depends on `clean_extracted_data()` to return an abort/skip signal if the user chooses to abort due to AAA.

---

**function name**: bulk_generate_report
**Description**
Generate a readable, comprehensive batch report summarizing processing stats, upload failures, per-document statuses, errors, user stops, and recommendations. Intended to be called after `bulk_process_batch`.

**Parameter**
1. **batch_results (dict)** — Output from `bulk_process_batch()`.
2. **failed_validations (list, optional)** — Upload validation failures to include in the report.

**Output**
1. **report (dict/batch_results)** — The function prints an extended report to stdout and returns `batch_results` (enhanced/annotated). It categorizes failures (duplicates, format issues, other) and prints recommendations.

**Library used/import**
1. **Builtins + datetime** — For formatting and timing. No external packages required.

**Notes & tips**
- When providing this report for teams, capture the printed output or extend the function to return a formatted string or save to a file.
- The report calculates success rates both excluding and including upload failures for clearer diagnostics.
- Useful recommendations are printed when duplicates, AAA skips, or failures are present, including advice about using the data removal functions prior to re-uploading duplicates.