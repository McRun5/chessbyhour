# Chess by Hour

A free, single-page site that shows when a [Chess.com](https://www.chess.com) player wins and loses the most. Enter a username and it reads their public games, groups results by hour of day and day of the week, and displays the pattern as a 24-hour dial, a weekday × hour heatmap, and a shareable image.

No backend, no accounts, no build step. Everything runs in the visitor's browser.

**Planned domain:** chessbyhour.com

---

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole app — markup, styles, and JavaScript in one file. |
| `privacy.html` | Privacy policy. |
| `contact.html` | Contact page. |
| `og-image.png` | 1200×630 link-preview image used by `og:image` / `twitter:image`. |
| `favicon.svg` | Browser-tab icon (a small dial motif). |

Deploy by uploading all five files to the same folder on any static host (Netlify, Vercel, GitHub Pages, Cloudflare Pages, etc.). There is nothing to build or install.

---

## Features

- **24-hour dial** — one wedge per hour; wedge length = games played, color = win rate vs. the player's overall average.
- **Weekday × hour heatmap** — same data, one square per day/hour combination, toggle between win-rate and volume views.
- **Win/loss bar chart** — wins up, losses down, per hour.
- **Weekday cards** — win rate for each day of the week.
- **Best/toughest hour ranking** — ignores hours below a minimum-games threshold to avoid small-sample noise.
- **Persona label** — a shareable one-line label ("Night owl", "Prime-time player", "Weekend warrior", etc.) based on when the player's games cluster.
- **Filters** — time control (bullet/blitz/rapid/daily), period (3–24 months or all games), minimum games per hour, and a full IANA time-zone picker (Chess.com doesn't expose a player's time zone, so the visitor chooses one; if the player's country maps to a single zone, a one-click suggestion appears).
- **Player header** — avatar, title, name, and self-reported location/country pulled from the public profile.
- **Shareable image** — a canvas-rendered 1080×1350 card (dial, persona, best/toughest hour, domain footer) with buttons for the Web Share sheet, X, Instagram, WhatsApp, copy-to-clipboard, and direct download.
- **Sticky compact search bar** — the intro collapses into a slim top bar once analysis starts.
- **Deep links** — `index.html#username` loads that player automatically.
- **Dark/light theme** — follows the OS/browser preference automatically.

---

## How it works

1. The browser calls Chess.com's public [Published-Data API](https://www.chess.com/news/view/published-data-api) directly:
   - `GET /pub/player/{username}` — profile (name, title, avatar, location, country).
   - `GET /pub/player/{username}/games/archives` — list of monthly archive URLs.
   - `GET /pub/player/{username}/games/{YYYY}/{MM}` — games for each month (fetched with limited concurrency and retry-on-429).
2. Each game is classified as a win, draw, or loss for the searched player, then bucketed by the hour (and weekday) its `end_time` falls on, converted to the visitor's selected time zone with `Intl.DateTimeFormat`.
3. Aggregates are computed client-side and rendered as SVG (dial), CSS grid (heatmap/bars), and a `<canvas>` (share image).

No game or username data is sent to or stored by this site — see `privacy.html`.

---

## Known limitations

- Hour = when a game **ended**, not when it started.
- Draws count as "not won" in win-rate calculations.
- Only standard chess games are counted; variants are skipped.
- Chess.com doesn't publish a player's real time zone, so results depend on the zone the visitor selects.
- Large accounts (tens of thousands of games) can take 10–20 seconds to load.
- The share image's domain footer and link fallback are hardcoded to `chessbyhour.com` (see `SITE` constant in `index.html`) — update this if the domain changes.

---

## Not affiliated with Chess.com

This project uses Chess.com's public API but is not affiliated with, endorsed by, or officially connected to Chess.com.
