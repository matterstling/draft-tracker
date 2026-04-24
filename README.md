# Football Draft Live Tracker

A single-file web app that tracks the Football Draft in real time. Polls ESPN's public draft API, shows recent picks with team logos, keeps a synced 10-minute clock, and plays fireworks + a celebratory modal when each pick drops.

Built for the 2026 Draft (Round 1 defaults), sized to fit the top-left third of a monitor, and fully static — just serve `index.html`.

## Features

- **Live ESPN feed** via `site.web.api.espn.com` (routed through a CORS proxy since ESPN's API doesn't send browser-friendly headers)
- **Fireworks + modal interruptor** when a new pick is announced
- **Real 10-minute pick clock**, synced to ESPN pick timestamps when available
- **Team logos** pulled from ESPN's CDN (`a.espncdn.com/i/teamlogos/nfl/500/{abbr}.png`)
- **Trade attribution** — shows "via [original team]" for picks acquired via trade
- **Horizontal scrolling recent-picks strip**, unlimited history
- **Graceful offline fallback** — clock runs locally if the feed is down
- **Debug button** — shows the raw ESPN response for the latest pick (useful when fields change)

## Run locally

Just open `index.html` in a browser, or serve it:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Deploy to matterstling.com/football/nfldraft

This is a static single-file site. A few common patterns depending on how your main site is hosted:

### Option A — GitHub Pages (if matterstling.com is on Pages)

1. Push this repo to GitHub.
2. In the repo that serves `matterstling.com`, add this file at `/football/nfldraft/index.html`.
3. Commit + push. Pages picks it up within a minute.

### Option B — Separate repo, subpath routing

If `matterstling.com` is served from one repo and you want `/football/nfldraft` to come from this repo, use a redirect or a reverse proxy (Netlify/Vercel/Cloudflare Pages all support this).

### Option C — Manual copy

Drop the `index.html` into the `football/nfldraft/` directory of whatever hosts your main site and push.

## Configuration

Open `index.html` and look for the top of the `<script>` block:

```javascript
const ESPN_DRAFT_URL = 'https://site.web.api.espn.com/apis/site/v2/sports/football/nfl/draft?season=2026&round=1';
```

- Change `season=2026` to the current year for future drafts.
- Change `round=1` to `round=2` etc. for later rounds, or remove the parameter to fetch all rounds that have data.

Other knobs:

- `POLL_INTERVAL_MS = 20000` — how often to poll ESPN (20 seconds default).
- `PICK_CLOCK_SECONDS = 600` — NFL first-round clock (10 minutes). Later rounds are shorter (Round 2 is 7 min, Rounds 3-6 are 5 min, Round 7 is 4 min).
- `FETCH_STRATEGIES` — the ordered list of CORS proxies the app tries. Swap the primary if you prefer a different proxy.

## Data source

- **Draft data**: ESPN public API (unofficial, unauthenticated).
- **Team logos**: ESPN CDN.
- **Pick order + trade info**: Static fallback in `staticOrder` array, sourced from Tankathon and accurate as of 4/23/2026. Any mid-draft trades will be reflected in ESPN's feed and override the static data.

## Known limitations

- ESPN's feed isn't an official API, so field names can shift. If positions/schools stop showing up in a future draft, use the DEBUG button to see the current response shape and update the `extractPicks` function's field coverage.
- CORS proxies are third-party services — if they're rate-limited or down, the app falls back to a local clock and shows a status banner.
- Pre-draft (before picks start dropping), the app displays the pre-populated order with "(pending)" player names.

## License

MIT — use it, break it, fork it.
