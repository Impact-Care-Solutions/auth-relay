# employmenthero-oauth-callback

A single static page, hosted via GitHub Pages, used as the OAuth 2.0 redirect URI for the `payrate-update-2026-schads` project's Employment Hero integration.

## Why this exists

Employment Hero's OAuth implementation requires a real, publicly-hosted HTTPS redirect URI, it rejects both IP-literal (`127.0.0.1`) and `localhost` redirect URIs at the platform level (confirmed by testing, see `payrate-update-2026-schads/BRIEF.md`). Since the actual script that needs the authorisation code runs on a local machine with no public endpoint of its own, this page exists purely to catch the redirect, display the code so it can be copied into a terminal, and nothing else.

## How it works

1. The local script opens a browser to Employment Hero's authorise URL, with this page's URL set as the `redirect_uri`.
2. After login, Employment Hero redirects the browser here with `?code=...` in the URL.
3. This page's JavaScript reads the code from the URL and displays it. That's the entire function of this page.
4. You copy the code and paste it into the waiting terminal.

## Security posture

- **No network calls of any kind.** The code is read from the URL and displayed, it is never sent anywhere, there is no server-side logic, no analytics, no external resources.
- **No external dependencies.** No CDN scripts, no fonts, no images, everything needed is inline in `index.html`.
- **Strict Content-Security-Policy** (`default-src 'none'`, no framing, no external connections) set via a `<meta>` tag.
- **`noindex, nofollow`** so this page is never crawled or indexed.
- **`Referrer-Policy: no-referrer`** so the URL (and any code in it) is never leaked via a Referer header, even though no outbound requests exist to leak it through.
- **The code is stripped from the visible URL and browser history** via `history.replaceState` immediately on page load, so it doesn't linger in browser autocomplete/history after the page renders.
- **Nothing sensitive is stored.** No cookies, no localStorage/sessionStorage.
- This repo is deliberately public (GitHub Pages on this plan requires it), but contains no secrets, credentials, or logic beyond what's described above. The only sensitive value that ever touches this page (a short-lived, single-use OAuth authorisation code) exists only transiently in the visiting browser's memory, never in this repo's contents.

## Maintenance

This is a one-file utility, there should be no reason to add dependencies, a build step, or server-side code. If a change ever seems to need those, reconsider whether this is still the right approach, per the security posture above.
