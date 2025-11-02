# WCAG Audit Data Extractor (Prototype)

This repository contains a working prototype for a WCAG audit data extraction system. The end goal is a Flask-based front end enhanced to meet WCAG accessibility standards. While back-end functionality is still under development, the current prototype provides usable functionality for extracting, cleaning, managing, and saving WCAG audit data—primarily via a notebook-driven workflow.

## Status
- Stage: Prototype (active development)
- Target: Flask front end with WCAG-compliant UI
- Current interface: Jupyter/Colab notebook with interactive controllers
- Back end: Google Sheets used as a simple data store (via `gspread`)

## Key Features (Prototype)
- Document processing: Upload, validation, conversion (.doc/.docx → .docx), and text extraction
- Data extraction: WCAG success criteria table detection and parsing
- Cleaning & validation: Standardizes fields, validates SC IDs, PASS/FAIL/NA normalization
- AAA detection: Early detection of AAA criteria with user approval/abort flow
- Database layer (Google Sheets): Prepare and save organization, audit, results, fact, and history tables
- Search & optimized deletion: Preview impact, efficient row-based deletes, verification
- Bulk processing (v5.4): Multi-file upload, sequential processing, comprehensive batch report
- Per-cell documentation: Glossaries for each notebook cell and a combined Word document

## Repository Structure
```
CFAA_Intern_Git/
├─ Orginal_notebook/
│  ├─ data_extractor_v5_4_debugging.ipynb  # Main prototype notebook
│  └─ Developer_documents/
│     ├─ Cell_1_glossary.md ... Cell_14_glossary.md
│     ├─ glassary-template.md
│     └─ dataextractor_glossary.docx       # Combined glossary (auto-generated)
└─ WCAG_enhanced_front_end/                # Placeholder for Flask front end (planned)
```

## How the Prototype Works
The primary entry point is the notebook `Orginal_notebook/data_extractor_v5_4_debugging.ipynb`.
- Run cells in order. Use the main controller to access production operations.
- Data is stored in a Google Sheets spreadsheet (workbook is created/connected during setup).
- Bulk processing (Cells 12–14) supports multi-document runs with reports.

High-level workflow:
1. Install/verify packages (Cell 1)
2. Authenticate and connect to Google Sheets (Cells 2–3)
3. Load success criteria lookup (Cell 3)
4. Process documents and extract results (Cells 5–6)
5. Clean/validate and detect AAA criteria (Cell 7 + Cell 11)
6. Save to Google Sheets (Cell 8)
7. Search/remove data if needed (Cell 9)
8. Use main production controller (Cell 10) or bulk processing (Cells 12–14)

## Requirements
- Python 3.9+ (tested with modern 3.x)
- Recommended: Google Colab for easiest uploads and auth flow
- Google account with access to create/read/write Google Sheets
- Packages used (non-exhaustive):
  - pandas, gspread, gspread_dataframe, google-auth
  - python-docx, PyMuPDF (`fitz`), pypandoc
  - tqdm, fuzzywuzzy, loguru

## Quick Start (Notebook)
- Open `Orginal_notebook/data_extractor_v5_4_debugging.ipynb` in Jupyter or Google Colab
- Execute cells from top to bottom
- In the main controller (Cell 10):
  - Option 1: Run the complete production workflow (upload → extract → clean → save)
  - Options 2–5: Search data, remove data, view DB status, system readiness
  - Option 9: Bulk processing (if Cells 12–14 executed)

If running locally (not Colab), replace Colab-specific upload/auth steps with local equivalents (service account or OAuth) and ensure dependencies are installed.

## Output Artifacts
- Google Sheets workbook with worksheets:
  - `organization_info`, `audit_info`, `audit_results`, `fact_auditing_table`, `history_log`
- Per-cell markdown glossaries in `Orginal_notebook/Developer_documents`
- Combined glossary document: `Orginal_notebook/Developer_documents/dataextractor_glossary.docx`

## Accessibility (WCAG) Principles for the Planned Flask UI
The eventual Flask front end will be designed to align with WCAG 2.1+ A/AA (and optionally AAA) where feasible:
- Semantic HTML and proper heading structure
- Sufficient color contrast and scalable fonts
- Focus management, skip links, and keyboard navigability
- ARIA roles/states only where necessary
- Form labels, error summaries, and accessible validation
- Responsive design with reflow-friendly layouts

## Roadmap
- Flask front end (WCAG-focused)
  - Accessible components (navigation, forms, dialogs)
  - Bulk upload UI with progress and previews
  - Results dashboards and history views
- API layer to replace or supplement Google Sheets
  - Persistence options: relational DB (e.g., Postgres) or cloud data store
  - AuthN/AuthZ for protected operations
- Packaging & deployment
  - Dockerfile and GitHub Actions CI/CD
  - Infrastructure-as-code templates for cloud deployment
- Testing & quality
  - Unit/integration tests for core extraction and cleaning
  - Accessibility linting and automated checks (axe-core, pa11y)

## Contributing
Contributions are welcome while this is in prototype—please open issues with context and screenshots/logs when relevant. If you plan a larger change (API, UI flows), propose it first in an issue for alignment.

### Development Notes
- Keep notebook cells idempotent when possible; avoid destructive operations without confirmations
- Prefer mock save functions for local testing (`use_mock=True`), then switch to real saves
- Document changes in `Developer_documents` or update `dataextractor_glossary.docx`

## License
TBD. If you have licensing requirements, add a `LICENSE` file (e.g., MIT, Apache-2.0) at the repository root.

## Acknowledgements
- Built using Python, Google Sheets (`gspread`), and related libraries
- WCAG references: https://www.w3.org/WAI/standards-guidelines/wcag/
