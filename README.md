# 🤖 AI Agents — Daily arXiv Tracker

A self-updating page that fetches the latest **AI agents papers** from arXiv every morning and publishes them as a filterable card page on GitHub Pages.

**Live site:** `https://<your-username>.github.io/<repo-name>/`
**Data:** `data/papers.csv` — cumulative, deduplicated record of all fetched papers
**Schedule:** Every day at **8:00 AM Pacific Time** (GitHub Actions)

---

## Repo structure

```
├── .github/
│   └── workflows/
│       └── fetch_arxiv.yml   ← daily automation
├── scripts/
│   └── fetch_arxiv.py        ← fetch + CSV + HTML generator
├── docs/
│   └── index.html            ← GitHub Pages site (auto-generated)
├── data/
│   └── papers.csv            ← cumulative paper archive (auto-generated)
└── README.md
```

---

## Setup (one-time, ~5 minutes)

### 1. Create a new GitHub repository

Go to [github.com/new](https://github.com/new) and create a **public** repository (public repos get GitHub Actions free minutes; private repos use paid minutes after 2,000/month).

### 2. Upload these files

Clone the repo locally and copy all files from this folder into it, preserving the directory structure. Then push:

```bash
git init
git add .
git commit -m "Initial commit — arXiv tracker setup"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

### 3. Enable GitHub Pages

1. Go to your repo on GitHub → **Settings** → **Pages**
2. Under **Source**, select **Deploy from a branch**
3. Branch: `main` · Folder: `/docs`
4. Click **Save**

Your site will be live at `https://<your-username>.github.io/<repo-name>/` within a minute or two.

### 4. Run the workflow for the first time

1. Go to your repo → **Actions** tab
2. Click **Fetch arXiv AI Agents Papers** in the left sidebar
3. Click **Run workflow** → **Run workflow**

This populates `data/papers.csv` and `docs/index.html` immediately. After that, the workflow runs automatically every morning at 8 AM PT.

---

## Customizing the search

Open `scripts/fetch_arxiv.py` and edit the `QUERY` variable at the top:

```python
# Current query (medium scope — cs.AI + cs.MA + cs.LG + "agent" keyword)
QUERY = (
    '(cat:cs.AI OR cat:cs.MA OR cat:cs.LG) AND '
    '(ti:agent OR ti:agents OR abs:"AI agent" OR ...)'
)

# Narrow example — multi-agent systems only
QUERY = 'cat:cs.MA'

# Broad example — add robotics and NLP
QUERY = '(cat:cs.AI OR cat:cs.MA OR cat:cs.RO OR cat:cs.CL) AND (ti:agent OR abs:"LLM agent")'
```

arXiv category codes: `cs.AI` (AI), `cs.MA` (Multi-agent), `cs.LG` (ML), `cs.CL` (NLP), `cs.RO` (Robotics)

---

## Changing the schedule

Edit `.github/workflows/fetch_arxiv.yml`:

```yaml
on:
  schedule:
    - cron: "0 15 * * *"  # 08:00 AM PDT (UTC-7) — change this
```

Cron runs in **UTC**. Converter: [crontab.guru](https://crontab.guru)

| Desired time (PT) | Cron (UTC) |
|---|---|
| 6:00 AM PDT | `0 13 * * *` |
| 7:00 AM PDT | `0 14 * * *` |
| **8:00 AM PDT** ← current | `0 15 * * *` |
| 9:00 AM PDT | `0 16 * * *` |

---

## The HTML page features

- **Real-time keyword filter** — searches title, abstract, and authors
- **Date filter** — Today / Last 7 days / Last 30 days / All time
- **Sort** — Newest first / Oldest first
- **Collapsible abstracts** — click "Show more" on any card
- **Category badges** — color-coded cs.AI / cs.MA / cs.LG
- **CSV download link** — in the page footer

---

## Dependencies

Only one Python package needed: [`arxiv`](https://pypi.org/project/arxiv/)
Everything else (csv, json, pathlib) is Python standard library.
