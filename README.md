# actions-agent (CloudWaddie Agent)

A collection of GitHub **workflows** that run **cloudwaddie-agent** (an autonomous bot made by CloudWaddie) on @cloudwaddie-agent mentions in issue/PR comments.

## What it does
- Listens for **issue/PR comments** that mention `@cloudwaddie-agent`
- Runs oh-my-opencode as a dependency
- Uses free OpenCode models (no authentication required by default)
- Posts a helpful comment back via `gh`

## Setup

### Copy/Paste Workflows (Recommended)

Copy the workflow files from the [`.github/workflows/`](.github/workflows/) folder in this repository:

1. **Copy these files to your `.github/workflows/` folder:**
   - [`.github/workflows/fork-cleanup.yml`](.github/workflows/fork-cleanup.yml) - Cleans up fork branches after PR merge
   - [`.github/workflows/cloudwaddie-agent.yml`](.github/workflows/cloudwaddie-agent.yml) - Main agent workflow

2. **Add repo secrets:**
   - `GH_PAT` — GitHub PAT with repo scope
   - `OPENCODE_AUTH_JSON` — (optional) your OpenCode auth JSON for premium models

3. **Enable the workflow** in your repository's Actions tab.

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
