# actions-agent (Sigma Agent by CloudWaddie)

A GitHub **workflow template** that runs **sigma-agent** (an autonomous bot made by CloudWaddie) on @sigma-agent mentions in issue/PR comments.

## What it does
- Listens for **issue/PR comments** that mention `@sigma-agent`
- Runs oh-my-opencode as a dependency
- Posts a helpful comment back via `gh`

## Setup
1. **Create a repo from this template** (or copy the workflow file).
2. Add repo secrets:
   - `GH_PAT` — GitHub PAT with repo scope
   - `OPENCODE_AUTH_JSON` — your OpenCode auth JSON
3. Ensure the workflow file is enabled.

## Notes
- The workflow only runs on mentions by a **contributor** (OWNER/MEMBER/COLLABORATOR/CONTRIBUTOR).
- It uses **OpenCode Zen** via the `--opencode-zen=yes` install flag.

## Credits
Based on the Sisyphus agent workflow from https://github.com/code-yeongyu/oh-my-opencode
