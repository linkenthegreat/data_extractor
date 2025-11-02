### [Cell 11_glossary] (./Cell 11_glossary.md)

**Request/Configuration information (If need)**
This cell implements the AAA detection & user notification workflow. It scans extracted audit results for Level-AAA success criteria (using the global `sc_lookup`), reports findings to the user, and provides helper functions for logging and readiness validation. Run after success criteria (Cell 3) is loaded.

**function name**: detect_aaa_criteria
**Description**
Detect AAA-level success criteria in a list of audit result dictionaries by looking up each result's `sc_id` in the global `sc_lookup` mapping. Returns structured detection info.

**Parameter**
1. **audit_results** — list
   List of audit result dictionaries. Each item is expected to contain at least an `sc_id` and `result` field.

**Output**
1. **detection_result (dict)**
   Dictionary with keys: `has_aaa` (bool), `aaa_criteria` (list of dicts with AAA criteria details), `aaa_count` (int), `total_criteria` (int). May include `error` when lookup unavailable.

**Library used/import**
1. **Builtins** — Uses basic Python (print/input) and list/dict manipulation. No external imports required.

---

### Auxiliary functions in this cell

**function name**: notify_aaa_detection
**Description**
Displays the detected AAA criteria to the user with details and prompts them to choose whether to continue processing or abort (safe default is to abort on errors). Returns `'continue'` or `'abort'` depending on the user's choice.

**Parameter**
1. **aaa_detection_result** — dict
   The output from `detect_aaa_criteria()`.

**Output**
1. **user_choice (str)** — `'continue'` or `'abort'`

**Library used/import**
1. **Builtins** — Uses `input()` for interactive confirmation.

---

**function name**: create_aaa_history_log_entry
**Description**
Builds a formatted history/log entry summarizing detected AAA criteria and the user's decision (continue or abort). This is intended for insertion into the `history_log` details column.

**Parameter**
1. **aaa_detection_result** — dict
2. **user_choice** — str (`'continue'` or `'abort'`)
3. **audit_id** — str
4. **document_name** — str

**Output**
1. **log_entry (str)** — Formatted summary string for history logging.

**Library used/import**
1. **Builtins** — string formatting.

---

**function name**: validate_aaa_detection_requirements
**Description**
Quick readiness check that ensures `sc_lookup` exists and contains the expected `s_level_A_AA_AAA` field and reports missing components / warnings.

**Parameter**
None

**Output**
1. **validation_result (dict)** — Keys: `ready` (bool), `missing_components` (list), `warnings` (list)

**Library used/import**
1. **Builtins** — no external libraries.

---

Notes & tips
- Requires `sc_lookup` to be populated (the success-criteria lookup from earlier notebook cells). If `sc_lookup` is empty the detection cannot run.
- Designed for interactive use: `notify_aaa_detection()` blocks for user input to select continue/abort. For automated/bulk flows, caller should be prepared to handle `'abort'` return.
- The detection function returns structured items that include criterion metadata (principle, guideline, description) when available in `sc_lookup`.