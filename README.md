# 📄 ATS Resume Checker

A Streamlit app that scores a resume for **ATS (Applicant Tracking System) compatibility** and gives specific, prioritised improvements, powered by **Google Gemini Flash**.

## Features

- Upload a resume as **PDF, DOCX or TXT** (up to 5 MB)
- Optional **job description** for tailored keyword matching
- **ATS score (0-100)** = 80% Gemini analysis (5 weighted categories) + 20% deterministic rule checks (email, phone, sections, bullets, metrics, length...)
- Strengths, weaknesses and **prioritised fixes with rewrite examples**
- Matched vs. missing keywords
- View the extracted text (what an ATS actually "sees")
- Download the report as Markdown
- Friendly errors for scanned/image PDFs, corrupt files, bad API keys and rate limits

## Project structure

```
ats-resume-checker/
├── app.py            # Streamlit app
├── requirements.txt  # Python dependencies
└── README.md
```

## Run locally

1. **Get a free Gemini API key** at <https://aistudio.google.com/apikey>.
2. **Create a virtual environment and install dependencies**
   ```bash
   python -m venv venv
   source venv/bin/activate        # Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```
3. **Provide your API key** (choose one):
   - Environment variable:
     ```bash
     export GEMINI_API_KEY="your-key"     # Windows PowerShell: $env:GEMINI_API_KEY="your-key"
     ```
   - or a local secrets file `.streamlit/secrets.toml`:
     ```toml
     GEMINI_API_KEY = "your-key"
     ```
   - or just paste it into the app's sidebar at runtime.
4. **Start the app**
   ```bash
   streamlit run app.py
   ```

### Choosing the model

The default model is `gemini-3.8-flash`. To use a different Flash model, either edit the **Model** box in the sidebar or set a `GEMINI_MODEL` secret / environment variable (e.g. `gemini-3.6-flash`). Check <https://ai.google.dev/gemini-api/docs/models> for current model names.

## Push to GitHub

```bash
cd ats-resume-checker

# 1. Make sure secrets are never committed
printf ".streamlit/secrets.toml\nvenv/\n__pycache__/\n*.pyc\n.env\n" > .gitignore

# 2. Initialise and commit
git init
git add .
git commit -m "Initial commit: ATS resume checker"
git branch -M main

# 3. Create an EMPTY repo on https://github.com/new (no README/.gitignore), then:
git remote add origin https://github.com/<your-username>/ats-resume-checker.git
git push -u origin main
```

> If GitHub asks for a password, use a **Personal Access Token** (GitHub → Settings → Developer settings → Tokens) or sign in with the GitHub CLI (`gh auth login`).

## Deploy on Streamlit Community Cloud (free)

1. Go to <https://share.streamlit.io> and sign in with GitHub.
2. Click **Create app** → **Deploy a public app from GitHub**.
3. Select your repository, branch `main`, and main file path `app.py`.
4. Open **Advanced settings** → **Secrets** and paste:
   ```toml
   GEMINI_API_KEY = "your-key"
   ```
5. Click **Deploy**. After a couple of minutes you'll get a public `*.streamlit.app` URL.

Every `git push` to `main` redeploys automatically.

## Security & privacy notes

- **Never commit your API key.** Use Streamlit secrets or environment variables.
- If the app is public and uses *your* key, anyone can spend your quota. Either keep the app private (Streamlit Cloud → Share settings), leave the secret unset so each visitor enters their own key in the sidebar, or set quota limits on your key in Google AI Studio.
- Resume text is sent to the Gemini API. Resumes are not stored by this app (they live only in the user's session memory).

## How the score works

| Category | Weight |
|---|---|
| ATS-friendly formatting | 20% |
| Keywords & relevance | 25% |
| Section completeness | 15% |
| Impact & achievements | 25% |
| Clarity & language | 15% |

The weighted AI score is blended with an automated rule-check score (80/20) to keep results more stable. Treat the score as **guidance, not an official ATS result**: real ATS software differs between vendors.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Very little text could be extracted" | The PDF is scanned/image-based. Export a text-based PDF or DOCX. |
| "Model not found" | Change the model name in the sidebar / `GEMINI_MODEL`. |
| "Rate limit / quota reached" | Wait a minute, or use another key / enable billing. |
| Deployment fails on dependencies | Check `requirements.txt` is in the repo root. |

## License

MIT - use freely.
