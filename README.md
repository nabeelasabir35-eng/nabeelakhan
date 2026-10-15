# nabeelakhan# 📄 ATS Resume Checker

A Streamlit app that analyzes a resume with Google Gemini Flash and returns:

- an **estimated ATS score** (0-100) with a 5-part breakdown (keywords, formatting, structure, impact, readability)
- **strengths and weaknesses**
- **missing keywords** (matched against a job description if you paste one)
- **prioritised improvements** with rewrite examples
- a downloadable Markdown report

Supported uploads: **PDF, DOCX, TXT** (max 5 MB). Scanned/image-only resumes are rejected because an ATS cannot read them either.

> The score is an AI estimate based on the text extracted from your file. It is not the output of a real ATS product (Workday, Greenhouse, etc.), so use it as guidance.

## How the score works

The model rates five categories from 0 to 100. The app computes the final score itself as a weighted average, so it is transparent and cannot be inflated by the model or by text hidden in a resume:

| Category | Weight |
|---|---|
| Keywords | 30% |
| Formatting | 20% |
| Structure | 20% |
| Impact | 20% |
| Readability | 10% |

## Project files

```
app.py            # the Streamlit app
requirements.txt  # Python dependencies
README.md         # this file
```

## Run locally

1. Get a free Gemini API key at <https://aistudio.google.com/apikey>.
2. Create a virtual environment and install dependencies (Python 3.10+):

   ```bash
   python -m venv venv
   source venv/bin/activate        # Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. Provide the key in **one** of these ways:

   - paste it in the app sidebar, or
   - set an environment variable:
     - macOS/Linux: `export GEMINI_API_KEY="your-key"`
     - Windows (PowerShell): `$env:GEMINI_API_KEY="your-key"`
   - or create `.streamlit/secrets.toml` containing:

     ```toml
     GEMINI_API_KEY = "your-key"
     ```

4. Start the app:

   ```bash
   streamlit run app.py
   ```

   It opens at <http://localhost:8501>.

### Choosing the model

The sidebar has a **Gemini model** field. The default is `gemini-flash-latest`, an alias that points to Google's newest Flash model, so the app keeps working when older versions are retired. You can pin a specific model (for example `gemini-2.5-flash`) in the sidebar or by setting the `GEMINI_MODEL` environment variable. Current model names are listed at <https://ai.google.dev/gemini-api/docs/models>.

## Push to GitHub

1. Create an empty repository on GitHub (<https://github.com/new>), for example `ats-resume-checker`. Do not add a README there, since you already have one.
2. In your project folder, first create a `.gitignore` so secrets never get committed:

   ```bash
   printf "venv/\n__pycache__/\n*.pyc\n.env\n.streamlit/secrets.toml\n" > .gitignore
   ```

3. Initialise, commit and push (replace `YOUR-USERNAME`):

   ```bash
   git init
   git add .
   git commit -m "Initial commit: ATS resume checker"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/ats-resume-checker.git
   git push -u origin main
   ```

   When Git asks for a password, use a **Personal Access Token** (GitHub → Settings → Developer settings → Personal access tokens), not your account password. Alternatively, install the GitHub CLI and run `gh auth login` first.

**Never commit your API key.** If you ever do, delete the key in Google AI Studio and create a new one.

## Deploy on Streamlit Community Cloud (free)

1. Sign in at <https://share.streamlit.io> with your GitHub account and authorise access to your repository.
2. Click **Create app** (top-right), then **Yup, I have an app**.
3. Fill in:
   - **Repository:** `YOUR-USERNAME/ats-resume-checker`
   - **Branch:** `main`
   - **Main file path:** `app.py`
   - **App URL:** optional custom subdomain
4. Click **Advanced settings**:
   - choose a **Python version** (3.11 or 3.12 both work)
   - paste your secret into the **Secrets** box:

     ```toml
     GEMINI_API_KEY = "your-key"
     ```

   - click **Save**.
5. Click **Deploy**. The first build installs `requirements.txt` and takes a couple of minutes. Your app then gets a public `*.streamlit.app` URL.

To update the app later, just `git push` to `main`; Streamlit redeploys automatically. You can edit secrets any time from the app's **Settings → Secrets** menu.

### Cost warning for public apps

If you store your key in Secrets, **everyone who opens your public app uses your Gemini quota**. Options: leave the Secrets box empty so each visitor pastes their own key in the sidebar, keep the app private (share only with specific people from the app's sharing settings), or monitor usage in Google AI Studio.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Gemini rejected the API key" | Check the key; make sure it has no extra spaces. |
| "The selected model was not found" | Change the model name in the sidebar (try `gemini-flash-latest`). |
| "Rate limit or quota reached" | Wait a minute; free-tier keys have low per-minute limits. |
| "Very little text could be extracted" | Your PDF is probably a scan. Export a text-based PDF/DOCX from Word or Google Docs. |
| Build fails on Streamlit Cloud | Confirm `requirements.txt` is in the repo root and the main file path is `app.py`. |

## Privacy

Resume text is sent to the Google Gemini API for analysis. The app itself does not store uploaded files or results beyond your current browser session.

## License

MIT, or choose your own.
