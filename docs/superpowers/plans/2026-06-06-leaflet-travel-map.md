# Leaflet Travel Map Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the hand-drawn travel SVG with a Leaflet choropleth-style China travel map using real province GeoJSON, highlighted visited provinces, and clickable city markers.

**Architecture:** Keep the portfolio as a static HTML/CSS/JS site. Load Leaflet CSS/JS from CDN, load local `assets/china-provinces.geojson`, render province polygons without online map tiles, and keep existing travel card/buttons as the detail surface.

**Tech Stack:** Vanilla HTML, CSS, JavaScript, Leaflet CDN, local GeoJSON asset, Python static server.

---

### Task 1: Add Leaflet Shell And Data Asset

**Files:**
- Modify: `index.html`
- Create: `assets/china-provinces.geojson`

- [ ] Add Leaflet CDN CSS in `<head>`:

```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIINfQWlOKLxjQbQ5m7XlGHoEJXYxU5E6d0=" crossorigin="">
```

- [ ] Replace current inline SVG map markup with a Leaflet map container:

```html
<div class="leaflet-travel-map" id="leaflet-travel-map" aria-label="中国旅行策划互动地图"></div>
```

- [ ] Keep `#travel-city-list`, `.map-source-note`, and the travel card markup unchanged.

- [ ] Change source note copy to:

```text
地图边界数据用于旅行经历可视化展示；页面不调用在线地图 API。
```

- [ ] Add Leaflet CDN script before local `script.js`:

```html
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
```

- [ ] Download China province GeoJSON to `assets/china-provinces.geojson` from a stable public GeoJSON source. The file must be valid GeoJSON `FeatureCollection`, include province names in feature properties, and be served locally.

### Task 2: Replace SVG Rendering With Leaflet

**Files:**
- Modify: `script.js`

- [ ] Keep `travelCities`, `toneColors`, `renderTravelCard`, `renderTravelCityList`, and `selectTravelCity`.

- [ ] Add `province` to each city record using Chinese province-level names:

```js
{ id: 'wuhan', zh: '武汉', en: 'Wuhan', province: '湖北省', lat: 30.5928, lng: 114.3055, tone: 'river' }
```

- [ ] Add province grouping helpers:

```js
function getVisitedProvinceNames() {
  return new Set(travelCities.map(city => city.province));
}

function getProvinceCities(provinceName) {
  return travelCities.filter(city => city.province === provinceName);
}
```

- [ ] Initialize Leaflet once after language setup:

```js
function initTravelMap() {
  travelMap = L.map('leaflet-travel-map', {
    zoomControl: false,
    attributionControl: false,
    dragging: true,
    scrollWheelZoom: false,
    doubleClickZoom: false
  });
}
```

- [ ] Fetch local GeoJSON:

```js
fetch('assets/china-provinces.geojson')
  .then(response => response.json())
  .then(renderProvinceLayer)
  .catch(showMapFallback);
```

- [ ] Render province polygons with visited provinces highlighted, unvisited provinces muted, hover stroke, and click behavior. Clicking a visited province selects the first city in that province; clicking an unvisited province only applies hover/visual feedback and does not change card.

- [ ] Render city `circleMarker`s for all 33 cities. Clicking a marker selects that city, highlights the marker, and updates the card and bottom city button.

- [ ] Keep map labels in Chinese for readability. Keep card/button language switching as currently implemented.

### Task 3: Update Styles

**Files:**
- Modify: `style.css`

- [ ] Remove obsolete inline SVG city block styles.

- [ ] Add Leaflet container styling:

```css
.leaflet-travel-map {
  width: 100%;
  min-height: 620px;
  border-radius: 18px;
  background: #eef2ff;
  overflow: hidden;
  border: 1px solid rgba(79, 70, 229, 0.12);
}
```

- [ ] Add marker and tooltip styles:

```css
.travel-city-marker {
  border: 2px solid #fff;
  box-shadow: 0 6px 14px rgba(15, 23, 42, 0.2);
}

.travel-city-tooltip {
  border: 0;
  border-radius: 999px;
  padding: 4px 8px;
  color: #172554;
  font-weight: 800;
  box-shadow: 0 6px 16px rgba(15, 23, 42, 0.12);
}
```

- [ ] Ensure mobile width has no horizontal overflow and map height reduces to about `430px`.

### Task 4: Verify Interactions

**Files:**
- Check only.

- [ ] Run:

```bash
node --check script.js
```

Expected: no output and exit code `0`.

- [ ] Run:

```bash
curl -I http://localhost:8000/index.html
curl -I http://localhost:8000/style.css
curl -I http://localhost:8000/script.js
curl -I http://localhost:8000/assets/china-provinces.geojson
```

Expected: each returns `200 OK`.

- [ ] Browser verify:

```text
http://localhost:8000/#travel
```

Expected:
- Leaflet map renders province boundaries.
- Visited provinces are highlighted.
- 33 city markers are visible.
- Clicking Wuhan, Shanghai, Macau, Ningbo, Chengdu updates the card and active city button.
- Clicking a highlighted province selects one city in that province.
- Language toggle keeps map usable and updates card/buttons.
- 390px mobile viewport has no horizontal overflow.
