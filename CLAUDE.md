# Instructions for Claude / other coding agents

This is a single-purpose static site, not a Python project (no `uv`, no `pyproject.toml` needed here, that's the convention for other repos under `ImpactCare/`, this one is intentionally simpler).

## What this repo is

An OAuth 2.0 redirect-URI landing page for the `payrate-update-2026-schads` project (a sibling repo under `ImpactCare/`), hosted via GitHub Pages. See `README.md` for the full why/how.

## Rules for changes here

- **No new dependencies, no build step, no server-side code.** This must stay a single static `index.html` that GitHub Pages can serve as-is. If a task seems to require more than that, stop and confirm with the user before adding anything, this repo's entire security posture depends on staying this simple.
- **No network calls from the page.** Nothing in `index.html` should ever call `fetch`, `XMLHttpRequest`, load an external script/font/image, or otherwise send data anywhere. The whole point is that the authorisation code never leaves the user's own browser via this page.
- **Preserve the security headers.** The CSP `<meta>` tag, `noindex` robots tag, and `no-referrer` policy in `index.html` are load-bearing, don't remove or loosen them without explicit confirmation.
- **This repo is public** (required for GitHub Pages on the org's current plan), never add secrets, tokens, or credentials of any kind to it.

## Deploying changes

Push to the default branch, GitHub Pages (configured in repo Settings → Pages) rebuilds automatically. There is no other deploy step.
