---
### [Cell 8_glossary] (./Cell 8_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Prepare cleaned data for persistence and provide helpers to save organization, audit, results, fact table, and history log entries to Google Sheets. Includes mock save functions for testing without a live Google Sheets connection.

**function name**:
- `prepare_organization_for_database`
- `prepare_audit_for_database`
- `prepare_results_for_database`
- `prepare_fact_table_records`
- `prepare_history_log_entry`
- `mock_update_or_insert_organization`, `mock_save_audit_info`, `mock_save_audit_results`, `mock_save_fact_table`, `mock_save_history_log`
- `validate_database_connection`
- `save_extracted_data_to_database`, `save_to_real_database`, plus per-table save functions: `save_organization_data`, `save_audit_data`, `save_results_data`, `save_fact_auditing_data`, `save_history_log`
- `validate_organization_data_in_fact_table`, `validate_organization_data_in_audit_info`

**Description**

This cell implements the database layer that converts cleaned objects into structures suitable for Google Sheets and then writes them to the appropriate worksheets. Key features:
- Preparation helpers convert runtime objects into database-shaped records (remove runtime-only flags, add created_date timestamps).
- `prepare_fact_table_records` builds denormalized records combining organization, audit, result, and success-criteria dimension fields — ideal for Looker Studio or BI dashboards.
- Mock save functions exist for safe local testing without writing to Google Sheets.
- Connection validation (`validate_database_connection`) checks for mock/sandbox/production readiness and ensures `gc` and `sheet` exist.
- Save orchestration (`save_extracted_data_to_database`) prepares records, optionally routes to mock saves, and otherwise calls `save_to_real_database` that writes each worksheet using `gspread_dataframe.set_with_dataframe`.
- After writes, validation functions ensure organization fields (sector, org_size_group, org_name) are synchronized into fact and audit tables.

**Parameter**
1. `org_info`, `audit_info`, `audit_results` — cleaned data ready for persistence.
2. `use_mock` (bool) — when True, uses mock save functions to avoid writing to Google Sheets.
3. Per-table functions accept prepared records (dicts or list of dicts).

**Output**
1. Writes to Google Sheets worksheets: `organization_info`, `audit_info`, `audit_results`, `fact_auditing_table`, and `history_log`.
2. Return flags (True/False) indicating save success per table, and overall success from `save_extracted_data_to_database`.
3. Console/log outputs describing saved counts and any errors encountered.

**Library used/import** 
1. pandas — convert dicts to DataFrames and add timestamps.
2. gspread_dataframe.set_with_dataframe, get_as_dataframe — read/write sheets.
3. datetime — for created_date and last_updated timestamps.
4. loguru (`logger`) — logging.

Notes & Tips:
- The save functions clear the destination worksheet before writing the combined DataFrame (existing data + new rows) — this preserves historical data order but may be heavy for large sheets. Consider appending rows for large datasets.
- `prepare_fact_table_records` tries to call `generate_fact_id` and falls back to a deterministic SHA1-based ID if the function is missing — ensure Cell 4 was executed so the canonical ID functions are available.
- Use `use_mock=True` for local testing to avoid accidental writes to production sheets.
- The functions expect worksheet names to match exactly; mismatches will cause errors — confirm sheet structure first.

---
