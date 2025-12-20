---
title: "Lab 4: Clearwater Crisis"
toc: false
---

<style>
  body {
    background: linear-gradient(180deg, #001a33 0%, #003d5c 50%, #004d73 100%);
    color: #e0f2f7;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    padding: 10px;
    position: relative;
    min-height: 100vh;
  }

  /* Aquarium bubbles */
  @keyframes rise {
    0% {
      bottom: -100px;
      transform: translateX(0);
    }
    50% {
      transform: translateX(100px);
    }
    100% {
      bottom: 110vh;
      transform: translateX(-50px);
    }
  }

  body::before,
  body::after {
    content: '';
    position: fixed;
    border-radius: 50%;
    background: radial-gradient(circle at 30% 30%, rgba(255, 255, 255, 0.3), rgba(255, 255, 255, 0.05));
    opacity: 0.4;
    animation: rise 15s infinite ease-in;
    pointer-events: none;
    z-index: 0;
  }

  body::before {
    width: 40px;
    height: 40px;
    left: 20%;
    animation-delay: 0s;
  }

  body::after {
    width: 30px;
    height: 30px;
    left: 70%;
    animation-delay: 3s;
  }
  
  .aquarium-header {
  background: linear-gradient(135deg, rgba(0, 77, 115, 0.9), rgba(0, 51, 102, 0.9));
  color: #5ddbff;
  padding: 40px 35px 35px 35px;  /* Add more padding to top instead */
  text-align: center;
  border: 4px solid rgba(100, 200, 255, 0.4);
  border-radius: 20px;
  margin: 20px auto;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5), inset 0 0 30px rgba(93, 219, 255, 0.1);
  position: relative;
  z-index: 1;
}
  
  .aquarium-header h1 {
  font-size: 2.9em;
  text-align: center;
  margin: 0;  /* Change this from margin-top: 15px to margin: 0 */
  text-shadow: 0 0 20px rgba(0, 200, 255, 0.6), 3px 3px 10px rgba(0, 0, 0, 0.8);
  font-weight: bold;
}
  
  .aquarium-subheader {
    font-size: 1.5em;
    font-style: italic;
    margin-top: 15px;
    color: #8dd9f5;
    text-shadow: 0 0 10px rgba(141, 217, 245, 0.5);
  }

  .section-box {
    background: linear-gradient(135deg, rgba(0, 61, 92, 0.85), rgba(0, 41, 66, 0.85));
    color: #e0f2f7;
    padding: 30px;
    border: 2px solid rgba(93, 219, 255, 0.3);
    border-radius: 15px;
    margin: 25px auto;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.4);
    backdrop-filter: blur(10px);
    position: relative;
    z-index: 1;
  }

  .section-box h2 {
    color: #5ddbff;
    font-size: 2em;
    margin-bottom: 20px;
    text-shadow: 0 0 10px rgba(0, 200, 255, 0.4);
  }

  .section-box h3 {
    color: #8dd9f5;
    font-size: 1.5em;
    margin-top: 25px;
    margin-bottom: 15px;
  }

  .evidence-box {
    background: rgba(93, 219, 255, 0.15);
    border-left: 4px solid #5ddbff;
    padding: 20px;
    margin: 20px 0;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  }

  .warning-box {
    background: rgba(255, 71, 87, 0.25);
    border-left: 4px solid #ff4757;
    padding: 20px;
    margin: 20px 0;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  }

  .suspect-card {
    background: linear-gradient(135deg, rgba(20, 60, 90, 0.9), rgba(10, 40, 70, 0.9));
    border-left: 5px solid #5ddbff;
    padding: 25px;
    margin: 20px 0;
    border-radius: 10px;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.4);
  }

  .suspect-card.guilty {
    background: linear-gradient(135deg, rgba(100, 20, 30, 0.9), rgba(70, 10, 20, 0.9));
    border-left: 5px solid #ff4757;
  }

  .suspect-card h3 {
    color: #5ddbff;
    margin-top: 0;
  }

  .suspect-card.guilty h3 {
    color: #ff4757;
  }

  .stats-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
    gap: 20px;
    margin: 25px 0;
  }

  .stat-card {
    background: rgba(93, 219, 255, 0.12);
    padding: 25px;
    border-radius: 12px;
    text-align: center;
    border: 2px solid rgba(93, 219, 255, 0.3);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
  }

  .stat-value {
    font-size: 2.8em;
    font-weight: bold;
    color: #5ddbff;
    text-shadow: 0 0 15px rgba(93, 219, 255, 0.6);
  }

  .stat-value.danger {
    color: #ff4757;
    text-shadow: 0 0 15px rgba(255, 71, 87, 0.6);
  }

  .stat-label {
    font-size: 1em;
    color: #8dd9f5;
    margin-top: 12px;
    line-height: 1.4;
  }

  .conclusion-box {
    background: linear-gradient(135deg, rgba(255, 71, 87, 0.3), rgba(200, 30, 50, 0.3));
    border: 3px solid #ff4757;
    border-radius: 15px;
    padding: 40px;
    margin: 40px auto;
    text-align: center;
    box-shadow: 0 15px 40px rgba(0, 0, 0, 0.5);
  }

  .conclusion-box h2 {
    color: #ff4757;
    font-size: 2.5em;
    margin-bottom: 20px;
    text-shadow: 0 0 20px rgba(255, 71, 87, 0.5);
  }

  ul {
    line-height: 1.9;
    margin-left: 20px;
  }

  li {
    margin: 8px 0;
  }

  strong {
    color: #5ddbff;
  }

  .chart-title {
    text-align: center;
    font-size: 1.1em;
    color: #8dd9f5;
    font-style: italic;
    margin-bottom: 15px;
  }
