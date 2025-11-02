---
### [Cell 3_glossary] (./Cell 3_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Initialize required worksheet tables in the Google Sheet and handle success criteria data import (load from existing sheet, import from production, upload CSV, or use a minimal fallback). Prepares lookup structures used by downstream processing.

**function name**:
- Multiple functions defined in this cell: `upload_success_criteria_csv`, `create_minimal_success_criteria_fallback`, `check_existing_tables`, `import_from_production` (plus logic to create missing worksheets and set initial empty DataFrames).

**Description**

This cell performs data initialization steps and provides several helper functions to obtain the reference list of WCAG success criteria:

- Defines a `table_defs` dictionary that lists the expected worksheet names and their column schema for the audit system (organization_info, audit_info, audit_results, success_criteria_info, fact_auditing_table, history_log).
- Ensures each table exists in the Google Sheet; if missing it creates worksheets and writes empty DataFrames with the correct column names.
- Provides `upload_success_criteria_csv()` (Colab-based upload helper) to load success criteria from a CSV file and validate required columns.
- Provides `create_minimal_success_criteria_fallback()` to generate a very small fallback DataFrame when no official success criteria are available.
- `check_existing_tables()` enumerates which worksheets already exist.
- `import_from_production()` attempts to copy success criteria from a production spreadsheet (if accessible).
- After choosing/importing success criteria the cell writes them to a `success_criteria_info` worksheet and builds an in-memory lookup `sc_lookup` keyed by `sc_id` for fast reference.

**Parameter**
1. Interactive user choices for importing: prompts to import from production or upload CSV, and whether to accept a minimal fallback. These are done via `input()` calls.
2. Google Sheet context (the `sheet` and `gc` objects created in the previous cell).

**Output**
1. Worksheets created in the Google Sheet (if absent) and populated with empty DataFrames or the imported success criteria.
2. `success_criteria_df` (pandas.DataFrame) and `sc_lookup` (dict) available in the notebook for later lookups.
3. Console/log outputs indicating number of records loaded, table creation status, and any validation errors.

**Library used/import** 
1. gspread_dataframe.set_with_dataframe
   - Description: Write a pandas DataFrame to a gspread worksheet.
   - Repo: https://github.com/robin900/gspread-dataframe
2. pandas
   - Description: Data handling and DataFrame creation.
   - Docs: https://pandas.pydata.org/
3. google.colab.files (used inside `upload_success_criteria_csv`)
   - Description: Colab file upload helper (only available in Colab).

Notes & Tips:
- The CSV upload helper is Colab-specific; for local runs, replace the upload flow with a path-based pd.read_csv call.
- The `create_minimal_success_criteria_fallback` function provides a basic set of example criteria useful for testing but not suitable for production.
- The cell performs considerable interactive prompts — for automation you can skip prompts and programmatically provide `success_criteria_df`.
- After this cell runs successfully you should have `sc_lookup` populated for lookups by `sc_id`.

---
