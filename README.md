# actions-agent (CloudWaddie Agent)

A GitHub **workflow template** that runs **cloudwaddie-agent** (an autonomous bot made by CloudWaddie) on @cloudwaddie-agent mentions in issue/PR comments.

## What it does
- Listens for **issue/PR comments** that mention `@cloudwaddie-agent`
- Runs oh-my-opencode as a dependency
- Uses free OpenCode models (no authentication required by default)
- Posts a helpful comment back via `gh`

## Setup
1. **Create a repo from this template** (or copy the workflow file).
2. Add repo secrets:
   - `GH_PAT` — GitHub PAT with repo scope
   - `OPENCODE_AUTH_JSON` — (optional) your OpenCode auth JSON for premium models
3. Ensure the workflow file is enabled.

## Notes
- The workflow only runs on mentions by a **contributor** (OWNER/MEMBER/COLLABORATOR/CONTRIBUTOR).
- Uses **free OpenCode models** by default (opencode/big-pickle)
- Authentication is optional - works with free models out of the box