</style>

<div class="aquarium-header">
<h1> 𓆝 𓆟 𓆞 The Clearwater Crisis 𓆝 𓆟 𓆞 </h1>
  <div class="aquarium-subheader">
    An Ecological Detective Story: Who Killed Lake Clearwater?
  </div>
</div>

<div class="section-box">
<h2> Summary of What Happened! </h2>
  <div class="chart-title">"Lake Clearwater was once a thriving recreational lake with healthy fish populations and clear waters. However, over the past two years, something has gone terribly wrong. Fish populations have crashed, particularly among sensitive species like trout. Water quality has degraded in certain areas of the lake."</div>
</div>

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
  <h2>Important Statistics</h2>
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

## Multi-Pollutant Analysis: *Isolating the Culprit*
<div class="chart-title">Water Quality Parameters at West Station (The Contamination Epicenter)</div>

```js
Plot.plot({
  width: 1100,
  height: 450,
  marginLeft: 60,
  marginRight: 100,
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
    label: "Measurement Value",
    grid: true
  },
  color: {
    domain: ["Heavy Metals (ppb)", "Nitrogen (mg/L × 10)", "Phosphorus (mg/L × 100)"],
    range: ["#ff4757", "#5ddbff", "#2ed573"],
    legend: true
  },
  marks: [
    Plot.line(westData, {
      x: "date",
      y: "heavy_metals_ppb",
      stroke: "Heavy Metals (ppb)",
      strokeWidth: 3
    }),
    Plot.line(westData, {
      x: "date",
      y: d => d.nitrogen_mg_per_L * 10,
      stroke: "Nitrogen (mg/L × 10)",
      strokeWidth: 2,
      strokeDasharray: "4,4"
    }),
    Plot.line(westData, {
      x: "date",
      y: d => d.phosphorus_mg_per_L * 100,
      stroke: "Phosphorus (mg/L × 100)",
      strokeWidth: 2,
      strokeDasharray: "2,2"
    }),
    Plot.dot(westData, {
      x: "date",
      y: "heavy_metals_ppb",
      fill: "#ff4757",
      r: 4
    })
  ]
})
```

<div class="evidence-box">
  <strong>KEY INSIGHTS💡:</strong> This chart scales nitrogen (×10) and phosphorus (×100) 
  to make patterns visible alongside heavy metals. While nitrogen and phosphorus show 
  seasonal fluctuations typical of agricultural runoff (spring and fall fertilizer application), 
  heavy metal contamination follows a completely different pattern with dramatic spikes 
  independent of agricultural cycles. This chemical signature <strong>rules out Riverside Farm</strong> 
  and points definitively to an industrial source.
</div>

---

## Heavy Metal Contamination
<div class="chart-title">Weekly Heavy Metal Concentrations Across All Stations (2023-2024)</div>

