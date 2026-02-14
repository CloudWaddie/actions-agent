# CloudWaddie Agent Workflow Setup Guide

This workflow runs the **cloudwaddie-agent** bot automatically when mentioned in GitHub issues or pull requests.

## Prerequisites

- A GitHub repository (this can be a template repository)
- Repository admin access (to configure secrets)

## Setup Steps

### 1. Configure Repository Secrets

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
   # Follow prompts to enter your Anthropic API key
   ```
   
   **For OpenAI (ChatGPT)**:
   ```bash
   opencode auth openai
   # Follow prompts to enter your OpenAI API key
   ```
   
   **For Google Gemini**:
   ```bash
   opencode auth google
   # Follow prompts to enter your Google API key
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

4. **Copy the entire JSON output** (should look like):
   ```json
   {
     "anthropic": {
       "apiKey": "sk-ant-..."
     },
     "openai": {
       "apiKey": "sk-..."
     }
   }
   ```

5. **Add to repository secrets**:
   - Go to your repo → Settings → Secrets → Actions
   - Click **New repository secret**
   - Name: `OPENCODE_AUTH_JSON`
   - Value: Paste the entire JSON
   - Click **Add secret**

### 2. Add Bot as Repository Collaborator

**CRITICAL**: The bot account must have write access to the repository to add labels, reactions, and create PRs.

**Steps**:
1. Go to your repository's **Settings → Collaborators and teams** (or **Settings → Access** for organizations)
2. Click **"Add people"** or **"Invite a collaborator"**
3. Search for the bot user account (e.g., `cloudwaddie-agent`)
4. Select **"Write"** role (or **"Triage"** for read-only bots)
5. Click **"Add [username] to this repository"**
6. If the bot account has email notifications enabled, accept the invitation
   - Alternatively, the invitation can be accepted via: https://github.com/[OWNER]/[REPO]/invitations

**Why this is required**:
- Without collaborator access, the bot will fail with `403 Forbidden` or `404 Not Found` errors
- Even with a valid PAT token, GitHub checks if the token's owner has repository access
- The bot needs write access to:
  - Add and remove labels
  - Add reactions to issues/PRs/comments
  - Create pull requests
  - Push to feature branches

**Security Note**: The bot should **NOT** have admin access. Write access is sufficient and safer.

### 3. Enable Workflow Permissions

Go to your repository's **Settings → Actions → General**:

1. Scroll to **"Workflow permissions"**
2. Select **"Read and write permissions"**
3. Check **"Allow GitHub Actions to create and approve pull requests"**
4. Click **Save**

**Note**: These permissions apply to the built-in `GITHUB_TOKEN`, but the bot uses the `GH_PAT` secret which has its own permissions based on the collaborator access level.

### 4. Rename the Bot (Optional)

If you want to use a different bot name instead of `cloudwaddie-agent`:

1. Edit `.github/workflows/cloudwaddie-agent.yml`
2. Find and replace all instances of `cloudwaddie-agent` with your bot name
3. Update the workflow filename to match: `.github/workflows/your-bot-name.yml`
4. Commit and push changes

### 5. Test the Bot

#### Manual Test (Workflow Dispatch)

1. Go to **Actions** tab in your repository
2. Select **"CloudWaddie Agent"** workflow
3. Click **"Run workflow"**
4. Optionally enter a test prompt
5. Click **"Run workflow"**
6. Check the workflow run logs to verify it works

#### Real Test (Issue Comment)

1. Create a test issue in your repository
2. Add a comment mentioning `@cloudwaddie-agent` (or your custom bot name)
3. The workflow should trigger automatically
4. The bot should add an 👀 reaction and respond with a comment

## Usage

### How to Trigger the Bot

1. **In any issue or pull request**, add a comment that mentions `@cloudwaddie-agent`
2. The bot will:
   - Add an 👀 reaction (acknowledging it saw your message)
   - Add a "cloudwaddie-agent: working" label
   - Process your request
   - Reply with a comment
   - Change 👀 to 👍 when done
   - Remove the working label

### Who Can Use the Bot

Only users with the following permissions can trigger the bot:
- **OWNER** (repository owner)
- **MEMBER** (organization members)
- **COLLABORATOR** (users with write access)
- **CONTRIBUTOR** (users who have contributed to the repo)

This prevents spam from random users.

## Managing Workflow Notifications

### Disabling Workflow Notification Emails

By default, GitHub sends email notifications for every workflow run, which can be overwhelming for active bots.

**To disable workflow notifications for yourself**:

1. Go to your **GitHub account settings**: https://github.com/settings/notifications
2. Scroll to **"Actions"** section
3. Uncheck **"Send notifications for failed workflows only"** and **"Send notifications for all workflows"**
4. Or, configure per-repository:
   - Go to the repository → **Watch** dropdown (top right)
   - Select **"Custom"**
   - Uncheck **"Actions"** under notification types
   - Click **Apply**

**For organization owners**:
- You can set organization-wide notification defaults in organization settings
- Individual users can still override these with their personal preferences

**Alternative: Email filters**:
- Filter workflow emails by subject containing `[cloudwaddie-agent]` or sender `notifications@github.com`
- Auto-archive or label them in your email client

