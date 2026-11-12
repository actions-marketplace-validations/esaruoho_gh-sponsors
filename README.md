# gh-sponsors

A comprehensive, whitelabeled CLI tool, GitHub CLI extension, and GitHub Action for managing and analyzing your GitHub Sponsors data.

Unlike the default GitHub Dashboard which treats every one-time payment as an isolated event, `gh-sponsors` **aggregates all historical transactions** by person to show you the true lifetime value and chronological timeline of every supporter.

## Features

- **Entity Deduplication Engine:** Groups multiple one-time contributions from the same person into a single unified profile.
- **Privacy Firewall:** Use `--public-only` to filter out private sponsors before exporting data.
- **Multiple Exports:** Supports beautiful terminal UI, JSON, CSV, and Markdown generation.
- **Three Modes of Operation:**
  - Standard standalone Python script
  - GitHub CLI Extension (`gh sponsors`)
  - GitHub Action (auto-generate `SPONSORS.md`)

## 1. Install as a GitHub CLI Extension

```bash
gh extension install esaruoho/gh-sponsors
```
*(Replace `esaruoho` with your username once pushed).*

Then run:
```bash
gh sponsors
gh sponsors --markdown
gh sponsors --public-only --json
```

## 2. Use as a GitHub Action

You can automatically regenerate a `SPONSORS.md` file every week:

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
          github_token: ${{ secrets.SPONSORS_PAT }} # Must have read:user scope
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

## 3. Run Locally as a Python Script

```bash
git clone https://github.com/esaruoho/gh-sponsors
cd gh-sponsors
chmod +x gh-sponsors
./gh-sponsors --help
```

### Options
- `--markdown` : Output a Markdown summary (perfect for READMEs).
- `--json` : Output structured JSON for automation.
- `--csv` : Output a flat CSV spreadsheet of all transactions.
- `--public-only` : Strip all sponsors who selected "Private".
- `--all-tiers` : Show all configured sponsor tiers (Terminal mode only).

## Authentication

This tool relies on the GitHub GraphQL API. Your token (or `gh` auth status) must have the `read:user` and `read:org` scopes.

To update your local CLI scopes:
```bash
gh auth refresh -s read:user
```

## License

MIT
