# design-tools

Small collection of two independent, standalone client-side web tools authored by leonides delgado. Each is a single self-contained HTML file (inline HTML + CSS + vanilla JavaScript, Spanish UI). There is no backend, database, package manager, or build step.

- `timer.html` — "Timer for design sessions": visual countdown timer with an analog clock face, presets, custom durations, and a "minuto a minuto" sequence mode.
- `linkedin-formatter.html` — "LinkedIn Formatter": converts markdown-style markers (`**bold**`, `_italic_`) into LinkedIn-ready Unicode text, with a live character counter and copy-to-clipboard.

## Cursor Cloud specific instructions

- No install/build/lint/test tooling exists. There is no `package.json`, no lockfiles, no bundler, and no automated test or lint setup. Do not expect `npm install`/`pnpm`/`pytest`/etc. to apply here.
- The only "service" is a static file server. Serve the repo root and open the tools in a browser, e.g. `python3 -m http.server 8000` (from `/workspace`), then visit `http://localhost:8000/timer.html` and `http://localhost:8000/linkedin-formatter.html`. Opening the files directly via `file://` also works.
- The two HTML files are fully independent and share no code or assets. Edit each file in place; changes appear on browser refresh (no hot reload, no compilation).
- The only external dependency is the Google Fonts CDN (Fira Sans). Both pages degrade gracefully to system fonts when offline, so lack of internet does not break functionality.