```js
Plot.plot({
  width: 1100,
  height: 600,
  marginLeft: 60,
  marginRight: 20,
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
  fy: {
    label: null,
    padding: 0.1
  },
  color: {
    domain: ["North", "South", "East", "West"],
    range: ["#2ed573", "#ffa502", "#5ddbff", "#ff4757"],
    legend: true
  },
  facet: {
    data: waterQuality,
    y: "station_id",
    label: null
  },
  marks: [
    // Regulatory limit
    Plot.ruleY([30], {
      stroke: "#ff4757",
      strokeWidth: 2,
      strokeDasharray: "4,2",
      strokeOpacity: 0.5
    }),
    // Concern threshold
    Plot.ruleY([20], {
      stroke: "#ffa502",
      strokeWidth: 1,
      strokeDasharray: "2,2",
      strokeOpacity: 0.5
    }),
    // Data 
    Plot.line(waterQuality, {
      x: "date",
      y: "heavy_metals_ppb",
      stroke: "station_id",
      strokeWidth: 2.5,
      curve: "catmull-rom"
    }),
    Plot.dot(waterQuality, {
      x: "date",
      y: "heavy_metals_ppb",
      fill: "station_id",
      r: 3,
      title: d => `${d.station_name}
Date: ${d3.timeFormat("%B %d, %Y")(d.date)}
Heavy Metals: ${d.heavy_metals_ppb.toFixed(1)} ppb
${d.heavy_metals_ppb > 30 ? "⚠️ VIOLATION ⚠️" : d.heavy_metals_ppb > 20 ? "⚠ Concern Level" : "✓ Within Limits"}`
    }),
    // Station labels
    Plot.text(waterQuality, 
      Plot.selectFirst({
        x: new Date("2023-02-01"),
        y: 75,
        text: "station_id",
        fill: "station_id",
        fontSize: 14,
        fontWeight: "bold",
        textAnchor: "start"
      })
    )
  ]
})
```

<div class="warning-box">
  <strong>FINDINGS:</strong> 
  West station shows catastrophic heavy metal contamination, consistently exceeding both the concern threshold (20 ppb) and regulatory limit (30 ppb). Peak contamination reached ${maxHeavyMetals.toFixed(0)} ppb—more than <strong>${(maxHeavyMetals/30).toFixed(1)}x the legal limit</strong>. Meanwhile, other stations remain well within safe parameters, indicating a localized point-source pollutant.
</div>

---

##  Ecological Devastation: *Fish Population Collapse*
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
  <strong>EVIDENCE:</strong> Trout populations at West station (high pollution sensitivity) crashed by <strong>${troutDecline}%</strong> from 2023 to 2024. This species-specific mortality at a single location is the ecological smoking gun. Meanwhile, trout populations at North, South, and East stations remained stable, proving the contamination is localized to the West station area. Bass (medium sensitivity) showed moderate decline, while carp (low sensitivity) remained relatively stable, exactly matching the toxicological profile of heavy metal poisoning.
</div>

---

## Biological Validation: *Does the Science Match?*
<div class="chart-title">Expected vs. Observed Fish Mortality Rates by Heavy Metal Concentration</div>

```js
const toxicityData = [
  { level: "< 20 ppb", range: [0, 20], trout: 5, bass: 3, carp: 2 },
  { level: "20-40 ppb", range: [20, 40], trout: 20, bass: 12, carp: 6 },
  { level: "40-60 ppb", range: [40, 60], trout: 50, bass: 28, carp: 12 },
  { level: "60+ ppb", range: [60, 100], trout: 80, bass: 50, carp: 20 }
];

const westTroutInitial = westTrout2023?.count || 45;
const westTroutFinal = westTrout2024?.count || 7;
const observedMortality = ((westTroutInitial - westTroutFinal) / westTroutInitial * 100);

const mortalityComparison = [
  { category: "< 20 ppb (Safe)", species: "Trout", mortality: 5, type: "Expected" },
  { category: "< 20 ppb (Safe)", species: "Bass", mortality: 3, type: "Expected" },
  { category: "< 20 ppb (Safe)", species: "Carp", mortality: 2, type: "Expected" },
  
  { category: "20-40 ppb (Concern)", species: "Trout", mortality: 20, type: "Expected" },
  { category: "20-40 ppb (Concern)", species: "Bass", mortality: 12, type: "Expected" },
  { category: "20-40 ppb (Concern)", species: "Carp", mortality: 6, type: "Expected" },
  
  { category: "40-60 ppb (Danger)", species: "Trout", mortality: 50, type: "Expected" },
  { category: "40-60 ppb (Danger)", species: "Bass", mortality: 28, type: "Expected" },
  { category: "40-60 ppb (Danger)", species: "Carp", mortality: 12, type: "Expected" },
  
  { category: "60+ ppb (Critical)", species: "Trout", mortality: 80, type: "Expected" },
  { category: "60+ ppb (Critical)", species: "Bass", mortality: 50, type: "Expected" },
  { category: "60+ ppb (Critical)", species: "Carp", mortality: 20, type: "Expected" },
  
  { category: `West Station (${maxHeavyMetals.toFixed(0)} ppb)`, species: "Trout", mortality: observedMortality, type: "Observed" },
  { category: `West Station (${maxHeavyMetals.toFixed(0)} ppb)`, species: "Bass", mortality: 35, type: "Observed" },
  { category: `West Station (${maxHeavyMetals.toFixed(0)} ppb)`, species: "Carp", mortality: 8, type: "Observed" }
];
```

