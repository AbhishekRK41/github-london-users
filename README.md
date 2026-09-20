# GitHub London Users — Data Collection & Analysis

Scrapes the GitHub REST API for every user based in London with 500+ followers, pulls their profile details and up to 500 repositories each, and exports two clean CSVs ready for analysis.

## What it does

1. Searches the GitHub Search API (`/search/users`) for accounts with `location:London` and `followers:>500`, paging through all results.
2. For each matching user, fetches full profile details (company, bio, hireable status, etc.) via `/users/{username}`.
3. For each user, fetches up to 500 of their repositories via `/users/{username}/repos`, capturing name, creation date, stars, primary language, and license.
4. Cleans and normalizes fields (e.g. stripping `@` from company names, uppercasing) and writes everything to `users.csv` and `repositories.csv`.
5. Respects GitHub's rate limits with a short delay between requests and pagination handling.

## Architecture

```
scraping_code.py
├── get_users()              # paginated search for London users, 500+ followers
├── get_user_details()       # per-user profile lookup
├── get_user_repositories()  # per-user repo listing, up to 500 repos (5 pages)
└── main / CSV export        # writes users.csv and repositories.csv
```

## Setup

**Requirements:** Python 3.x, a [GitHub Personal Access Token](https://github.com/settings/tokens) (used only to raise the API rate limit to 5,000 requests/hour — no special scopes needed for public data).

```bash
git clone https://github.com/AbhishekRK41/github-london-users.git
cd github-london-users
pip install requests
```

Set your token as an environment variable:

```bash
export GITHUB_TOKEN="your-personal-access-token"
```

## Usage

```bash
python scraping_code.py
```

Produces two files in the working directory:
- **`users.csv`** — GitHub login, name, company, location, bio, hireable status, follower count
- **`repositories.csv`** — repo name, owner, creation date, star count, primary language, license

The script deliberately does not collect email addresses. The `users.csv` currently committed here was produced before that change and still has an `email` column; re-run the script to regenerate it without one.

## Findings

- Top users in London tend to have a diverse language mix across their repos rather than specializing in one stack.
- Active, well-documented profiles correlate with higher follower counts.

## License

MIT — see [LICENSE](./LICENSE).
