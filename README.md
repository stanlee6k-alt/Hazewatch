# Haze Watch — Malaysia

**Live air quality for the Klang Valley and Penang, refreshed every five minutes.**

During haze season the question is rarely "is the air bad?" — it's *how* bad, *where*, and *is it heading my way*. Haze Watch answers all three on one screen: measured readings from the nearest government monitoring station to each of six places, a five-day outlook, and the wind direction that decides whether smoke is about to arrive.

It's a single HTML file. No build step, no dependencies, no server, no tracking.

![Haze Watch showing six Malaysian locations with measured US AQI readings, station attribution, five-day outlook bars and wind direction](screenshot-light.png)

*Example view. Readings shown are illustrative. There's a [dark theme](screenshot-dark.png) too, and it follows your system setting by default.*

---

## What it tracks

| Klang Valley & Negeri Sembilan | Pulau Pinang |
|---|---|
| Kuala Lumpur | Georgetown |
| Semenyih | Bayan Lepas |
| Seremban | Bukit Mertajam |

For each location:

- **US AQI as measured** at the nearest DOE station, with its category and how far it sits from today's average
- **The station itself** — named, with its distance, so a reading is never passed off as being taken where you are
- **PM2.5 and PM10** concentrations in µg/m³
- **Malaysian API** — an estimate from the station's 24-hour average, using the DOE sub-index formulas
- **A five-day outlook** — each day's forecast average with its min–max range
- **Wind direction and speed**, with a **Sumatra track** flag when the wind is arriving from the bearing sector that carries transboundary smoke

A **Compare** tab puts all six locations' outlooks on one chart with your alert threshold drawn across it, plus current readings as a table.

## Alerts

Set a threshold in Settings — the default is US AQI 101, where air starts to affect sensitive groups. When a location crosses it you get a coloured banner, a flashing tab title, an optional chime, and a desktop notification. It re-notifies hourly while a location stays above the line, rather than nagging on every refresh.

Desktop notifications require `https`, which is what GitHub Pages provides. Opened as a local file they stay blocked by the browser; everything else still works.

---

## Reading the numbers

**The headline is the US AQI**, driven mostly by PM2.5 — the fine particles that make haze visible and that reach deep into the lungs.

| US AQI | Category | What it means |
|---|---|---|
| 0–50 | Good | Nothing to think about |
| 51–100 | Moderate | Unusually sensitive people may notice it |
| 101–150 | Unhealthy for sensitive groups | Asthma, heart conditions, children and older adults should ease off outdoor exertion |
| 151–200 | Unhealthy | Everyone should cut outdoor time; close up indoors |
| 201–300 | Very Unhealthy | Stay in |
| 301+ | Hazardous | Emergency conditions |

**Malaysia's own API usually reads lower than the US AQI for the same air.** Neither number is wrong. Malaysia's "Moderate" band runs to 75.5 µg/m³ of PM2.5 where the US band stops at 35.4, so a day the DOE calls Moderate can be Unhealthy on the US scale. Both are shown here so the gap is visible rather than surprising.

**Watch the wind, not just the number.** Malaysia's worst haze usually blows in rather than building up locally. The bearing that points back at Sumatra differs by region — roughly SW–WNW from the Klang Valley, S–WSW from Penang — so each location is checked against its own sector.

---

## Data sources, and their limits

Current readings are **measurements**, not simulation. They come from Malaysia's Department of Environment monitoring stations — the same instruments [APIMS](https://apims.doe.gov.my/public_v2/api_table.html) publishes from — delivered through the [World Air Quality Index](https://aqicn.org/api/) project's API.

The honest caveat is distance, not accuracy. A station is never in your exact spot: Seremban's sits under 3 km from the town centre, while the nearest station to Semenyih is 15 km away in Nilai. Air quality varies over that distance. Every card names its station and the gap, so you can judge how much to trust it.

The **five-day outlook is a forecast** and the only modelled figure here.

Wind, visibility and humidity come from [Open-Meteo](https://open-meteo.com/), where a physics model is the right tool and ground sensors can't help.

An earlier version of this project ran entirely on a forecast model. During an active episode it read roughly half what the DOE stations were measuring — which is why it doesn't any more. If you fork this, resist the temptation to put a modelled number where people expect a measured one.

Nothing here is medical advice. If you have a respiratory or cardiac condition, follow your doctor's guidance and the official advisories, not a webpage.

---

## Running your own copy

**Locally** — download `index.html` and open it in a browser. That's the whole installation.

**Note on the API token.** The WAQI token sits in plain sight in `index.html`. That's how the API is designed to be used — it's rate-limited per token and carries no account access — but if you fork this, [request your own free token](https://aqicn.org/data-platform/token/) rather than reusing the one in here.

**On GitHub Pages** — fork this repository, then go to **Settings → Pages**, set the source to **Deploy from a branch**, pick `main` and the root folder, and save. A minute later your copy is live at `https://YOUR-USERNAME.github.io/REPO-NAME/`.

For link previews in WhatsApp, Slack or LinkedIn to render, change the `og:image` line in `index.html` from the relative path to your full URL:

```html
<meta property="og:image" content="https://YOUR-USERNAME.github.io/REPO-NAME/og.png">
```

## Tracking different places

Edit the `LOCATIONS` array near the top of the `<script>` block in `index.html`:

```js
{ id:"ipoh", name:"Ipoh", short:"Ipoh",
  where:"Perak · city centre", area:"Klang Valley & Negeri Sembilan",
  lat:4.5975, lon:101.0901 }
```

Each location also needs a `uid` — the WAQI station id it reads from. Find one by searching [aqicn.org](https://aqicn.org/) for a city and taking the number from its station URL, or by querying `https://api.waqi.info/map/bounds/?latlng=<lat1>,<lng1>,<lat2>,<lng2>&token=<your token>`, which lists every station in a box with its `uid`.

`area` groups cards under a heading and selects which wind sector counts as the Sumatra track — add a new entry to `SUMATRA_ARC` if you add a region. Up to eight locations render with distinct, colourblind-checked series colours.

## Technical notes

- One file, roughly 1,200 lines, vanilla JavaScript — no framework, no bundler, no `node_modules`
- Charts are hand-rolled inline SVG with hover crosshairs and tooltips
- Light and dark themes are both hand-picked rather than auto-inverted, and the series palette is validated for colourblind separation
- Fetches carry no `AbortSignal`, so the page also works inside sandboxed viewers that proxy `fetch` through `postMessage`
- The station feed publishes AQI sub-indices rather than concentrations, so PM2.5 and PM10 in µg/m³ are recovered by running the EPA breakpoints in reverse
- Settings live in the visitor's own `localStorage`, wrapped so that blocked storage degrades instead of breaking
- No analytics, no cookies, no third-party scripts. The only outbound requests are to WAQI and Open-Meteo.

## Credits

Air quality measurements by the Malaysian Department of Environment, served through the [World Air Quality Index](https://waqi.info/) project. Weather data by [Open-Meteo](https://open-meteo.com/), licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## License

MIT — see [LICENSE](LICENSE).
