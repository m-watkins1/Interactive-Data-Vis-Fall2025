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

const westTrout2023 = fishSurveys.find(d => 
  d.station_id === "West" && d.species === "Trout" && d.date.startsWith("2023-01")
);
const westTrout2024 = fishSurveys.find(d => 
  d.station_id === "West" && d.species === "Trout" && d.date.startsWith("2024-10")
);
const troutDecline = westTrout2023 && westTrout2024 ? 
  Math.round((1 - westTrout2024.count / westTrout2023.count) * 100) : 0;

const violationCount = westData.filter(d => d.heavy_metals_ppb > 30).length;
const westStation = stations.find(s => s.station_id === "West");
const distanceToChemtech = westStation?.distance_to_chemtech_m || 800;
```
<div class="section-box">
  <h2>The Statistics</h2>
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
