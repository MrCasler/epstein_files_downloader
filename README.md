# Epstein Files Downloader

Download PDFs from the U.S. Department of Justice Epstein document repository:  
**[justice.gov/epstein](https://www.justice.gov/epstein/)**

The site requires age verification and uses cookies. This tool uses **Brave** (or Chrome) with Selenium to pass the gate once, then uses a Python `requests` session with those cookies for fast bulk downloads.

---

## Requirements

- **Python 3.10+**
- **Brave Browser** (or Chrome) — used only for the initial age check and search; downloads run in Python.
- **macOS**: SSL certs via `certifi` (see Setup).

---

## Setup

### 1. Clone or copy this folder

If you're using it from **smol_skripts**:

```bash
cd /path/to/smol_skripts/epstein
```

If you're using the **standalone repo**:

```bash
git clone <your-epstein-repo-url> epstein-files
cd epstein-files
```

### 2. Create a virtual environment

```bash
python3 -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. (macOS) SSL certificate fix

If you see `SSL: CERTIFICATE_VERIFY_FAILED` when the script runs, install certifi and use it (the script already uses it if available):

```bash
pip install certifi
```

On some Mac Python installs you may need to run the “Install Certificates” command from your Python folder in Applications.

---

## How to run

From the `epstein` directory (with `venv` activated):

```bash
python download_epstein_files.py
```

You’ll see a menu:

| Mode | What it does |
|------|----------------|
| **1** | **Download ALL** — Searches for “No images produced”, paginates through all results (you choose max pages), and downloads every file as PDF. |
| **2** | **Journalist (auto)** — Runs many curated keyword searches (names, violence-related terms, evidence/locations). Saves the first **N** files per keyword (default 10). Builds a report and asks whether to download. |
| **3** | **File list** — No browser. Reads `file_ids.txt` and downloads only those IDs as PDFs. Use after running mode 1 or 2 to re-download without searching again. |
| **4** | **Custom search** — You type a search query and choose how many files to download and how many pages to search. |

### First run (modes 1, 2, or 4)

1. The script opens **Brave** and goes to justice.gov/epstein.
2. If you see an age gate, click **Yes** (or complete any CAPTCHA).
3. The script saves cookies to `cookies_browser.json` and uses them for all downloads.
4. On later runs, if those cookies still work, the browser may be skipped.

### Where files go

- PDFs are saved under **`downloads/pdf/`** (e.g. `downloads/pdf/EFTA00024813.pdf`).
- Collected file IDs are appended to **`file_ids.txt`** so you can re-download with mode 3.
- Mode 2 also writes **`downloads/journalist_report.json`** with matched keywords per file.

---

## Options you’ll be asked

- **Max pages** — How many result pages to scrape (mode 1 and 4).
- **Files per keyword** — For mode 2, how many files to keep per search term (e.g. 10).
- **Max pages per search** — For mode 2, how many pages to follow per keyword.

You can just press Enter to accept the defaults.

---

## Other scripts in this folder

| Script | Purpose |
|--------|--------|
| `download_epstein_files.py` | **Main script** — menu above; use this. |
| `download_epstein_files_selenium.py` | Older variant: does both search and download in the browser (slower, but no separate requests step). |
| `fetch_file_list_selenium.py` | Only fetches and saves file IDs (no downloads). |
| `quick_cookie_extract.py` | Extracts cookies from the browser for use elsewhere. |
| `manual_file_list.py` | Helpers for building or editing a file list. |

See `EXTRACT_COOKIES.md` if you want to use cookies in another tool.

---

## Publishing this as its own repo

This folder can live in two places:

1. **Inside smol_skripts** — Use it as `smol_skripts/epstein/` and run it from there.
2. **As a separate GitHub (or other) repo** — So others can clone only the Epstein tool.

### To create a new repo with only the Epstein downloader

1. **Create a new empty repo** on GitHub (or GitLab, etc.). Do not add a README or .gitignore there.

2. **Copy the epstein folder** to a new directory (so you don’t mix smol_skripts history with the new repo):

   ```bash
   cp -R /path/to/smol_skripts/epstein /tmp/epstein-standalone
   cd /tmp/epstein-standalone
   ```

3. **Remove things you don’t want in the public repo** (venv, downloads, cookies, cache):

   ```bash
   rm -rf venv downloads .pytest_cache .cursor
   rm -f cookies_browser.json cookies.json cookies.txt file_ids.txt
   ```

   The `epstein` folder has a **`.gitignore`** that already ignores these. So when you init git *inside* the copied folder, they won’t be added.

4. **Initialize git and push**:

   ```bash
   git init
   git add .
   git commit -m "Initial commit: Epstein files downloader"
   git remote add origin https://github.com/YOUR_USERNAME/YOUR_EPSTEIN_REPO.git
   git branch -M main
   git push -u origin main
   ```

5. **Keep using the original in smol_skripts** — Your normal workflow stays in `smol_skripts/epstein/`. The standalone repo is just a clean copy for sharing. When you want to update the public repo, copy the script (and README, requirements.txt, etc.) again and commit/push from the standalone directory.

---

## Privacy and safety

- **Cookies** — `cookies_browser.json` holds session cookies. Don’t commit it or share it; it’s in `.gitignore` for the standalone repo.
- **Downloads** — The DOJ site hosts sensitive material. Use and store downloaded files according to your own policies and the site’s terms of use.

---

## License

Use and modify as you like. No warranty.
