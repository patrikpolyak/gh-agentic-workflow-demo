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
    - defaults
    - github.blog
    - github.com
tools:
  github:
    mode: local
    toolsets: [repos]
  edit: true
  web-fetch: {}
safe-outputs:
  create-pull-request:
    title-prefix: "[github-info] "
    allowed-files:
      - site/content/github-info.md
---

# Update GitHub Info

Read `notes/mona-notes.md` before drafting content. Use GitHub repository API tools, not terminal commands, CLI commands, or sandboxed commands, to read repository guidance and reference files.

Fetch and review these official sources:

- https://github.blog/latest/
- https://github.blog/changelog/

Update `site/content/github-info.md` only when the sources contain practical, current GitHub updates that fit Mona's editorial notes. Keep summaries short, include the official source URL and date for each selected update, and do not invent details.

When there is a material update, use the configured `create-pull-request` safe output to open a pull request for Mona to review. Include a concise summary of the selected updates and their source URLs in the pull request body. Do not write directly to the default branch.

Call `noop` with a brief reason when the sources contain no suitable new updates or the content already reflects the relevant information.