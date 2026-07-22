# auth-relay

A single static page, hosted via GitHub Pages, used as an OAuth 2.0 redirect URI for an internal script that has no public endpoint of its own.

## Why this exists

Some OAuth providers require a real, publicly-hosted HTTPS redirect URI and reject loopback-style redirects (`127.0.0.1`, `localhost`) at the platform level. Since the script that actually needs the authorisation code runs on a local machine with no public endpoint, this page exists purely to catch the redirect, display the code so it can be copied into a terminal, and nothing else.

Deliberately generic: this repo and page name, and everything in them, avoid naming the specific provider or internal project this is used for. A public page that says exactly which vendor/system an organisation integrates with is free reconnaissance for a targeted phishing attempt, there's no benefit to disclosing that here.

## How it works

1. The local script opens a browser to the provider's authorise URL, with this page's URL set as the `redirect_uri`.
2. After login, the provider redirects the browser here with `?code=...` in the URL.
3. This page's JavaScript reads the code from the URL and displays it. That's the entire function of this page.
4. You copy the code and paste it into the waiting terminal.

## Security posture

- **No network calls of any kind.** The code is read from the URL and displayed, it is never sent anywhere, there is no server-side logic, no analytics, no external resources.
- **No external dependencies.** No CDN scripts, no fonts, no images, everything needed is inline in `index.html`.
- **Content-Security-Policy** via `<meta>`: `default-src 'none'`, inline script/style locked to their exact SHA-256 hash (not `'unsafe-inline'`), no images, no outbound connections, no base/form actions.
- **`noindex, nofollow`** meta tag, plus a `robots.txt` disallowing all crawling, so this page is never indexed.
- **`Referrer-Policy: no-referrer`** so the URL (and any code in it) is never leaked via a Referer header, even though no outbound requests exist to leak it through.
- **JS-based frame-busting** (`if (window.top !== window.self) { ... }`). GitHub Pages cannot set real HTTP response headers, so `X-Frame-Options`/CSP `frame-ancestors` (framing/clickjacking protection) cannot be enforced here, a `<meta>` tag is explicitly ignored by browsers for that directive. This script-based check is a best-effort fallback, not equivalent to a real header. If stronger, properly-enforced header-level protection is ever needed, move this to a host that supports custom response headers (e.g. Cloudflare Pages), GitHub Pages fundamentally can't do it.
- **The code is stripped from the visible URL and browser history** via `history.replaceState` immediately on page load, so it doesn't linger in browser autocomplete/history after the page renders.
- **Nothing sensitive is stored.** No cookies, no localStorage/sessionStorage.
- This repo is public (GitHub Pages on this org's plan requires it), but contains no secrets, credentials, or identifying details of what it's used for. The only sensitive value that ever touches this page (a short-lived, single-use OAuth authorisation code) exists only transiently in the visiting browser's memory, never in this repo's contents.

## Maintenance

This is a one-file utility, there should be no reason to add dependencies, a build step, or server-side code. If a change ever seems to need those, reconsider whether this is still the right approach, per the security posture above.

If `index.html`'s inline `<script>` or `<style>` content changes, the CSP hashes in the `<meta>` tag must be regenerated (SHA-256 of the exact element content, base64-encoded), a mismatched hash will cause the browser to silently block the script/style entirely.
