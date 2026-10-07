# Instructions for Claude / other coding agents

This is a single-purpose static site, not a Python project (no `uv`, no `pyproject.toml` needed here, that's the convention for other repos under `ImpactCare/`, this one is intentionally simpler).

## What this repo is

A generic OAuth 2.0 redirect-URI landing page used by an internal script that has no public endpoint of its own, hosted on Cloudflare Pages. See `README.md` for the full why/how.

**Every file in this repo is served publicly at the page's origin**, including this file and `README.md`. Write nothing here that you would not publish.

## Rules for changes here

- **Never name the specific provider, vendor, or internal project this is used for**, in the repo name, page content, file contents, commit messages, PR text, or anywhere else. Not even as an example of what not to name. A public page advertising which vendor/system an organisation integrates with is free intel for a targeted phishing attempt. Keep everything generic ("an OAuth provider", "an internal script").
- **Do not add an index or navigation file** (such as a `MAP.yaml`) that names internal repos, systems or people. Other repos may point at this one; this one points at nothing.
- **No new dependencies, no build step, no server-side code.** This must stay a static `index.html`, `_headers` and `robots.txt` that Cloudflare Pages serves as-is. If a task seems to require more than that, stop and confirm with the user before adding anything, this repo's entire security posture depends on staying this simple.
- **No network calls from the page.** Nothing in `index.html` should ever call `fetch`, `XMLHttpRequest`, load an external script/font/image, or otherwise send data anywhere. The whole point is that the authorisation code never leaves the user's own browser via this page.
- **Preserve the security posture.** The `_headers` file (CSP, `X-Frame-Options`, `frame-ancestors`, `Referrer-Policy`, `Permissions-Policy`), the CSP `<meta>` tag (hash-based script-src/style-src, not `'unsafe-inline'`), `noindex` robots tag + `robots.txt`, and the JS frame-busting fallback are all load-bearing, don't remove or loosen them without explicit confirmation.
- **If the inline `<script>` or `<style>` content changes**, even a comment, regenerate the corresponding CSP hash (SHA-256 of the exact element text content, base64-encoded) and update it in both the `<meta>` tag and `_headers`, or the browser will silently block the script/style.
- **This repo is public**, never add secrets, tokens, or credentials of any kind to it.

## Deploying changes

Merging to the default branch deploys. Cloudflare Pages builds from this repo and serves the result. There is no other deploy step. After a merge, check the live page returns 200 and still sends the `_headers` values.
