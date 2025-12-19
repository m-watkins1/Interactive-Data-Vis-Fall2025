---
title: "Lab 4: Clearwater Crisis"
toc: false
---

<!-- Import Data -->
```js
const waterQuality = await FileAttachment("data/water_quality.csv").csv({ typed: true });
const fishSurveys = await FileAttachment("data/fish_surveys.csv").csv({ typed: true });
const stations = await FileAttachment("data/monitoring_stations.csv").csv({ typed: true });
const activities = await FileAttachment("data/suspect_activities.csv").csv({ typed: true });
```

```js
// Calculate stats
const westData = waterQuality.filter(d => d.station_id === "West");
const maxHeavyMetals = d3.max(westData, d => d.heavy_metals_ppb);

// Use Date object methods instead of string methods
const westTrout2023 = fishSurveys.find(d => 
  d.station_id === "West" && 
  d.species === "Trout" && 
  d.date.getFullYear() === 2023 && 
  d.date.getMonth() === 0
);

const westTrout2024 = fishSurveys.find(d => 
  d.station_id === "West" && 
  d.species === "Trout" && 
  d.date.getFullYear() === 2024 && 
  d.date.getMonth() === 9
);

const troutDecline = westTrout2023 && westTrout2024 ? 
  Math.round((1 - westTrout2024.count / westTrout2023.count) * 100) : 0;

const violationCount = westData.filter(d => d.heavy_metals_ppb > 30).length;
const westStation = stations.find(s => s.station_id === "West");
const distanceToChemtech = westStation?.distance_to_chemtech_m || 800;
```

<div class="section-box">
  <h2>Crucial Stats</h2>
  <div class="stats-grid">
    <div class="stat-card">
      <div class="stat-value danger">${maxHeavyMetals.toFixed(0)}</div>
      <div class="stat-label">Peak Heavy Metals (ppb)<br/>West Station</div>
    </div>
    <div class="stat-card">
      <div class="stat-value danger">${troutDecline}%</div>
      <div class="stat-label">Trout Population Decline<br/>West Station (2023-2024)</div>
    </div>
    <div class="stat-card">
      <div class="stat-value">${distanceToChemtech}</div>
      <div class="stat-label">Distance (meters)<br/>ChemTech → West Station</div>
    </div>
    <div class="stat-card">
      <div class="stat-value danger">${violationCount}</div>
      <div class="stat-label">Regulatory Violations<br/>At West Station</div>
    </div>
  </div>
</div>

---

## Heavy Metal Contamination | *Clearwater's Smoking Gun*
<div class="chart-title">Weekly Heavy Metal Concentrations Across All Stations (2023-2024)</div>

```js
Plot.plot({
  width: 1100,
  height: 500,
  marginLeft: 60,
  marginRight: 60,
  style: {
    background: "rgba(0, 30, 50, 0.5)",
    color: "#e0f2f7"
  },
  x: {
    label: "Date",
    grid: true,
    tickFormat: d3.timeFormat("%b %Y")
  },
  y: {
    label: "Heavy Metals (ppb)",
    grid: true,
    domain: [0, 80]
  },
  color: {
    domain: ["North", "South", "East", "West"],
    range: ["#2ed573", "#ffa502", "#5ddbff", "#ff4757"],
    legend: true
  },
  marks: [
    // Regulatory limit line
    Plot.ruleY([30], {
      stroke: "#ff4757",
      strokeWidth: 3,
      strokeDasharray: "8,4"
    }),
    Plot.text([{x: new Date("2024-06-01"), y: 31}], {
      x: "x",
      y: "y",
      text: ["← Regulatory Limit (30 ppb)"],
      fill: "#ff4757",
      fontSize: 13,
      textAnchor: "start"
    }),
    
    // Concern threshold line
    Plot.ruleY([20], {
      stroke: "#ffa502",
      strokeWidth: 2,
      strokeDasharray: "4,4"
    }),
    Plot.text([{x: new Date("2024-06-01"), y: 21}], {
      x: "x",
      y: "y",
      text: ["← Concern Threshold (20 ppb)"],
      fill: "#ffa502",
      fontSize: 13,
      textAnchor: "start"
    }),
    
    // Station data
    Plot.line(waterQuality, {
      x: "date",
      y: "heavy_metals_ppb",
      stroke: "station_id",
      strokeWidth: 3,
      curve: "catmull-rom"
    }),
    Plot.dot(waterQuality, {
      x: "date",
      y: "heavy_metals_ppb",
      fill: "station_id",
      r: 4,
      title: d => `${d.station_name}
Date: ${d3.timeFormat("%B %d, %Y")(d.date)}
Heavy Metals: ${d.heavy_metals_ppb.toFixed(1)} ppb
${d.heavy_metals_ppb > 30 ? "⚠️ VIOLATION" : d.heavy_metals_ppb > 20 ? "⚠ Concern Level" : "✓ Within Limits"}`
    })
  ]
})
```

<div class="warning-box">
  <strong>🚨 CRITICAL FINDING:</strong> 
  West station shows catastrophic heavy metal contamination, consistently exceeding both the concern threshold (20 ppb) and regulatory limit (30 ppb). Peak contamination reached ${maxHeavyMetals.toFixed(0)} ppb—more than <strong>${(maxHeavyMetals/30).toFixed(1)}x the legal limit</strong>. Meanwhile, other stations remain well within safe parameters, indicating a localized point-source pollutant.
</div>

##  Ecological Devastation | Fish Population Collapse
<div class="chart-title">Fish Population Trends by Species and Station (Quarterly Surveys)</div>

```js
Plot.plot({
  width: 1100,
  height: 500,
  marginLeft: 60,
  style: {
    background: "rgba(0, 30, 50, 0.5)",
    color: "#e0f2f7"
  },
  x: {
    label: "Survey Date",
    grid: true,
    tickFormat: d3.timeFormat("%b %Y")
  },
  y: {
    label: "Fish Count (Standardized Survey)",
    grid: true
  },
  color: {
    domain: ["Trout-West", "Bass-West", "Carp-West", "Trout-North", "Trout-South", "Trout-East"],
    range: ["#ff4757", "#ff6348", "#ffa502", "#2ed573", "#5ddbff", "#a29bfe"],
    legend: true
  },
  marks: [
    Plot.line(fishSurveys, {
      x: "date",
      y: "count",
      stroke: d => `${d.species}-${d.station_id}`,
      strokeWidth: 3,
      curve: "catmull-rom"
    }),
    Plot.dot(fishSurveys, {
      x: "date",
      y: "count",
      fill: d => `${d.species}-${d.station_id}`,
      r: 6,
      title: d => `${d.species} at ${d.station_id} Station
Date: ${d3.timeFormat("%B %Y")(d.date)}
Count: ${d.count} fish
Sensitivity: ${d.pollution_sensitivity}
Avg Length: ${d.avg_length_cm} cm
Avg Weight: ${d.avg_weight_g}g`
    })
  ]
})
```

<div class="evidence-box">
  <strong>🔍 BIOLOGICAL EVIDENCE:</strong> Trout populations at West station (high pollution sensitivity) 
  crashed by <strong>${troutDecline}%</strong> from 2023 to 2024. This species-specific mortality at a single 
  location is the ecological smoking gun. Meanwhile, trout populations at North, South, and East stations 
  remained stable, proving the contamination is localized to the West station area. Bass (medium sensitivity) 
  showed moderate decline, while carp (low sensitivity) remained relatively stable—exactly matching the 
  toxicological profile of heavy metal poisoning.
</div>

---

