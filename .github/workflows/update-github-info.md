---
name: update-github-info
description: Refresh Mona's GitHub Info content from official GitHub sources.
intent: Keep Mona's GitHub Info content current with practical, sourced GitHub updates proposed through a pull request.
on:
  schedule: daily
  workflow_dispatch:
permissions:
  contents: read
  copilot-requests: write
strict: true
network:
  allowed:
    - awesome-copilot.github.com
    - defaults
    - github.blog
    - github.com
# The Copilot CLI gates network-capable shell commands (curl/wget/fetch) behind a
# separate per-domain URL approval on top of the firewall allowlist and the bash
# tool allowlist below; --allow-url pre-approves it so curl isn't denied in
# headless CI (no interactive user to approve it). See github/copilot-cli#3165
# and the "Allowing and denying tool use" Copilot CLI docs.
engine:
  id: copilot
  args:
    - "--allow-url=github.blog"
    - "--allow-url=awesome-copilot.github.com"
    - "--allow-url=github.com"
tools:
  github:
    mode: local
    toolsets: [repos]
  edit: true
  # The Copilot engine exposes no web-fetch tool; external sources are fetched with curl through the firewall allowlist.
  bash: ["curl:*"]
safe-outputs:
  create-pull-request:
    title-prefix: "[github-info] "
    allowed-files:
      - site/content/github-info.md
---

# Update GitHub Info

Read `notes/mona-notes.md` before drafting content. Use GitHub repository API tools, not terminal commands, to read repository guidance and reference files.

Use `curl` to retrieve and review these official sources, then read the returned HTML for the update entries:

- https://github.blog/latest/
- https://github.blog/changelog/
- https://awesome-copilot.github.com/workflows/

Fetch each URL with its own plain, single-command `curl` call, for example `curl -sL -m 30 <url> -o /tmp/gh-aw/agent/<name>.html`. Do not combine multiple commands into one call (no `&&`, `;`, `|`, or multi-line scripts) and do not add extra flags beyond `-s`/`-L`/`-m`/`-o` — the sandbox only auto-approves plain, atomic `curl` invocations and will deny anything more complex. Read each downloaded file with a separate tool call afterwards.

Update `site/content/github-info.md` only when the sources contain practical, current GitHub updates that fit Mona's editorial notes. Keep summaries short, include the official source URL and date for each selected update, and do not invent details.

When there is a material update, use the configured `create-pull-request` safe output to open a pull request for Mona to review. Include a concise summary of the selected updates and their source URLs in the pull request body. Do not write directly to the default branch.

Call `noop` with a brief reason when the sources contain no suitable new updates or the content already reflects the relevant information.