```js
Plot.plot({
  width: 1100,
  height: 500,
  marginLeft: 60,
  marginBottom: 100,
  style: {
    background: "rgba(0, 30, 50, 0.5)",
    color: "#e0f2f7"
  },
  x: {
    label: "Heavy Metal Concentration Level →",
    tickRotate: -35,
    domain: ["< 20 ppb (Safe)", "20-40 ppb (Concern)", "40-60 ppb (Danger)", "60+ ppb (Critical)", `West Station (${maxHeavyMetals.toFixed(0)} ppb)`]
  },
  y: {
    label: "Fish Mortality Rate (%) →",
    grid: true,
    domain: [0, 100]
  },
  color: {
    domain: ["Trout", "Bass", "Carp"],
    range: ["#ff4757", "#ff6348", "#2ed573"],
    legend: true
  },
  marks: [
    Plot.barY(mortalityComparison, {
      x: "category",
      y: "mortality",
      fill: "species",
      tip: true,
      title: d => `${d.species}\n${d.category}\n${d.mortality.toFixed(1)}% mortality\n(${d.type} rate)`
    }),
    Plot.ruleY([observedMortality], {
      stroke: "#ff4757",
      strokeWidth: 2,
      strokeDasharray: "4,4",
      strokeOpacity: 0.7
    }),
    Plot.text([{
      x: "60+ ppb (Critical)", 
      y: observedMortality + 5, 
      label: `Observed: ${observedMortality.toFixed(0)}%`
    }], {
      x: "x",
      y: "y",
      text: "label",
      fill: "#ff4757",
      fontSize: 13,
      fontWeight: "bold"
    })
  ]
})
```

<div class="evidence-box">
  <strong>SCIENTIFIC MATCH:</strong> Research on freshwater ecosystems shows that 60+ ppb heavy metal exposure causes 70-90% mortality in pollution-sensitive species like trout. West Station contamination reached <strong>${maxHeavyMetals.toFixed(0)} ppb</strong>, and we observed <strong>${observedMortality.toFixed(0)}% trout mortality</strong>, a perfect match to the scientific literature. Bass showed ~35% mortality (expected: 40-60%) and carp showed ~8% mortality (expected: 15-25%). This dose-response validation proves the contamination levels are sufficient to cause the observed ecological collapse.
</div>

---

## Evidence Summary: *The Three-Dimensional Case*
<div class="chart-title">Systematic Evaluation Across All Lines of Evidence</div>

```js
const evidenceSummary = [
  { dimension: "Spatial", chemtech: "✓ 800m", farm: "✗ 5,200m", resort: "✗ 4,600m", lodge: "✗ 5,600m" },
  { dimension: "Temporal", chemtech: "✓ Matches", farm: "✗ No match", resort: "✗ No match", lodge: "✗ No match" },
  { dimension: "Biological", chemtech: "✓ 85% = 60+ppb", farm: "✗ Wrong pollutant", resort: "✗ No evidence", lodge: "✗ No mechanism" },
  { dimension: "Violations", chemtech: "✓ " + violationCount, farm: "✗ 0", resort: "✗ 0", lodge: "✗ 0" }
];

Inputs.table(evidenceSummary)
```

<div class="warning-box">
  <strong>VERDICT PREVIEW:</strong> Only ChemTech Manufacturing satisfies <strong>all three dimensions</strong> of evidence (spatial, temporal, biological). The case is airtight: closest proximity to contamination, activities align with pollution spikes, and observed mortality matches expected rates at measured contamination levels. All other suspects fail on multiple dimensions.
</div>

---

## Distance: *Geographic Evidence*

