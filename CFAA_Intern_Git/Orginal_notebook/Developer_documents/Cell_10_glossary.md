### [Cell 10_glossary] (./Cell 10_glossary.md)

**Request/Configuration information (If need)**
Main Production Controller & User Interface for WCAG Audit Extractor v4.0. This cell provides high-level workflow orchestration, a production-ready run function, interactive main menu, system readiness checks, system info and help pages. Run after previous initialization, extraction, cleaning, and database cells are loaded.

**function name**: check_system_readiness
**Description**
Run a quick readiness check to ensure required components and functions are loaded in the current runtime (document processing, cleaning, database and removal functions). Prints a human-friendly summary and returns boolean readiness.

**Parameter**
None (inspects globals and runtime state)

**Output**
1. **all_ready (bool)** — True when all required components are present; False otherwise.

**Library used/import**
1. **Builtins** — uses `print()` and basic `globals()` checks.

---

**function name**: run_production_workflow
**Description**
End-to-end production workflow orchestration. Calls document processing, cleaning/validation, and database save steps in sequence; collects results, handles retries and user restart requests, and returns a structured `workflow_results` dict describing success, errors and intermediate outputs.

**Parameter**
None directly — relies on globally available functions like `process_document_complete`, `clean_extracted_data`, and `save_extracted_data_to_database`.

**Output**
1. **workflow_results (dict)** — Keys: `success` (bool), `extracted_data` (dict), `cleaning_results` (dict), `database_results` (dict), `errors` (list), `warnings` (list).

**Library used/import**
1. **Builtins** — control flow and exception handling.

---

**function name**: main_controller
**Description**
Interactive main menu for the full system. Exposes options to run the production workflow, search/remove data, check system readiness, view system info/help, and launch testing or bulk-processing controllers. Handles user input with a loop, validates availability of required helper functions, and guides the user through multi-step, potentially destructive operations (with confirmations).

**Parameter**
None (interactive input-driven)

**Output**
1. **None (interactive)** — Runs until the user exits. Side effects include calling other functions and updating Google Sheets.

**Library used/import**
1. **Builtins** — `input()`/`print()` for interactivity.
2. **google.colab.files** (optional) — referenced elsewhere for uploads; not required here.

---

**function name**: display_system_info
**Description**
Print a summary of system architecture, feature availability, function status and database worksheet listings. Designed to help users verify which cells/functions are loaded and which external integrations are available.

**Parameter**
None

**Output**
1. **None (prints status)**

**Library used/import**
1. **Builtins** and optional `google.colab` import detection.

---

**function name**: display_help_guide
**Description**
Print a detailed help guide describing controller options, getting started steps, troubleshooting tips, and glossary/terminology sections.

**Parameter**
None

**Output**
1. **None (prints help text)**

**Library used/import**
1. **Builtins**

---

Notes & tips
- This cell is highly interactive and intended for manual/Colab use; many menu options call functions that must be defined in earlier cells (e.g., `process_document_complete`, `search_audit_data`, `optimized_remove_audit_data`, `save_extracted_data_to_database`). Use `check_system_readiness()` first.
- Production mode will perform permanent writes to Google Sheets. Use the testing controller or `use_mock=True` saves during development to avoid destructive changes.
- For automation, call `run_production_workflow()` programmatically instead of driving `main_controller()`.
- The menu includes triple-confirmation flows for dangerous operations (removal), previews and debug helpers before final deletion.