# Hazewatch - Malaysia
**Live air quality for the Klang Valley and Penang, refreshed every five minutes.**

During haze season the question is rarely "is the air bad?" — it's *how* bad, *where*, and *is it heading my way*. Haze Watch answers all three on one screen: current readings for six locations, the last 24 hours and the next 24 hours side by side, and the wind direction that decides whether smoke is about to arrive.

It's a single HTML file. No build step, no dependencies, no server, no tracking, no API key.

![Haze Watch showing six Malaysian locations with US AQI readings, trend sparklines and wind direction](screenshot-light.png)

*Example view. Readings shown are illustrative. There's a [dark theme](screenshot-dark.png) too, and it follows your system setting by default.*

---

## What it tracks

| Klang Valley & Negeri Sembilan | Pulau Pinang |
|---|---|
| Kuala Lumpur | Georgetown |
| Semenyih | Bayan Lepas |
| Seremban | Bukit Mertajam |

For each location:

- **US AQI** as the headline number, with its category and a 3-hour trend
- **PM2.5 and PM10** concentrations in µg/m³
- **Malaysian API** — an estimate, computed from the trailing 24-hour averages
- **Wind direction and speed**, with a **Sumatra track** flag when the wind is arriving from the bearing sector that carries transboundary smoke
- **A 48-hour sparkline** — solid for observed, dashed for forecast, hover for any hour
- **Modelled visibility**, which often drops before the numbers climb

A **Compare** tab puts all six on one chart with your alert threshold drawn across it, plus the same data as a sortable table.

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

## Data source, and its limits

Air quality and weather come from [Open-Meteo](https://open-meteo.com/), which serves the **CAMS** atmospheric model — satellite observation blended with simulation on a grid of roughly 11 km. It is free, needs no API key, and is well suited to seeing an episode coming and to comparing one town against another.

It is **not** a sensor on any particular street, and it should not be treated as one. For the official figure that health advisories and school closures are based on, see the Department of Environment's [APIMS station readings](https://apims.doe.gov.my/public_v2/api_table.html).

The Malaysian API figure shown here is this project's own estimate. It applies the DOE's published PM2.5 and PM10 [sub-index formulas](https://www.doe.gov.my/wp-content/uploads/2021/09/API_Calculation.pdf) to the trailing 24-hour modelled averages and takes the higher of the two. It is not an official DOE number and will not match APIMS exactly.

Nothing here is medical advice. If you have a respiratory or cardiac condition, follow your doctor's guidance and the official advisories, not a webpage.

---

## Running your own copy

**Locally** — download `index.html` and open it in a browser. That's the whole installation.

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

`area` groups cards under a heading and selects which wind sector counts as the Sumatra track — add a new entry to `SUMATRA_ARC` if you add a region. Up to eight locations render with distinct, colourblind-checked series colours.

## Technical notes

- One file, roughly 1,300 lines, vanilla JavaScript — no framework, no bundler, no `node_modules`
- Charts are hand-rolled inline SVG with hover crosshairs and tooltips
- Light and dark themes are both hand-picked rather than auto-inverted, and the series palette is validated for colourblind separation
- Fetches carry no `AbortSignal`, so the page also works inside sandboxed viewers that proxy `fetch` through `postMessage`
- Settings live in the visitor's own `localStorage`, wrapped so that blocked storage degrades instead of breaking
- No analytics, no cookies, no third-party scripts. The only outbound requests are to Open-Meteo.

## Credits

Data by [Open-Meteo](https://open-meteo.com/), licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Weather model data from the Copernicus Atmosphere Monitoring Service (CAMS).

## License

MIT — see [LICENSE](LICENSE).