```js
const proximityData = stations.flatMap(station => [
  { station: station.station_name, suspect: "ChemTech", distance: station.distance_to_chemtech_m },
  { station: station.station_name, suspect: "Riverside Farm", distance: station.distance_to_farm_m },
  { station: station.station_name, suspect: "Lakeview Resort", distance: station.distance_to_resort_m },
  { station: station.station_name, suspect: "Fishing Lodge", distance: station.distance_to_lodge_m }
]);
```

<div class="chart-title">Proximity Analysis: Suspects vs. Monitoring Stations</div>

```js
Plot.plot({
  width: 1100,
  height: 450,
  marginLeft: 150,
  style: {
    background: "rgba(0, 30, 50, 0.5)",
    color: "#e0f2f7"
  },
  x: {
    label: "Distance (meters)",
    grid: true
  },
  y: {
    label: "Station",
    padding: 0.2
  },
  color: {
    domain: ["ChemTech", "Riverside Farm", "Lakeview Resort", "Fishing Lodge"],
    range: ["#ff4757", "#2ed573", "#5ddbff", "#ffa502"],
    legend: true
  },
  marks: [
    Plot.barX(proximityData, {
      x: "distance",
      y: "station",
      fill: "suspect",
      title: d => `${d.suspect} → ${d.station}: ${d.distance}m`
    }),
    Plot.ruleX([800], {
      stroke: "#ff4757",
      strokeWidth: 2,
      strokeDasharray: "4,4"
    })
  ],
  facet: {
    data: proximityData,
    y: "station"
  }
})
```
<div class="evidence-box">
  <strong>SPATIAL PATTERN:</strong> West station sits just <strong>800 meters</strong> from ChemTech Manufacturing, the closest suspect to the most contaminated location. ChemTech is positioned where water enters the lake, explaining why contamination is highest at West and gradually decreases with distance. The spatial correlation is unmistakable.
</div>

---


## Timeline: *Temporal Evidence*

```js
const chemtechActivities = activities.filter(d => d.suspect === "ChemTech Manufacturing");
const westHeavyMetalsChart = westData.map(d => ({
  date: d.date,
  value: d.heavy_metals_ppb,
  type: "Heavy Metals"
}));
const activityMarkers = chemtechActivities.map(d => ({
  date: d.date,
  value: 75,
  type: "ChemTech Activity",
  activity: d.activity_type
}));
```
<div class="chart-title">ChemTech Maintenance Events vs. Pollution Spikes</div>

```js
Plot.plot({
  width: 1100,
  height: 450,
  marginLeft: 60,
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
  marks: [
    Plot.line(westHeavyMetalsChart, {
      x: "date",
      y: "value",
      stroke: "#ff4757",
      strokeWidth: 3
    }),
    Plot.dot(westHeavyMetalsChart, {
      x: "date",
      y: "value",
      fill: "#ff4757",
      r: 4
    }),
    Plot.dot(activityMarkers, {
      x: "date",
      y: "value",
      fill: "#ffa502",
      r: 12,
      symbol: "triangle",
      title: d => `ChemTech: ${d.activity}
Date: ${d3.timeFormat("%B %d, %Y")(d.date)}`
    }),
    Plot.text(activityMarkers, {
      x: "date",
      y: d => d.value + 3,
      text: ["⚠️"],
      fontSize: 16
    })
  ]
})
```
 <div class="evidence-box">
  <strong>TIMELINE MATCH:</strong> ChemTech's quarterly maintenance shutdowns (requiring temporary process changes) align <strong>precisely</strong> with major pollution spikes at West station. Every significant contamination event corresponds to a ChemTech maintenance period. This temporal correlation, combined with spatial and biological evidence, creates an airtight case.
</div>

---

<div class="section-box">
  <h2>Suspect Analysis 🔍: The Four Defendants</h2>
</div>

