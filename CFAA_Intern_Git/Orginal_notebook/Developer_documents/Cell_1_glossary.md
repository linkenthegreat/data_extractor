---
### [Cell 1_glossary] (./Cell 1_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Install required Python packages and perform initial environment setup for `data_extractor_v5_4_debugging.ipynb` so subsequent cells (authentication, data import, processing) can run correctly. Intended for execution in Jupyter/Colab environments. Commands use `pip` and may require elevated permissions or a compatible Python environment.

**function name**:
- N/A (this cell contains shell/pip commands for environment setup, not a Python function).

**Description**

This cell performs package installation and basic environment preparation. It uninstalls an older `fitz` package (if present) and installs or upgrades a set of dependencies used across the notebook, including libraries for document conversion, PDF handling, Google Sheets integration, dataframes, and text processing. Running this cell ensures the notebook has the required packages and recent compatible versions.

Key actions performed in the cell:
1. Uninstall `fitz` (to avoid conflicts with PyMuPDF).
2. Install `pypandoc`, `loguru` and other packages.
3. Upgrade a list of packages using `pip install --upgrade` to make sure the latest compatible versions are available.

**Parameter**
1. `!pip` shell commands — the cell executes shell pip commands; there are no runtime function parameters.
2. Execution environment — assumes internet access and permission to install packages (e.g., Colab, notebook kernel with pip access).

**Output**
1. Installed/upgraded Python packages in the current environment.
2. Console output describing success/failure of each pip operation.
3. Potential side effects: changes to the Python environment (package versions), which may require kernel restart to take effect.

**Library used/import** 
1. python-docx
   - Library Description: Read and write Microsoft Word (.docx) files.
   - Reference & Documentation: https://python-docx.readthedocs.io/

2. PyMuPDF (package name: PyMuPDF, import name: fitz)
   - Library Description: Fast PDF, XPS, EPUB, and image rendering and text extraction.
   - Reference & Documentation: https://pymupdf.readthedocs.io/

3. pandas
   - Library Description: Data manipulation and analysis; primary dataframe library used for tabular data.
   - Reference & Documentation: https://pandas.pydata.org/

4. gspread
   - Library Description: Google Sheets API wrapper for read/write access to Google Sheets.
   - Reference & Documentation: https://gspread.readthedocs.io/

5. gspread_dataframe
   - Library Description: Utilities to convert between gspread worksheets and pandas DataFrames.
   - Reference & Documentation: https://github.com/robin900/gspread-dataframe

6. google-auth
   - Library Description: Google authentication library used to obtain credentials for Google APIs.
   - Reference & Documentation: https://google-auth.readthedocs.io/

7. openpyxl
   - Library Description: Read/write Excel 2010 xlsx/xlsm/xltx/xltm files; used when working with Excel files.
   - Reference & Documentation: https://openpyxl.readthedocs.io/

8. pypandoc
   - Library Description: Python wrapper for pandoc for converting between document formats (e.g., doc -> docx, docx -> markdown).
   - Reference & Documentation: https://pypandoc.readthedocs.io/

9. tqdm
   - Library Description: Fast, extensible progress bars for loops and iterable operations.
   - Reference & Documentation: https://tqdm.github.io/

10. fuzzywuzzy (with [speedup])
    - Library Description: Fuzzy string matching utilities (used for similarity checks, e.g., organization name matching). The `[speedup]` extra attempts to install C-backed speedups.
    - Reference & Documentation: https://github.com/seatgeek/fuzzywuzzy

11. loguru
    - Library Description: Simple and convenient logging library for Python with an easier API than the stdlib logger.
    - Reference & Documentation: https://github.com/Delgan/loguru

Notes & Tips:
- If running locally on Windows, consider using a virtual environment (venv/conda) before installing/upgrading packages to avoid altering the system Python.
- Some conversions (pypandoc) may require system-level binaries like `pandoc` or LibreOffice for full functionality.
- The cell uses shell-style `!pip` commands which are suitable in Jupyter/Colab; in other environments, use the corresponding pip command (e.g., `pip install ...`) in the terminal.

---
