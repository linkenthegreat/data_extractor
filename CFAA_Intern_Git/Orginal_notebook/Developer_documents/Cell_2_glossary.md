---
### [Cell 2_glossary] (./Cell 2_glossary.md)

**Request/Configuration information (If need)**

- Purpose: Authenticate with Google (in Colab/Jupyter environment), initialize logging, and connect to the Google Sheets workbook used by the WCAG Audit Extractor. Prompts the user to choose sandbox vs production mode and will create the spreadsheet if it doesn't exist.

**function name**:
- N/A (cell contains initialization and interactive setup code rather than a single function).

**Description**

This cell configures logging (using loguru), performs Google authentication, establishes a `gspread` client, and selects the target Google Sheet. It prompts the user whether to use sandbox mode (safe test sheet) or production mode (live sheet). When production is chosen it asks for a second confirmation. If the named sheet is missing, the code will create it. At the end the cell prints a short system status (sheet URL, name, sandbox flag, timestamp) and signals readiness for the next cell.

**Parameter**
1. User input: Prompt "Enable sandbox mode? (yes/no)" — controls whether the cell uses a test Google Sheet or the live one.
2. If production chosen: additional confirmation input to avoid accidental usage of production data.
3. Environment: Assumes running in an environment that supports Google authentication (e.g., Google Colab) and that `gspread`, `google-auth`, and `loguru` are installed.

**Output**
1. `gc` (gspread client) and `sheet` (gspread.Spreadsheet) are created and available in the notebook namespace.
2. Console output and logger messages summarizing authentication and connection state.
3. If the sheet didn't exist, a new spreadsheet is created and its URL is printed.

**Library used/import** 
1. pandas
   - Description: Dataframes and timestamp formatting.
   - Docs: https://pandas.pydata.org/
2. gspread
   - Description: Google Sheets API wrapper used to read/write spreadsheets.
   - Docs: https://gspread.readthedocs.io/
3. google.colab.auth (Colab only)
   - Description: Helper to authenticate a Colab session with Google account.
   - Notes: Only available in Colab; local Jupyter requires different auth flow.
4. google.auth.default
   - Description: Get default credentials when running in Google environments.
   - Docs: https://google-auth.readthedocs.io/
5. gspread_dataframe.get_as_dataframe
   - Description: Convert worksheets to/from pandas DataFrames.
   - Repo: https://github.com/robin900/gspread-dataframe
6. loguru
   - Description: User-friendly logging.
   - Docs: https://github.com/Delgan/loguru
7. sys
   - Description: Used for loguru output stream configuration.

Notes & Tips:
- When running outside Colab, replace `google.colab.auth.authenticate_user()` with an appropriate OAuth flow (service account, local OAuth, or environment-provided credentials).
- Creating spreadsheets requires permissions for the authenticated account; ensure the account has Drive access.
- The cell is interactive (input prompts) and will block for user responses — useful for manual runs but may need automation adjustments for non-interactive runs.

---