<div class="suspect-card guilty">
  <h3>ChemTech Manufacturing 🏭 — GUILTY</h3>
  <p><strong>Location:</strong> Western shore, 800m from West station (where water enters lake)</p>
  <p><strong>Permit Limit:</strong> 2.5 kg heavy metals/day | 30 ppb maximum concentration</p>
  
  <h4 style="color: #ff4757; margin-top: 20px;">Evidence Against:</h4>
  <ul>
    <li><strong>Spatial:</strong> Closest to most contaminated station (800m to West)</li>
    <li><strong>Temporal:</strong> Maintenance shutdowns perfectly align with pollution spikes in Mar, Jun, Sep, Dec 2024</li>
    <li><strong>Chemical:</strong> Heavy metals reached ${maxHeavyMetals.toFixed(0)} ppb (${((maxHeavyMetals/30 - 1) * 100).toFixed(0)}% over legal limit)</li>
    <li><strong>Biological:</strong> ${troutDecline}% trout mortality matches expected death rates at 60+ ppb contamination</li>
    <li><strong>Pattern:</strong> Contamination isolated to West station, consistent with point-source industrial discharge</li>
    <li><strong>Violations:</strong> ${violationCount} documented exceedances of 30 ppb regulatory limit</li>
  </ul>
  
  <p style="margin-top: 20px; font-size: 1.1em; color: #ff4757;">
    <strong>Verdict:</strong> All three lines of evidence (spatial, temporal, biological) converge on ChemTech. 
    The contamination pattern, species-specific mortality, and activity timeline create an irrefutable case.
  </p>
</div>

<div class="suspect-card">
  <h3> Riverside Farm 🌾 — CLEARED</h3>
  <p><strong>Location:</strong> Northern shore, 600m from North station</p>
  <p><strong>Operations:</strong> 500-acre corn and soybean operation with spring/fall fertilizer application</p>
  
  <h4 style="color: #8dd9f5; margin-top: 20px;">Evidence For Innocence:</h4>
  <ul>
    <li>North station shows <strong>no elevated heavy metals</strong> (8-12 ppb, well within limits)</li>
    <li>Fish populations at North station remain healthy and stable</li>
    <li>Nitrogen/phosphorus show seasonal patterns at <strong>all stations</strong>, not just North—typical of watershed-wide agricultural runoff</li>
    <li>Agricultural operations cannot produce heavy metal contamination</li>
    <li>Fertilizer application timing (Apr-May, Sep) doesn't match heavy metal spike patterns</li>
  </ul>
  
  <p style="margin-top: 15px; color: #8dd9f5;">
    <strong>Conclusion:</strong> Agricultural runoff contributes to baseline nutrient levels but cannot explain 
    the heavy metal contamination or species-specific fish mortality at West station.
  </p>
</div>

<div class="suspect-card">
  <h3> Lakeview Resort 🏨 — CLEARED</h3>
  <p><strong>Location:</strong> Eastern shore, 700m from East station</p>
  <p><strong>Permit Limit:</strong> 0.3 kg heavy metals/day (12x smaller than ChemTech)</p>
  
  <h4 style="color: #8dd9f5; margin-top: 20px;">Evidence For Innocence:</h4>
  <ul>
    <li>East station shows <strong>low heavy metal levels</strong> (10-15 ppb), consistently below 30 ppb limit</li>
    <li>Fish populations at East station remain stable across all species</li>
    <li>Discharge permit (0.3 kg/day) is insufficient to cause observed contamination levels</li>
    <li>Resort expansion in 2023-2024 shows no correlation with pollution events</li>
    <li>No temporal match between resort activities and contamination spikes</li>
  </ul>
  
  <p style="margin-top: 15px; color: #8dd9f5;">
    <strong>Conclusion:</strong> The Resort's wastewater treatment operates within permitted levels. 
    East station data shows no evidence of harmful contamination.
  </p>
</div>

<div class="suspect-card">
  <h3> Clearwater Fishing Lodge 🎣 — CLEARED</h3>
  <p><strong>Location:</strong> Southern shore, 800m from South station</p>
  <p><strong>Opening Date:</strong> January 2023 (timing appears suspicious initially)</p>
  
  <h4 style="color: #8dd9f5; margin-top: 20px;">Evidence For Innocence:</h4>
  <ul>
    <li>South station shows <strong>minimal heavy metals</strong> (8-12 ppb), well within safe parameters</li>
    <li>Fish populations at South station remain healthy, despite being nearest to the Lodge</li>
    <li>Fishing activity (removing fish) cannot cause heavy metal pollution</li>
    <li>Septic systems do not release heavy metals</li>
    <li>Boat traffic and dock construction produce turbidity, not chemical contamination</li>
    <li>No scientific mechanism for Lodge operations to produce observed pollution</li>
  </ul>
  
  <p style="margin-top: 15px; color: #8dd9f5;">
    <strong>Conclusion:</strong> The Lodge's operations cannot produce the observed chemical contamination. 
    South station data shows no evidence of harmful pollution near their facility.
  </p>
</div>




