# resume

Single source of truth for my resume/portfolio data (`resume.json`), with scripts and GitHub Actions that turn it into:

- A **portfolio website**, published via GitHub Pages
- A **PDF resume**, committed back into the repo

## Quick start

Finding the right platform for creating a resume is always a challenge — especially when it's urgent. Do this instead:

1. Fork this repository: https://github.com/zhalok/resume
2. Update [`resume.json`](resume.json) with your own information
3. Push the code to `main`

You'll then see two things happen automatically:

1. A `resume.pdf` gets generated and committed to your fork
2. A portfolio site gets generated and deployed to `https://<your-github-username>.github.io/resume/`

> **Note:** GitHub Pages deployment must be enabled once per fork — go to your fork's **Settings → Pages** and set the source to **GitHub Actions**. After that, every push to `main` keeps both the PDF and the portfolio in sync with `resume.json`.

## How it works

Everything is driven by [`resume.json`](resume.json):

- `basics` — name, title, contact info, links
- `fields_to_show` — which sections appear, **and in what order**, on both the portfolio page and the PDF
- `work`, `education`, `skills`, `certificates`, `publications`, `projects` — the actual content

Each entry (and each item inside `work`, `certificates`, `projects`, etc.) can be hidden without deleting it by adding `"show": false`.

To update your resume/portfolio, edit `resume.json` and push to `main` — the workflows below take care of the rest.

## Generating things locally

### Portfolio (`portfolio/index.html`)

```bash
node generate-portfolio.js
```

Reads `resume.json` and regenerates `portfolio/index.html`.

### PDF resume (`resume.pdf`)

Requires Docker.

```bash
./run.sh
```

This runs `json_to_latext.py` (via `docker-compose`) to turn `resume.json` into `resume.tex`, then compiles it with `pdflatex` into `resume.pdf`.

## GitHub Actions

| Workflow | File | Trigger | What it does |
|---|---|---|---|
| Deploy static content to Pages | [`.github/workflows/static.yml`](.github/workflows/static.yml) | Push to `main`, or manual | Regenerates `portfolio/index.html` from `resume.json` and deploys the `portfolio/` folder to GitHub Pages |
| Generate resume PDF | [`.github/workflows/generate-resume-pdf.yml`](.github/workflows/generate-resume-pdf.yml) | Push to `main`, or manual | Regenerates `resume.tex` from `resume.json`, compiles it to `resume.pdf`, and commits the PDF back to the repo if it changed |
| Manual trigger | [`.github/workflows/main.yml`](.github/workflows/main.yml) | Manual only | Runs the Docker-based `json_to_latext.py` + `pdflatex` pipeline (same as `run.sh`) without committing the result — useful for a one-off check |

So in short: **push to `main`**, and both the live portfolio site and `resume.pdf` in the repo stay in sync with `resume.json` automatically.

## File overview

```
resume.json              # source of truth for all resume/portfolio content
generate-portfolio.js    # resume.json -> portfolio/index.html
json_to_latext.py        # resume.json -> resume.tex
util.py                  # LaTeX section renderers used by json_to_latext.py
run.sh                   # local helper: resume.json -> resume.tex -> resume.pdf (via Docker)
compose.yml              # Docker services (python, latex) used by run.sh / main.yml
portfolio/index.html     # generated portfolio site (do not edit by hand)
resume.pdf               # generated PDF resume (do not edit by hand)
```
