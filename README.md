# runbook
open site -> https://maryoussef149-cell.github.io/DevOps-Runbook/

A single-page reference site for junior DevOps engineers: a troubleshooting **runbook** you diagnose before you check the answer, plus an **interview prep** section organized by company. Built as a static site no build step, no framework, no backend.

## Features

- **35 real-world incidents** across Docker, Kubernetes, Git, Linux, Networking, CI/CD, and Infrastructure as Code. Each one shows the symptom first, a diagnostic checklist, and a fix that stays hidden behind a "Reveal fix" button until you actually check it.
- **Interview prep by company** Google, Amazon/AWS, Microsoft/Azure, Meta, and Netflix, each with questions that reflect how that company actually interviews, plus a "Universal Fundamentals" tab for the basics every interviewer circles back to. Every question expands to show what it's really testing.
- **Search and category filters** on the runbook, so you can drill into just Kubernetes, just networking, etc.
- **Light and dark mode**, following your system preference by default, with a manual toggle.
- **Progress tracking** resolved incidents are remembered so you can see how much of the runbook you've actually worked through.

## Project structure

```
.
├── index.html                     # the entire site markup, styles, and logic in one file
├── README.md
└── .github/
    └── workflows/
        └── ci-cd.yml               # validates the HTML, then deploys to GitHub Pages
```

## Running it locally

There's nothing to install or build. Either:

- Double-click `index.html` to open it directly in a browser, or
- Serve it locally so relative paths and fonts behave exactly like production:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## CI/CD

The pipeline lives in [`.github/workflows/ci-cd.yml`](.github/workflows/ci-cd.yml) and runs as two jobs:

| Job | Runs on | What it does |
|---|---|---|
| `validate` | every push and pull request to `main` | Checks `index.html` exists and validates its HTML5 markup with [`html5validator`](https://pypi.org/project/html5validator/), so a broken tag fails the build before it ever reaches production. |
| `deploy` | pushes to `main` only, after `validate` passes | Publishes the site to GitHub Pages via the official `actions/deploy-pages` action. |

Pull requests get validated but never deployed only merges to `main` go live.

**To turn on deployment in your own repo:**

1. Push this project to GitHub.
2. Go to **Settings → Pages** and set **Source** to **GitHub Actions**.
3. Push (or merge) to `main` the workflow will validate and deploy automatically.
4. Your site will be live at `https://<your-username>.github.io/<repo-name>/`.

No secrets or extra configuration are needed; `actions/deploy-pages` uses the repo's built-in `GITHUB_TOKEN`.

## Extending the content

Everything lives in two arrays near the top of the `<script>` block in `index.html`:

- `PROBLEMS` add a new incident by adding an object with `category`, `title`, `demo`, `tips`, `cause`, `fix`, and optionally `commands`. If you introduce a new category, add it to `CATEGORY_ORDER` too so it gets a filter chip.
- `COMPANIES` add a new company (with a flat `questions` array), or extend `Universal Fundamentals`, which groups questions under `groups` instead.

No build step means editing either array and reloading the page is the entire workflow.

## Notes on persistence

Resolved-incident progress and your light/dark preference are saved through `window.storage`, an API available when this page runs as a Claude.ai artifact. Outside that environment (e.g. self-hosted on GitHub Pages), the site detects its absence and simply skips persistence  everything else works exactly the same, it just won't remember your progress between visits unless you wire up your own storage.
