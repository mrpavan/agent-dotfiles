# Bitbucket API Token Setup (Cursor / PR access)

As of **September 9, 2025**, Bitbucket no longer allows creating **App passwords**. Use **API tokens** instead. Existing app passwords stop working **June 9, 2026**.

This guide configures an API token so Cursor (or scripts) can access your private Bitbucket repo and pull request data (e.g. for code review).

---

## 1. Create a Bitbucket API token

1. Open **Bitbucket**: [https://bitbucket.org](https://bitbucket.org) → sign in.
2. Go to **Personal settings**:
   - Click your **profile avatar** (bottom-left) → **Personal settings**,  
   - or go to: [https://bitbucket.org/account/settings/](https://bitbucket.org/account/settings/).
3. In the left sidebar, open **Atlassian account** or **Security**, then:
   - **Create and manage API tokens** (or **API tokens**).
   - If you’re sent to **id.atlassian.com**, that’s the right place.
4. Click **Create API token with scopes**.
5. Fill in:
   - **Token name**: e.g. `api-token-readonly`
   - **Expiration**: e.g. 90 days or 1 year (optional; you can create a new token later).
6. Under **Products / Apps**, choose **Bitbucket** → **Next**.
7. Under **Scopes**, select **only what you need**:
   - **Repositories** → **Read** (repo metadata, file browsing, clone; does *not* include PRs).
   - **Pull requests** → **Read** (view PRs, comments, diff).
   - Optionally restrict to workspace **workspace-name** if the UI offers it.
8. **Create token**.
9. **Copy the token immediately** and store it somewhere safe (e.g. password manager).  
   It is shown only once and cannot be viewed again.

---

## 2. Set the token in your environment

Use **one** of the options below. The goal is that when Cursor runs terminal commands (e.g. `curl`), the shell has these variables set.

### Option A – Project-only (recommended)

In the **project root** (`app-folder`), create a `.env` file (it is ignored by git):

```bash
# Bitbucket API token (for private repo / PR access)
export BITBUCKET_API_TOKEN="your-api-token-here"
export BITBUCKET_EMAIL="your-atlassian-account-email@example.com"
```

- Replace `your-api-token-here` with the token you copied.
- Replace `your-atlassian-account-email@example.com` with the **Atlassian account email** (Bitbucket profile / Email settings).  
  The Bitbucket REST API expects **email + token**, not username + token.

Then load it in the terminal where you (or Cursor) work:

```bash
source .env
```

To have it loaded automatically when you `cd` into this project, you can add to `~/.zshrc`:

```bash
# Load app-folder .env when in that directory (optional)
if [[ -f "$HOME/Documents/cursor/app-folder/.env" ]]; then
  # Don't auto-source; use: source .env
  alias app-env='source "$HOME/Documents/cursor/app-folder/.env"'
fi
```

Then run `source .env` (or `app-env`) in the project directory before asking Cursor to use the token.

### Option B – User-level (current user only)

In `~/.zshrc` (or `~/.bashrc`):

```bash
export BITBUCKET_API_TOKEN="your-api-token-here"
export BITBUCKET_EMAIL="your-atlassian-account-email@example.com"
```

Run `source ~/.zshrc` or open a new terminal.

### Option C – Cursor / IDE environment

If your editor supports project or user environment variables, add:

- `BITBUCKET_API_TOKEN` = the token you created  
- `BITBUCKET_EMAIL` = your Atlassian account email  

Then restart Cursor so terminals inherit them.

---

## 3. How the token is used

- **Bitbucket REST API** (e.g. to fetch PR #602):  
  Requests use **HTTP Basic auth** with **email** and **API token** (not Bitbucket username):
  ```bash
  curl -s -u "$BITBUCKET_EMAIL:$BITBUCKET_API_TOKEN" \
    "https://api.bitbucket.org/2.0/repositories/work-space/bmp-ui/pullrequests/602"
  ```
- **Git over HTTPS** (clone/fetch):  
  You can use either:
  - `https://x-bitbucket-api-token-auth:$BITBUCKET_API_TOKEN@bitbucket.org/work-space/bmp-ui.git`, or  
  - `https://YOUR_BITBUCKET_USERNAME:$BITBUCKET_API_TOKEN@bitbucket.org/work-space/bmp-ui.git`  
  Prefer storing the token in a credential helper or env and avoid putting it in committed config.

---

## 4. Verify it works

In the project directory, with `BITBUCKET_EMAIL` and `BITBUCKET_API_TOKEN` set:

```bash
curl -s -u "$BITBUCKET_EMAIL:$BITBUCKET_API_TOKEN" \
  "https://api.bitbucket.org/2.0/repositories/work-space/repo-name/pullrequests/602" | head -50
```

- If you see JSON with the PR title and fields, the token and scopes are correct.
- If you get `401`, check the email and token (no extra spaces; token copied fully).
- If you get `404`, the repo or PR might not exist or the token may not have access to that workspace/repo.

---

## 5. Security checklist

- Use **API tokens**, not your Bitbucket password.
- Grant only **Read** for **Repositories** and **Pull requests** (and optionally limit to workspace **ada-asia**).
- **Do not** commit the token or put it in tracked files. The repo’s `.gitignore` already includes `.env` and `.env.local`.
- Rotate or **revoke** the token if it’s exposed or no longer needed:  
  Bitbucket → Personal settings → **API tokens** → find the token → **Revoke**.

---

## 6. After setup

Once the token is set in the environment that Cursor’s terminal uses, you can ask for a **code review of a PR** (e.g. “review PR #602”). The assistant will use `BITBUCKET_EMAIL` and `BITBUCKET_API_TOKEN` in `curl` to fetch the PR and its diff from the Bitbucket API and then run the review.

---

## References

- [Using API tokens | Bitbucket Cloud](https://support.atlassian.com/bitbucket-cloud/docs/using-api-tokens/)
- [API tokens | Bitbucket Cloud](https://support.atlassian.com/bitbucket-cloud/docs/api-tokens/)
- [API token permissions (scopes) | Bitbucket Cloud](https://support.atlassian.com/bitbucket-cloud/docs/api-token-permissions/)
- [Create an API token | Bitbucket Cloud](https://support.atlassian.com/bitbucket-cloud/docs/create-an-api-token/)
