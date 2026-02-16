# actions-agent (CloudWaddie Agent)

GitHub Actions workflows that run **cloudwaddie-agent** (an autonomous bot made by CloudWaddie) on @cloudwaddie-agent mentions in issue/PR comments.

## What it does
- Listens for **issue/PR comments** that mention `@cloudwaddie-agent`
- Runs oh-my-opencode as a dependency
- Uses free OpenCode models (no authentication required by default)
- Posts a helpful comment back via `gh`

## Quick Setup

### Copy/Paste Workflows (Recommended)

Copy the workflow files from the [`workflows/`](workflows/) folder in this repository:

1. **Copy these files to your `.github/workflows/` folder:**
   - [`workflows/fork-cleanup.yml`](workflows/fork-cleanup.yml) - Cleans up fork branches after PR merge
   - [`workflows/cloudwaddie-agent.yml`](workflows/cloudwaddie-agent.yml) - Main agent workflow

2. **Add repo secrets:**
   - `GH_PAT` — GitHub PAT with repo scope
   - `OPENCODE_AUTH_JSON` — (optional) your OpenCode auth JSON for premium models

3. **Enable the workflow** in your repo's Actions tab.

## Security Features
- **Write workflow permissions** (`contents: write`, `pull-requests: write`) for fork and PR workflow
- All write operations (comments, reactions, git operations) are performed via the `GH_PAT` token through `gh` CLI
- **No label management** - uses emoji reactions instead (👀 when working, 👍 when done)
- The bot uses **fork-based workflow**: forks the repo, pushes to fork, creates PR to main

## Notes
- The workflow only runs on mentions by a **contributor** (OWNER/MEMBER/COLLABORATOR/CONTRIBUTOR)
- Uses **free OpenCode models** by default (opencode/big-pickle)
- Authentication is optional - works with free models out of the box
- The bot **always posts at least one comment** explaining what it did or found

## Setup Details

### Configure Repository Secrets

Go to your repository's **Settings → Secrets and variables → Actions** and add the following secrets:

#### Required Secret: `GH_PAT`

**Purpose**: Allows the bot to read issues/PRs and post comments.

**Steps to create**:
1. Go to https://github.com/settings/tokens/new
2. Select **"Classic"** token (not fine-grained)
3. Give it a descriptive name (e.g., "cloudwaddie-agent-bot")
4. Set expiration (recommend: no expiration for persistent bots)
5. Select scopes:
   - ✅ `repo` (Full control of private repositories)
   - ✅ `workflow` (Update GitHub Action workflows)
6. Click **"Generate token"**
7. Copy the token (you won't see it again!)
8. In your repo → Settings → Secrets → Actions → **New repository secret**
9. Name: `GH_PAT`
10. Value: Paste the token
11. Click **Add secret**

#### Optional Secret: `OPENCODE_AUTH_JSON`

**Purpose**: Use premium OpenCode models (Claude, GPT, etc.) instead of free models.

**Without this secret**: The bot uses free OpenCode models like `opencode/big-pickle` (works fine for basic tasks).

**With this secret**: The bot can use premium models for better responses.

**Steps to get your OpenCode auth JSON**:

1. **Install OpenCode CLI** (on your local machine):
   ```bash
   curl -fsSL https://opencode.ai/install | bash
   ```

2. **Authenticate with your providers** (pick at least one):
   
   **For Claude (Anthropic)**:
   ```bash
   opencode auth anthropic
   ```
   
   **For OpenAI (ChatGPT)**:
   ```bash
   opencode auth openai
   ```
   
   **For Google Gemini**:
   ```bash
   opencode auth google
   ```

3. **Extract the auth JSON**:
   
   **On Linux/Mac**:
   ```bash
   cat ~/.local/share/opencode/auth.json
   ```
   
   **On Windows**:
   ```powershell
   type %USERPROFILE%\AppData\Local\opencode\auth.json
   ```

4. **Add to repository secrets**:
   - Go to your repo → Settings → Secrets → Actions
   - Click **New repository secret**
   - Name: `OPENCODE_AUTH_JSON`
   - Value: Paste the entire JSON
   - Click **Add secret**

## Troubleshooting

### Bot doesn't respond

**Check**:
1. Did you mention `@cloudwaddie-agent` in your comment?
2. Are you a contributor to the repository?
3. Is the workflow file enabled? (Actions → Workflows → CloudWaddie Agent → Enable)
4. Check the Actions tab for workflow run logs

### Bot fails with authentication errors

**Solution**:
1. Verify the PAT token has the required scopes:
   - ✅ `repo` (Full control of private repositories)
   - ✅ `workflow` (Update GitHub Action workflows)
2. If the token is expired or has wrong scopes, create a new one

## License

This workflow is provided as-is for public use.
