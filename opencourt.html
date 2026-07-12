<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>OpenCourt — Calgary outdoor hoops</title>
<!--
  ============================================================================
  OpenCourt v1 — Calgary outdoor basketball court finder + condition reports
  ============================================================================
  DATA ARCHITECTURE
  - Primary layer: City of Calgary "Parks Sport Surfaces" open dataset
    (Socrata id pb2x-6irv), queried live at runtime via the SODA GeoJSON
    endpoint with a full-text filter for "basketball". Polygons are reduced
    to centroids for markers. Rows are re-checked client-side so only
    basketball surfaces render, regardless of the dataset's field names.
  - Fallback layer: SEED_COURTS below — a 12-court snapshot with verified
    coordinates. Used when the live endpoint is unreachable (e.g. inside a
    sandboxed preview) and merged with live data otherwise (seed courts more
    than ~120 m from any official surface are kept, since a few well-known
    courts such as CMLC sites are not Calgary Parks assets).
  - Refresh path: everything lives in CONFIG. To point at a different or
    updated dataset, change CONFIG.datasetId / searchTerm. Nothing else
    needs to move.

  CROWDSOURCING LAYER
  - Anonymous, no accounts. One storage key per court: reports:{courtId},
    newest first, capped at 25. A single "activity" index key powers marker
    freshness without N reads.
  - Storage adapter prefers window.storage (shared, available in the Claude
    artifact preview), falls back to localStorage when self-hosted, then
    in-memory. For production swap the adapter body for Supabase — the
    get/set interface is the only contract (see README).
  - Reports age out: crowd levels fade after 2 h and expire at 6 h;
    rim/surface reports fade after 14 d and expire at 45 d.

  SATELLITE DETECTION (v2, not built — documented path)
  - Court objects carry source: 'city' | 'seed' | 'community' | 'detected'.
    A 'detected' court renders with a dashed marker ring and an
    "Unconfirmed" chip until a user verifies it. An offline pipeline
    (e.g. a pre-trained aerial sports-court detector run over Calgary
    orthophotos) only needs to emit features in the same normalized shape
    {id, name, lat, lng, courtType, source:'detected', confirmed:false}
    and push them into state.courts. No rebuild required.
  ============================================================================
