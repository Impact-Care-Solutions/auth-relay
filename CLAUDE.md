# Instructions for Claude / other coding agents

This is a single-purpose static site, not a Python project (no `uv`, no `pyproject.toml` needed here, that's the convention for other repos under `ImpactCare/`, this one is intentionally simpler).

## What this repo is

A generic OAuth 2.0 redirect-URI landing page used by an internal script that has no public endpoint of its own, hosted via GitHub Pages. See `README.md` for the full why/how.

## Rules for changes here

- **Never name the specific provider, vendor, or internal project this is used for**, in the repo name, page content, commit messages, or anywhere else. A public page advertising which vendor/system an organisation integrates with is free intel for a targeted phishing attempt. Keep everything generic ("an OAuth provider", "an internal script"), never "Employment Hero" or similar.
- **No new dependencies, no build step, no server-side code.** This must stay a single static `index.html` (plus `robots.txt`) that GitHub Pages can serve as-is. If a task seems to require more than that, stop and confirm with the user before adding anything, this repo's entire security posture depends on staying this simple.
- **No network calls from the page.** Nothing in `index.html` should ever call `fetch`, `XMLHttpRequest`, load an external script/font/image, or otherwise send data anywhere. The whole point is that the authorisation code never leaves the user's own browser via this page.
- **Preserve the security posture.** The CSP `<meta>` tag (hash-based script-src/style-src, not `'unsafe-inline'`), `noindex` robots tag + `robots.txt`, `no-referrer` policy, and the JS frame-busting fallback are all load-bearing, don't remove or loosen them without explicit confirmation.
- **If the inline `<script>` or `<style>` content changes**, regenerate the corresponding CSP hash (SHA-256 of the exact element text content, base64-encoded) and update the `<meta>` tag, or the browser will silently block the script/style.
- **This repo is public** (required for GitHub Pages on the org's current plan), never add secrets, tokens, or credentials of any kind to it.

## Deploying changes

Push to the default branch, GitHub Pages (configured in repo Settings → Pages) rebuilds automatically. There is no other deploy step.