## Troubleshooting

### Bot doesn't respond

**Check**:
1. Did you mention `@cloudwaddie-agent` in your comment?
2. Are you a contributor to the repository?
3. Is the workflow file enabled? (Actions → Workflows → CloudWaddie Agent → Enable)
4. Check the Actions tab for workflow run logs

### Bot fails with "403 Forbidden" or "404 Not Found" errors

**Symptoms**:
- Workflow runs but fails with errors like:
  - `HTTP 404: Not Found (https://api.github.com/repos/OWNER/REPO/labels)`
  - `GraphQL: [bot-name] does not have the correct permissions to execute AddLabelsToLabelable`
  - `failed to update issue: 403 Forbidden`

**Root Cause**: The bot user account is not added as a repository collaborator.

**Solution**:
1. Go to repository **Settings → Collaborators and teams**
2. Verify the bot account (e.g., `cloudwaddie-agent`) is listed
3. If not, follow [Step 2: Add Bot as Repository Collaborator](#2-add-bot-as-repository-collaborator)
4. Ensure the bot has **Write** role (not just Read)
5. Accept the collaboration invitation if pending

**To verify the bot's access**:
```bash
# As the bot user, check access level
gh api /repos/OWNER/REPO/collaborators/BOT_USERNAME/permission
# Should return: "permission": "write" or "admin"
```

### Bot fails with "Invalid token" or "Bad credentials"

**Symptoms**:
- Workflow fails with authentication errors
- `gh auth status` shows "Not logged in"

**Root Cause**: The `GH_PAT` secret is invalid, expired, or has insufficient scopes.

**Solution**:
1. Create a new Personal Access Token:
   - Go to https://github.com/settings/tokens/new
   - Select **Classic** token
   - Check `repo` and `workflow` scopes
   - Generate and copy the token
2. Update the repository secret:
   - Go to repository **Settings → Secrets → Actions**
   - Update `GH_PAT` with the new token
3. Re-run the workflow

**Security Warning**: Never share or expose PAT tokens in logs, code, or public places.

### Bot responds to itself (infinite loop)

This shouldn't happen - the workflow has a check to prevent this. If it does:
1. Check the workflow file has: `if: github.event_name == 'workflow_dispatch' || github.event.comment.user.login != 'cloudwaddie-agent'`
2. The validation step should check for `@cloudwaddie-agent` mentions

### Bot times out or gets cancelled

The workflow has no timeout by default (uses GitHub's 6-hour limit). If you see timeouts:
1. Check the workflow doesn't have `timeout-minutes: 10` or similar
2. Remove any restrictive timeout settings

### Bot accidentally pushes to main branch

**Prevention**: The workflow has built-in safeguards:
- Push step checks if current branch is `main` or `master` and blocks the push
- Workflow instructions tell the bot to use fork workflow and create PRs

**If this happens**:
1. Check the "Push changes" step in the workflow has the branch check
2. Enable branch protection rules:
   - Go to **Settings → Branches → Add rule**
   - Branch name pattern: `main` (or `master`)
   - Check **"Require pull request reviews before merging"**
   - Check **"Restrict who can push to matching branches"**
   - Save changes

### Bot gives poor responses

If using free models (`opencode/big-pickle`):
- Responses may be less accurate than premium models
- Consider adding `OPENCODE_AUTH_JSON` secret with premium API keys

## Models Used

### Default (Free)
- `opencode/big-pickle` - Free OpenCode model

### With Authentication (Premium)
The bot uses different models based on available credentials:
- **Sisyphus** (main agent): Claude Opus 4.5 High
- **Oracle** (consultation): GPT 5.2 Medium
- **Librarian** (docs/search): Various models
- **Explore** (codebase): Claude Haiku 4.5

## Cost Considerations

### Free Usage
- No costs if you use free OpenCode models
- GitHub Actions minutes usage (free tier: 2,000 minutes/month for private repos, unlimited for public)

### Paid Usage (with premium models)
- API costs from your providers (Anthropic, OpenAI, Google)
- Each workflow run consumes API tokens based on:
  - Comment length
  - Response complexity
  - Number of agent calls

**Estimate**: ~$0.01 - $0.10 per workflow run (depends on model and complexity)

## Advanced Configuration

### Change Default Model

Edit the workflow file and modify:
```yaml
# Set default model to opencode/big-pickle
CONFIG=~/.config/opencode/opencode.json
if [ -f "$CONFIG" ]; then
  jq '(.model = "opencode/big-pickle")' "$CONFIG" > /tmp/oc.json && mv /tmp/oc.json "$CONFIG"
```

Change `opencode/big-pickle` to your preferred model.

### Adjust Bot Behavior

The bot's behavior is controlled by the prompt in the workflow file:
```yaml
PROMPT=$(cat <<'PROMPT_EOF'
You are cloudwaddie-agent, an autonomous bot made by CloudWaddie.
...
PROMPT_EOF
)
```

You can customize this prompt to change how the bot responds.

## Support

- **Issues**: Open an issue in this repository
- **Discussions**: Use GitHub Discussions for questions
- **Documentation**: See https://opencode.ai/docs for OpenCode details

## License

This workflow template is provided as-is for public use.
