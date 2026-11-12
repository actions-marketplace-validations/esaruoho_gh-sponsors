# gh-sponsors

[![GitHub Sponsors](https://img.shields.io/badge/Sponsor%20@esaruoho-%E2%9D%A4-ea4aaa?style=flat-square&logo=github)](https://github.com/sponsors/esaruoho) [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)

A comprehensive CLI tool, GitHub CLI extension, and GitHub Action for managing and analyzing your GitHub Sponsors data.

Unlike the default GitHub Dashboard which treats every one-time payment as an isolated event, `gh-sponsors` **aggregates all historical transactions by person** to give you the true lifetime contribution and chronological timeline of every supporter.

## Why `gh-sponsors`?

Existing tools in the GitHub ecosystem typically fall into two categories:
1. **Avatar grid generators** (like `github-sponsors-readme-action` or `tj/sponsors-api`): Good for rendering circular profile pictures in a README, but they don't track amounts, transaction history, payouts, or analytics.
2. **Package paywalling/DRM** (like `devlooped/gh-sponsors`): Designed to gate package installs behind Auth0 tokens, rather than providing maintainer analytics.

`gh-sponsors` fills the gap as a **true maintainer financial ledger and reporting tool**:
- **Deduplication:** When a supporter makes multiple one-time contributions (e.g. $5 today, $5 next month, $10 later), it rolls them into **1 person with a lifetime sum ($20.00)** and a full chronological ledger.
- **Privacy Firewall:** Safely export public markdown for your repo (`--public-only`) without exposing private supporter identities or amounts.
- **Unified Distribution:** Works as a local CLI command (`sponsors`), a native GitHub CLI extension (`gh sponsors`), and an automated GitHub Action.

## Features

- **Entity Deduplication Engine:** Groups multiple one-time contributions from the same person into a single unified profile.
- **Privacy Firewall:** Use `--public-only` to filter out private sponsors before exporting data.
- **Multiple Exports:** Supports terminal UI, JSON, CSV, and Markdown generation.
- **Three Modes of Operation:**
  - Standard standalone CLI script (`sponsors`)
  - GitHub CLI Extension (`gh sponsors`)
  - GitHub Action (auto-generate `SPONSORS.md`)

## 1. Install as a GitHub CLI Extension

```bash
gh extension install esaruoho/gh-sponsors
```

Then run:
```bash
gh sponsors
gh sponsors --markdown
gh sponsors --public-only --json
```

## 2. Use as a GitHub Action

Automatically regenerate a `SPONSORS.md` file in your repository on a schedule:

```yaml
name: Generate Sponsors
on:
  schedule:
    - cron: '0 0 * * 0' # Every Sunday
  workflow_dispatch:

jobs:
  sponsors:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate SPONSORS.md
        uses: esaruoho/gh-sponsors@main
        with:
          github_token: ${{ secrets.SPONSORS_PAT }} # Requires read:user and read:org
          output_file: 'SPONSORS.md'
          public_only: 'true'
          
      - name: Commit changes
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git add SPONSORS.md
          git commit -m "docs: Update SPONSORS.md" || echo "No changes to commit"
          git push
```

## 3. Run Locally as a Standalone Script

```bash
git clone https://github.com/esaruoho/gh-sponsors
cd gh-sponsors
chmod +x gh-sponsors
./gh-sponsors --help
```

### CLI Options

| Flag | Description |
| :--- | :--- |
| *(default)* | Formatted ANSI terminal dashboard with financial overview, goals, and cards |
| `--markdown` | Clean Markdown summary suitable for READMEs or `SPONSORS.md` |
| `--public-only` | Omit private sponsors and their transaction data |
| `--json` | Machine-readable JSON output (ideal for scripting and `jq`) |
| `--csv` | Flat CSV spreadsheet of all individual transactions |
| `--all-tiers` | Display all configured tiers on your sponsors profile |
| `--no-color` | Disable ANSI color codes |

## Authentication & Permissions

This tool queries GitHub's GraphQL API. Your token (or `gh` authentication) must include the `read:user` and `read:org` scopes.

To update your local CLI scopes:
```bash
gh auth refresh -s read:user
```

## 💖 Support this Project

If `gh-sponsors` helps you track your open-source backers or celebrate funding milestones with your community, consider joining as a supporter:

[![Sponsor on GitHub](https://img.shields.io/badge/Sponsor%20@esaruoho-%E2%9D%A4-ea4aaa?style=for-the-badge&logo=github)](https://github.com/sponsors/esaruoho)

👉 **[Become a backer at github.com/sponsors/esaruoho](https://github.com/sponsors/esaruoho)**

## License

MIT