-->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.css">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Archivo:wght@400;500;600;700&family=Archivo+Black&display=swap" rel="stylesheet">
<style>
  :root{
    --asphalt:#1b1d21;        /* ink */
    --ink-soft:#5b6066;
    --concrete:#e9ebec;       /* app background behind tiles */
    --card:#ffffff;
    --hairline:#d5d8da;
    --ball:#e8622c;           /* game-ball orange: primary action */
    --ball-deep:#c94f1f;
    --key-blue:#2b5cad;       /* painted key: secondary */
    --good:#1f7a57;           /* fresh court paint green */
    --warn:#c98a06;
    --bad:#c2382e;
    --radius:14px;
    --shadow:0 10px 30px rgba(27,29,33,.18);
    --font-body:'Archivo',system-ui,-apple-system,'Segoe UI',sans-serif;
    --font-display:'Archivo Black','Arial Black',var(--font-body);
    --font-mono:ui-monospace,'SF Mono','Cascadia Mono',Menlo,monospace;
  }
  *{box-sizing:border-box}
  html,body{height:100%;margin:0}
  body{
    font-family:var(--font-body);
    color:var(--asphalt);
    background:var(--concrete);
    overflow:hidden;
    -webkit-tap-highlight-color:transparent;
  }

  /* ---------- top bar ---------- */
  .topbar{
    position:fixed;inset:0 0 auto 0;z-index:1100;
    display:flex;align-items:center;justify-content:space-between;
    padding:calc(10px + env(safe-area-inset-top)) 14px 10px;
    background:linear-gradient(rgba(27,29,33,.92),rgba(27,29,33,.85));
    backdrop-filter:blur(6px);
    color:#fff;
  }
  .brand{
    display:flex;align-items:center;gap:9px;
    font-family:var(--font-display);
    font-size:17px;letter-spacing:.12em;
  }
  .brand-ring{
    width:15px;height:15px;border-radius:50%;
    border:3.5px solid var(--ball);
    display:inline-block;flex:none;
  }
  .brand small{
    font-family:var(--font-body);font-weight:500;font-size:11px;
    letter-spacing:.08em;color:#b7bbbf;margin-left:2px;
  }
  .source-badge{
    font-family:var(--font-mono);font-size:10.5px;letter-spacing:.08em;
    padding:5px 9px;border-radius:999px;
    border:1px solid rgba(255,255,255,.35);color:#e6e8ea;
    text-transform:uppercase;white-space:nowrap;
  }
  .source-badge.city{border-color:var(--good);color:#7fd4b2}
  .source-badge.seed{border-color:var(--warn);color:#f0c164}

  /* ---------- map ---------- */
  #map{position:fixed;inset:0;z-index:1;background:var(--concrete)}
  .leaflet-container{font-family:var(--font-body)}
  .leaflet-top{top:calc(58px + env(safe-area-inset-top))}
  .leaflet-control-attribution{font-size:9px}

  /* markers: little backboards */
  .mk{position:relative;width:30px;height:34px;cursor:pointer;filter:drop-shadow(0 2px 3px rgba(27,29,33,.35))}
  .mk-board{
    position:absolute;top:0;left:3px;width:24px;height:18px;
    background:#fff;border:2.5px solid var(--asphalt);border-radius:4px;
  }
  .mk-ring{
    position:absolute;top:10px;left:9px;width:12px;height:12px;
    border:3px solid var(--ball);border-radius:50%;background:transparent;
  }
  .mk-post{
    position:absolute;top:18px;left:13.5px;width:3px;height:14px;
    background:var(--asphalt);border-radius:2px;
  }
  .mk-live::after{
    content:"";position:absolute;top:-4px;right:-2px;width:10px;height:10px;
    border-radius:50%;background:var(--good);border:2px solid #fff;
  }
  .mk-unconfirmed .mk-board{border-style:dashed}
  .mk.sel .mk-board{border-color:var(--ball-deep);box-shadow:0 0 0 3px rgba(232,98,44,.35)}

  /* ---------- notice banner ---------- */
  .banner{
    position:fixed;left:12px;right:12px;z-index:1090;
    top:calc(62px + env(safe-area-inset-top));
    background:var(--asphalt);color:#e6e8ea;
    border-left:4px solid var(--warn);
    padding:10px 36px 10px 12px;border-radius:10px;
    font-size:12.5px;line-height:1.45;box-shadow:var(--shadow);
    max-width:520px;margin:0 auto;
  }
  .banner button{
    position:absolute;top:6px;right:6px;border:0;background:transparent;
    color:#b7bbbf;font-size:16px;cursor:pointer;padding:4px 8px;
  }

  /* ---------- bottom sheet ---------- */
  .sheet{
    position:fixed;left:0;right:0;bottom:0;z-index:1200;
    margin:0 auto;max-width:520px;
    background:var(--card);
    border-radius:18px 18px 0 0;
    box-shadow:0 -12px 40px rgba(27,29,33,.28);
    transform:translateY(calc(100% - 52px));
    transition:transform .28s cubic-bezier(.32,.72,.28,1);
    padding-bottom:env(safe-area-inset-bottom);
    max-height:78vh;display:flex;flex-direction:column;
  }
  @media (prefers-reduced-motion:reduce){ .sheet{transition:none} }
  .sheet.open{transform:translateY(0)}
  .sheet-peek{
    flex:none;display:flex;align-items:center;justify-content:center;gap:8px;
    height:52px;cursor:default;font-size:13px;color:var(--ink-soft);
    border-bottom:1px solid transparent;position:relative;
  }
  .sheet.open .sheet-peek{border-bottom-color:var(--hairline)}
  .sheet-peek::before{
    content:"";position:absolute;top:7px;left:50%;transform:translateX(-50%);
    width:38px;height:4px;border-radius:2px;background:var(--hairline);
  }
  .sheet-peek strong{color:var(--asphalt);font-weight:600}
  .sheet-close{
    position:absolute;right:8px;top:10px;width:34px;height:34px;
    border:0;border-radius:50%;background:var(--concrete);
    font-size:15px;line-height:1;cursor:pointer;color:var(--ink-soft);
    display:none;
  }
  .sheet.open .sheet-close{display:block}
  .sheet-body{overflow-y:auto;padding:14px 16px 20px;-webkit-overflow-scrolling:touch}

  .court-name{
    font-family:var(--font-display);
    font-size:20px;line-height:1.15;letter-spacing:.01em;
    margin:2px 0 6px;text-transform:uppercase;
  }
  .court-meta{display:flex;flex-wrap:wrap;gap:6px;margin-bottom:14px}
  .tag{
    font-size:11px;font-weight:600;letter-spacing:.06em;text-transform:uppercase;
    padding:4px 8px;border-radius:6px;background:var(--concrete);color:var(--ink-soft);
  }
  .tag.type{background:rgba(43,92,173,.12);color:var(--key-blue)}
  .tag.src-city{background:rgba(31,122,87,.12);color:var(--good)}
  .tag.src-seed{background:rgba(201,138,6,.14);color:#8a5f04}
  .tag.src-detected{background:rgba(194,56,46,.1);color:var(--bad)}

  /* status summary */
  .status-grid{display:grid;grid-template-columns:1fr 1fr 1fr;gap:8px;margin-bottom:16px}
  .status-cell{
    border:1px solid var(--hairline);border-radius:var(--radius);
    padding:10px 10px 9px;min-height:74px;
    display:flex;flex-direction:column;gap:4px;
  }
  .status-cell .lab{font-size:10.5px;font-weight:600;letter-spacing:.09em;text-transform:uppercase;color:var(--ink-soft)}
  .status-cell .val{font-size:14px;font-weight:600;line-height:1.2}
  .status-cell .val.none{color:var(--ink-soft);font-weight:500;font-size:13px}
  .status-cell .val.v-good{color:var(--good)}
  .status-cell .val.v-warn{color:var(--warn)}
  .status-cell .val.v-bad{color:var(--bad)}
  .freshness{display:flex;align-items:center;gap:5px;margin-top:auto}
  .freshness .clock{
    --pct:100%;
    width:14px;height:14px;border-radius:50%;flex:none;
    background:conic-gradient(var(--ball) var(--pct), var(--concrete) 0);
    -webkit-mask:radial-gradient(circle at center, transparent 3.2px, #000 3.6px);
            mask:radial-gradient(circle at center, transparent 3.2px, #000 3.6px);
  }
  .freshness time{font-family:var(--font-mono);font-size:10.5px;color:var(--ink-soft)}
  .freshness.stale time{color:var(--warn)}

  /* report form */
  .report-btn{
    width:100%;border:0;border-radius:var(--radius);
    background:var(--ball);color:#fff;cursor:pointer;
    font-family:var(--font-body);font-weight:700;font-size:15px;
    letter-spacing:.02em;padding:14px;margin-bottom:6px;
  }
  .report-btn:active{background:var(--ball-deep)}
  .report-form{border-top:1px dashed var(--hairline);margin-top:12px;padding-top:14px}
  .rf-group{margin-bottom:14px}
  .rf-label{font-size:11px;font-weight:600;letter-spacing:.09em;text-transform:uppercase;color:var(--ink-soft);margin-bottom:7px}
  .seg{display:flex;flex-wrap:wrap;gap:6px}
  .seg button{
    flex:1 1 auto;min-height:42px;padding:8px 10px;
    border:1.5px solid var(--hairline);border-radius:10px;background:#fff;
    font-family:var(--font-body);font-size:13px;font-weight:500;cursor:pointer;color:var(--asphalt);
  }
  .seg button[aria-pressed="true"]{
    border-color:var(--ball);background:rgba(232,98,44,.09);
    color:var(--ball-deep);font-weight:700;
  }
  .rf-note{
    width:100%;border:1.5px solid var(--hairline);border-radius:10px;
    padding:10px 12px;font-family:var(--font-body);font-size:14px;
    resize:none;height:64px;
  }
  .rf-note:focus{outline:2px solid var(--key-blue);outline-offset:1px;border-color:transparent}
  .rf-actions{display:flex;gap:8px;align-items:center;margin-top:6px}
  .rf-post{
    flex:1;border:0;border-radius:var(--radius);background:var(--asphalt);color:#fff;
    font-weight:700;font-size:15px;padding:13px;cursor:pointer;font-family:var(--font-body);
  }
  .rf-post:disabled{opacity:.45;cursor:default}
  .rf-cancel{border:0;background:transparent;color:var(--ink-soft);font-size:14px;cursor:pointer;padding:12px 10px;font-family:var(--font-body)}
  .rf-hint{font-size:11px;color:var(--ink-soft);margin-top:8px}

  /* history */
  .history{margin-top:18px}
  .history h3{font-size:11px;font-weight:600;letter-spacing:.09em;text-transform:uppercase;color:var(--ink-soft);margin:0 0 8px}
  .hist-item{
    border-left:3px solid var(--concrete);padding:2px 0 10px 10px;margin-left:2px;
    font-size:13px;line-height:1.5;
  }
  .hist-item time{font-family:var(--font-mono);font-size:10.5px;color:var(--ink-soft);display:block}
  .hist-item .note{color:var(--asphalt)}
  .hist-empty{font-size:13px;color:var(--ink-soft);font-style:italic}
  .city-props{margin-top:16px;font-size:12px;color:var(--ink-soft)}
  .city-props summary{cursor:pointer;font-weight:600}
  .city-props dl{margin:8px 0 0;display:grid;grid-template-columns:auto 1fr;gap:2px 10px}
  .city-props dt{font-family:var(--font-mono);font-size:10.5px}
  .city-props dd{margin:0}

  /* toast */
  .toast{
    position:fixed;left:50%;bottom:calc(84px + env(safe-area-inset-bottom));
    transform:translate(-50%,12px);z-index:1400;
    background:var(--asphalt);color:#fff;font-size:13.5px;font-weight:600;
    padding:11px 18px;border-radius:999px;box-shadow:var(--shadow);
    opacity:0;pointer-events:none;transition:opacity .2s,transform .2s;
  }
  .toast.show{opacity:1;transform:translate(-50%,0)}

  button:focus-visible,summary:focus-visible{outline:2.5px solid var(--key-blue);outline-offset:2px}
</style>
</head>
<body>

<header class="topbar">
  <div class="brand"><span class="brand-ring" aria-hidden="true"></span>OPENCOURT<small>YYC</small></div>
  <div class="source-badge" id="sourceBadge">loading&#8230;</div>
</header>

<div id="map" role="application" aria-label="Map of Calgary outdoor basketball courts"></div>

<div class="banner" id="banner" hidden>
  <span id="bannerText"></span>
  <button type="button" aria-label="Dismiss notice" onclick="document.getElementById('banner').hidden=true">&#10005;</button>
</div>

<section class="sheet" id="sheet" aria-label="Court details">
  <div class="sheet-peek" id="sheetPeek"><span id="peekText">Loading courts&#8230;</span>
    <button type="button" class="sheet-close" id="sheetClose" aria-label="Close court details">&#10005;</button>
  </div>
  <div class="sheet-body" id="sheetBody"></div>
</section>

<div class="toast" id="toast" role="status"></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<script>
"use strict";

/* ========================= CONFIG (the refresh path) ===================== */
const CONFIG = {
  sodaBase: "https://data.calgary.ca/resource",
  datasetId: "pb2x-6irv",          // City of Calgary — Parks Sport Surfaces
  searchTerm: "basketball",        // SODA $q full-text filter
  maxRows: 5000,
  fetchTimeoutMs: 12000,
  center: [51.0447, -114.0719],    // Calgary
  zoom: 11,
  tiles: {
    url: "https://basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png",
    fallbackUrl: "https://tile.openstreetmap.org/{z}/{x}/{y}.png",
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> &copy; <a href="https://carto.com/attributions">CARTO</a> &middot; Court data: City of Calgary Open Data'
  },
  dedupeMeters: 120,
  windows: {                        // report aging (ms)
    crowd: { fresh: 2*3600e3,  max: 6*3600e3  },
    rim:   { fresh: 14*864e5,  max: 45*864e5  },
    surf:  { fresh: 14*864e5,  max: 45*864e5  }
  }
};

/* ============ Seed snapshot: verified coordinates, fallback only ========= */
const SEED_COURTS = [
  { name:"Bridgeland Sport Court", lat:51.0514876, lng:-114.0416243, courtType:"3 half courts", note:"Circular 3x3 layout beside Murdoch Park" },
  { name:"The Bounce (East Village)", lat:51.044998, lng:-114.053608, courtType:"Full court", note:"Glass backboards, next to Studio Bell" },
  { name:"High Park Rooftop Courts", lat:51.0436563, lng:-114.0696669, courtType:"3 half courts", note:"Level 6 of City Centre Parkade, Beltline" },
  { name:"Pixel Park", lat:51.0414543, lng:-114.0575776, courtType:"Half court", note:"Beside the skate plaza, Culture + Entertainment District" },
  { name:"Century Gardens Courts", lat:51.0461716, lng:-114.0802878, courtType:"Half courts", note:"8 St & 8 Ave SW, opened 2023" },
  { name:"Gopher Park", lat:51.0401502, lng:-114.0365963, courtType:"Half court", note:"11 Ave & 11 St SE" },
  { name:"St. Andrews Heights Court", lat:51.0642177, lng:-114.1211213, courtType:"Full court", note:"With tennis practice wall" },
  { name:"John Laurie Park Courts", lat:51.1146093, lng:-114.1513992, courtType:"Full court", note:"Edgemont NW" },
  { name:"Royal Birkdale Court", lat:51.1474902, lng:-114.2239570, courtType:"Full court", note:"Royal Oak NW, hilltop" },
  { name:"Spruce Cliff Courts", lat:51.0476688, lng:-114.1355819, courtType:"Full court", note:"Spruce Dr & Cedar Cr SW" },
  { name:"Applewood Park Courts", lat:51.0468021, lng:-113.9287739, courtType:"2 courts", note:"Rink doubles as court space in summer" },
  { name:"New Brighton Athletic Park", lat:50.9280266, lng:-113.9507371, courtType:"Full court", note:"Part of the athletic park complex" }
];

/* ============================== utilities ================================ */
function courtId(lat, lng){ return "coc-" + lat.toFixed(5) + "x" + lng.toFixed(5); }

function centroidOf(geom){
  if (!geom || !geom.coordinates) return null;
  if (geom.type === "Point") return [geom.coordinates[1], geom.coordinates[0]];
  const pts = [];
  (function collect(c){
    if (typeof c[0] === "number") { pts.push(c); return; }
    for (const inner of c) collect(inner);
  })(geom.coordinates);
  if (!pts.length) return null;
  let sx = 0, sy = 0;
  for (const p of pts) { sx += p[0]; sy += p[1]; }
  return [sy / pts.length, sx / pts.length]; // [lat, lng]
}

function metersBetween(a, b){
  const dLat = (a[0]-b[0]) * 111320;
  const dLng = (a[1]-b[1]) * 111320 * Math.cos(a[0] * Math.PI/180);
  return Math.hypot(dLat, dLng);
}

function timeAgo(t){
  const s = Math.max(0, (Date.now() - t) / 1000);
  if (s < 60)      return "just now";
  if (s < 3600)    return Math.round(s/60) + "m ago";
  if (s < 86400)   return Math.round(s/3600) + "h ago";
  return Math.round(s/86400) + "d ago";
}

function firstProp(props, keys){
  for (const k of keys){
    for (const pk of Object.keys(props)){
      if (pk.toLowerCase() === k && props[pk] != null && String(props[pk]).trim() !== "")
        return String(props[pk]).trim();
    }
  }
  return null;
}

function titleCase(s){
  return s.toLowerCase().replace(/\b[a-z]/g, function(m){ return m.toUpperCase(); });
}

/* =========================== storage adapter ============================= */
/* Contract: async get(key) -> value|null, async set(key, value) -> bool.
   Swap this block for a Supabase/Postgres implementation in production. */
const store = (function(){
  const mem = new Map();
  function wsOK(){
    try { return typeof window !== "undefined" && window.storage && typeof window.storage.get === "function"; }
    catch(e){ return false; }
  }
  let lsOK = false;
  try {
    const t = "__oc_probe";
    window.localStorage.setItem(t, "1"); window.localStorage.removeItem(t);
    lsOK = true;
  } catch(e){ /* sandboxed or unavailable — fall through */ }

  async function get(key){
    if (wsOK()){
      try { const r = await window.storage.get(key, true); return r ? JSON.parse(r.value) : null; }
      catch(e){ return null; }               // missing key throws -> treat as empty
    }
    if (lsOK){
      const v = window.localStorage.getItem("oc:" + key);
      return v ? JSON.parse(v) : null;
    }
    return mem.has(key) ? mem.get(key) : null;
  }
  async function set(key, value){
    const s = JSON.stringify(value);
    if (wsOK()){
      try { const r = await window.storage.set(key, s, true); return !!r; }
      catch(e){ return false; }
    }
    if (lsOK){
      try { window.localStorage.setItem("oc:" + key, s); return true; }
      catch(e){ return false; }
    }
    mem.set(key, value); return true;
  }
  return { get: get, set: set, mode: wsOK() ? "shared" : (lsOK ? "device" : "session") };
})();

/* ============================ report model ============================== */
const RIM_LABELS   = ["Rims good", "Bent / no net", "Rim missing"];
const SURF_LABELS  = ["Smooth", "Some cracks", "Rough"];
const CROWD_LABELS = ["Empty", "A few shooting", "Games running", "Packed"];
const SEVERITY = { rim: ["v-good","v-warn","v-bad"], surf: ["v-good","v-warn","v-bad"], crowd: ["v-good","v-good","v-warn","v-bad"] };

function latestFor(reports, field){
  const w = CONFIG.windows[field];
  for (const r of reports){
    if (r[field] == null) continue;
    if (Date.now() - r.t <= w.max) return r;
    return null; // newest mention already expired; older ones are older still
  }
  return null;
}

async function getReports(id){ return (await store.get("reports:" + id)) || []; }

async function postReport(id, report){
  const key = "reports:" + id;
  const list = (await store.get(key)) || [];
  list.unshift(report);
  const ok = await store.set(key, list.slice(0, 25));
  // lightweight activity index so markers can show freshness with one read
  const idx = (await store.get("activity")) || {};
  idx[id] = { t: report.t };
  await store.set("activity", idx);
  return ok;
}

/* ============================ data loading =============================== */
function normalizeCityFeature(f){
  const props = (f && f.properties) || {};
  if (!/basket/i.test(JSON.stringify(props))) return null;   // belt & braces on $q
  const c = centroidOf(f.geometry);
  if (!c) return null;
  const rawName = firstProp(props, ["site_name","sitename","park_site_name","location","label","name","description","address"]);
  const blob = JSON.stringify(props);
  let courtType = "Basketball court";
  if (/half/i.test(blob)) courtType = "Half court";
  else if (/full/i.test(blob)) courtType = "Full court";
  const shown = {};
  let n = 0;
  for (const k of Object.keys(props)){
    const v = props[k];
    if (v == null || typeof v === "object" || String(v).length > 80) continue;
    shown[k] = String(v);
    if (++n >= 6) break;
  }
  return {
    id: courtId(c[0], c[1]),
    name: rawName ? titleCase(rawName) : "Basketball Court",
    lat: c[0], lng: c[1],
    courtType: courtType,
    source: "city",
    confirmed: true,
    cityProps: shown
  };
}

function normalizeSeed(s){
  return {
    id: courtId(s.lat, s.lng),
    name: s.name, lat: s.lat, lng: s.lng,
    courtType: s.courtType, source: "seed", confirmed: true,
    seedNote: s.note, cityProps: null
  };
}

function mergeWithSeed(cityCourts){
  const merged = cityCourts.slice();
  for (const s of SEED_COURTS){
    const sc = normalizeSeed(s);
    const dup = cityCourts.some(function(c){ return metersBetween([c.lat,c.lng],[sc.lat,sc.lng]) < CONFIG.dedupeMeters; });
    if (!dup) merged.push(sc);
  }
  return merged;
}

function fetchWithTimeout(url, ms){
  const ctl = new AbortController();
  const timer = setTimeout(function(){ ctl.abort(); }, ms);
  return fetch(url, { signal: ctl.signal }).finally(function(){ clearTimeout(timer); });
}

async function loadCourts(){
  const url = CONFIG.sodaBase + "/" + CONFIG.datasetId + ".geojson"
            + "?$q=" + encodeURIComponent(CONFIG.searchTerm)
            + "&$limit=" + CONFIG.maxRows;
  try {
    const res = await fetchWithTimeout(url, CONFIG.fetchTimeoutMs);
    if (!res.ok) throw new Error("HTTP " + res.status);
    const gj = await res.json();
    const feats = (gj.features || []).map(normalizeCityFeature).filter(Boolean);
    if (!feats.length) throw new Error("no basketball features in response");
    return { courts: mergeWithSeed(feats), source: "city" };
  } catch (err){
    return { courts: SEED_COURTS.map(normalizeSeed), source: "seed", error: String(err && err.message || err) };
  }
}

/* =============================== app state =============================== */
const state = { courts: [], source: null, map: null, markers: new Map(), activity: {}, selectedId: null, formOpen: false };

/* ================================ map ==================================== */
function markerIcon(court, selected){
  const act = state.activity[court.id];
  const live = act && (Date.now() - act.t) < CONFIG.windows.crowd.max;
  const cls = "mk" + (live ? " mk-live" : "") + (court.confirmed === false ? " mk-unconfirmed" : "") + (selected ? " sel" : "");
  return L.divIcon({
    className: "",
    html: '<div class="' + cls + '"><span class="mk-board"></span><span class="mk-ring"></span><span class="mk-post"></span></div>',
    iconSize: [30, 34],
    iconAnchor: [15, 32]
  });
}

function renderMarkers(){
  for (const m of state.markers.values()) m.remove();
  state.markers.clear();
  for (const court of state.courts){
    const m = L.marker([court.lat, court.lng], {
      icon: markerIcon(court, court.id === state.selectedId),
      keyboard: true,
      title: court.name
    });
    m.on("click", function(){ selectCourt(court.id); });
    m.addTo(state.map);
    state.markers.set(court.id, m);
  }
}

function refreshMarker(id){
  const court = state.courts.find(function(c){ return c.id === id; });
  const m = state.markers.get(id);
  if (court && m) m.setIcon(markerIcon(court, id === state.selectedId));
}

function initMap(){
  state.map = L.map("map", { zoomControl: true, attributionControl: true })
               .setView(CONFIG.center, CONFIG.zoom);
  let fellBack = false;
  const layer = L.tileLayer(CONFIG.tiles.url, { maxZoom: 19, attribution: CONFIG.tiles.attribution });
  layer.on("tileerror", function(){
    if (fellBack) return;
    fellBack = true;
    layer.setUrl(CONFIG.tiles.fallbackUrl);
  });
  layer.addTo(state.map);
}

/* ============================ bottom sheet =============================== */
const el = function(id){ return document.getElementById(id); };

function setPeek(text){
  el("peekText").innerHTML = "";
  el("peekText").appendChild(document.createTextNode(text));
}

function openSheet(){ el("sheet").classList.add("open"); }
function closeSheet(){
  el("sheet").classList.remove("open");
  const prev = state.selectedId;
  state.selectedId = null; state.formOpen = false;
  if (prev) refreshMarker(prev);
}

function node(tag, cls, text){
  const n = document.createElement(tag);
  if (cls) n.className = cls;
  if (text != null) n.textContent = text;
  return n;
}

function freshnessNode(report, field){
  const w = CONFIG.windows[field];
  const age = Date.now() - report.t;
  const remaining = Math.max(0, 1 - age / w.max);
  const wrap = node("div", "freshness" + (age > w.fresh ? " stale" : ""));
  const clock = node("span", "clock");
  clock.style.setProperty("--pct", Math.round(remaining * 100) + "%");
  clock.setAttribute("aria-hidden", "true");
  const t = document.createElement("time");
  t.textContent = timeAgo(report.t);
  wrap.appendChild(clock); wrap.appendChild(t);
  return wrap;
}

function statusCell(label, field, labels, reports){
  const cell = node("div", "status-cell");
  cell.appendChild(node("div", "lab", label));
  const latest = latestFor(reports, field);
  if (latest){
    const sev = SEVERITY[field][latest[field]] || "";
    cell.appendChild(node("div", "val " + sev, labels[latest[field]]));
    cell.appendChild(freshnessNode(latest, field));
  } else {
    cell.appendChild(node("div", "val none", "No recent report"));
  }
  return cell;
}

function segGroup(labels, onPick){
  const wrap = node("div", "seg");
  let picked = null;
  labels.forEach(function(lab, i){
    const b = node("button", null, lab);
    b.type = "button";
    b.setAttribute("aria-pressed", "false");
    b.addEventListener("click", function(){
      picked = (picked === i) ? null : i;
      Array.prototype.forEach.call(wrap.children, function(ch, j){
        ch.setAttribute("aria-pressed", String(picked === j));
      });
      onPick(picked);
    });
    wrap.appendChild(b);
  });
  return wrap;
}

function buildReportForm(court, container, reports){
  const form = node("div", "report-form");
  const draft = { rim: null, surf: null, crowd: null };

  function group(label, field, labels){
    const g = node("div", "rf-group");
    g.appendChild(node("div", "rf-label", label));
    g.appendChild(segGroup(labels, function(v){ draft[field] = v; sync(); }));
    return g;
  }
  form.appendChild(group("Crowd right now", "crowd", CROWD_LABELS));
  form.appendChild(group("Rims", "rim", RIM_LABELS));
  form.appendChild(group("Surface", "surf", SURF_LABELS));

  const noteGroup = node("div", "rf-group");
  noteGroup.appendChild(node("div", "rf-label", "Note (optional)"));
  const note = document.createElement("textarea");
  note.className = "rf-note";
  note.maxLength = 140;
  note.placeholder = "e.g. Nets replaced this week, court dry by noon";
  noteGroup.appendChild(note);
  form.appendChild(noteGroup);

  const actions = node("div", "rf-actions");
  const post = node("button", "rf-post", "Post report");
  post.type = "button"; post.disabled = true;
  const cancel = node("button", "rf-cancel", "Cancel");
  cancel.type = "button";
  actions.appendChild(post); actions.appendChild(cancel);
  form.appendChild(actions);
  form.appendChild(node("div", "rf-hint", "Reports are public and anonymous. Crowd levels expire after a few hours; rim and surface reports after a few weeks."));

  function sync(){
    post.disabled = (draft.rim == null && draft.surf == null && draft.crowd == null && note.value.trim() === "");
  }
  note.addEventListener("input", sync);

  cancel.addEventListener("click", function(){ state.formOpen = false; renderCourt(court, reports); });

  post.addEventListener("click", async function(){
    post.disabled = true; post.textContent = "Posting\u2026";
    const report = { t: Date.now() };
    if (draft.rim   != null) report.rim   = draft.rim;
    if (draft.surf  != null) report.surf  = draft.surf;
    if (draft.crowd != null) report.crowd = draft.crowd;
    const n = note.value.trim();
    if (n) report.note = n.slice(0, 140);
    const ok = await postReport(court.id, report);
    if (ok){
      state.activity[court.id] = { t: report.t };
      state.formOpen = false;
      refreshMarker(court.id);
      toast("Report posted");
      renderCourt(court, await getReports(court.id));
    } else {
      toast("Couldn't save \u2014 try again");
      post.disabled = false; post.textContent = "Post report";
    }
  });

  container.appendChild(form);
}

function renderCourt(court, reports){
  const body = el("sheetBody");
  body.innerHTML = "";

  body.appendChild(node("h2", "court-name", court.name));

  const meta = node("div", "court-meta");
  meta.appendChild(node("span", "tag type", court.courtType));
  if (court.source === "city") meta.appendChild(node("span", "tag src-city", "City of Calgary data"));
  if (court.source === "seed") meta.appendChild(node("span", "tag src-seed", "Snapshot"));
  if (court.confirmed === false) meta.appendChild(node("span", "tag src-detected", "Unconfirmed"));
  body.appendChild(meta);

  const grid = node("div", "status-grid");
  grid.appendChild(statusCell("Crowd", "crowd", CROWD_LABELS, reports));
  grid.appendChild(statusCell("Rims", "rim", RIM_LABELS, reports));
  grid.appendChild(statusCell("Surface", "surf", SURF_LABELS, reports));
  body.appendChild(grid);

  if (state.formOpen){
    buildReportForm(court, body, reports);
  } else {
    const btn = node("button", "report-btn", "Report conditions");
    btn.type = "button";
    btn.addEventListener("click", function(){ state.formOpen = true; renderCourt(court, reports); });
    body.appendChild(btn);
  }

  const hist = node("div", "history");
  hist.appendChild(node("h3", null, "Recent reports"));
  const recent = reports.slice(0, 5);
  if (!recent.length){
    hist.appendChild(node("div", "hist-empty", "No reports yet. First one's yours."));
  } else {
    for (const r of recent){
      const item = node("div", "hist-item");
      const t = document.createElement("time");
      t.textContent = timeAgo(r.t);
      item.appendChild(t);
      const bits = [];
      if (r.crowd != null) bits.push(CROWD_LABELS[r.crowd]);
      if (r.rim   != null) bits.push(RIM_LABELS[r.rim]);
      if (r.surf  != null) bits.push("Surface: " + SURF_LABELS[r.surf].toLowerCase());
      if (bits.length) item.appendChild(node("div", null, bits.join(" \u00b7 ")));
      if (r.note) item.appendChild(node("div", "note", "\u201c" + r.note + "\u201d"));
      hist.appendChild(item);
    }
  }
  body.appendChild(hist);

  if (court.seedNote){
    body.appendChild(node("div", "city-props", court.seedNote));
  }
  if (court.cityProps && Object.keys(court.cityProps).length){
    const det = document.createElement("details");
    det.className = "city-props";
    const sum = document.createElement("summary");
    sum.textContent = "From City records";
    det.appendChild(sum);
    const dl = document.createElement("dl");
    for (const k of Object.keys(court.cityProps)){
      dl.appendChild(node("dt", null, k));
      dl.appendChild(node("dd", null, court.cityProps[k]));
    }
    det.appendChild(dl);
    body.appendChild(det);
  }
}

async function selectCourt(id){
  const prev = state.selectedId;
  state.selectedId = id; state.formOpen = false;
  if (prev) refreshMarker(prev);
  refreshMarker(id);
  const court = state.courts.find(function(c){ return c.id === id; });
  if (!court) return;
  setPeek(court.name);
  state.map.panTo([court.lat, court.lng]);
  renderCourt(court, []);          // instant paint
  openSheet();
  renderCourt(court, await getReports(id));  // then hydrate
}

/* ================================ toast ================================== */
let toastTimer = null;
function toast(msg){
  const t = el("toast");
  t.textContent = msg;
  t.classList.add("show");
  clearTimeout(toastTimer);
  toastTimer = setTimeout(function(){ t.classList.remove("show"); }, 2400);
}

/* ================================= init ================================== */
async function init(){
  initMap();
  const result = await loadCourts();
  state.courts = result.courts;
  state.source = result.source;

  state.activity = (await store.get("activity")) || {};

  const badge = el("sourceBadge");
  if (result.source === "city"){
    badge.textContent = "city data";
    badge.classList.add("city");
  } else {
    badge.textContent = "snapshot";
    badge.classList.add("seed");
    el("bannerText").textContent =
      "Live City of Calgary data isn't reachable from this environment, so you're seeing a bundled snapshot of " +
      state.courts.length + " known courts. Host this file anywhere and the full official dataset loads automatically.";
    el("banner").hidden = false;
  }

  renderMarkers();
  setPeek(state.courts.length + " courts \u00b7 tap one to check or report conditions");

  el("sheetClose").addEventListener("click", closeSheet);
  el("sheetPeek").addEventListener("click", function(e){
    if (e.target === el("sheetClose")) return;
    if (state.selectedId) openSheet();
  });
  state.map.on("click", closeSheet);
}

/* test hook for headless verification — harmless in the browser */
if (typeof window !== "undefined"){
  window.__OC_TEST = { centroidOf: centroidOf, metersBetween: metersBetween, timeAgo: timeAgo,
                       latestFor: latestFor, normalizeCityFeature: normalizeCityFeature,
                       mergeWithSeed: mergeWithSeed, courtId: courtId, CONFIG: CONFIG, SEED_COURTS: SEED_COURTS };
}

if (typeof document !== "undefined" && document.getElementById("map")){
  if (document.readyState === "loading"){
    document.addEventListener("DOMContentLoaded", init);
  } else {
    init();
  }
}
</script>
</body>
</html>
