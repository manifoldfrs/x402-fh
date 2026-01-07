# Documentation Guide

## Package Identity
- GitBook documentation source for x402.org
- Markdown files with `SUMMARY.md` as table of contents

## Directory Structure
- `core-concepts/` — Protocol explanations (HTTP 402, client-server, facilitator, wallet)
- `getting-started/` — Quickstart guides for buyers and sellers
- `guides/` — How-to guides (MCP server, v1→v2 migration)
- `README.md` — Welcome/landing page
- `SUMMARY.md` — Table of contents (GitBook navigation)

## Conventions
- ✅ DO: Add new pages to `SUMMARY.md` for GitBook navigation
- ✅ DO: Include code examples from real SDK files (not made-up snippets)
- ✅ DO: Link to relevant specs in `specs/` for protocol details
- ✅ DO: Use diagrams for complex flows (store in `static/` or inline)
- ❌ DON'T: Duplicate protocol details from `specs/` — link instead
- ❌ DON'T: Add pages without updating `SUMMARY.md`
- **Git: NEVER commit changes; leave commits to the user**

## Touch Points / Key Files
- `README.md` — Landing page
- `SUMMARY.md` — Table of contents (MUST update when adding pages)
- `core-concepts/*.md` — Conceptual documentation
- `getting-started/*.md` — Quickstart guides
- `guides/*.md` — How-to guides

## JIT Index Hints
- `find . -name "*.md"`
- `git grep -n "quickstart" .`
- `cat SUMMARY.md` (to see current structure)

## Common Gotchas
- `SUMMARY.md` controls GitBook navigation; pages not listed won't appear
- Images/diagrams go in project root `static/` directory
- Code examples should reference actual SDK file paths

## Pre-PR Checks
- All links work (no broken references)
- New pages added to `SUMMARY.md`
- Code examples are from actual SDK files and compile
