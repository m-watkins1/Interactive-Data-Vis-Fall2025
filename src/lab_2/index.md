---
title: "Lab 2: Subway Staffing"
toc: true
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).

# Lab 2 | New York City Summer Operations Dashboard
The below dashboard answers the following questions:

1. How did local events impact ridership in summer 2025? What effect did the July 15th fare increase have?
2. How do the stations compare when it comes to response time? Which are the best, which are the worst?
3. Which three stations need the most staffing help for next summer based on the 2026 event calendar?

[BONUS] If you had to prioritize one station to get increased staffing, which would it be and why?
The data could yield additional interesting trends, but these questions are the most important. The data has been provided in multiple datasets, all in the /data folder.

```js
const incidents = FileAttachment("data/incidents.csv").csv({typed: true});
const localEvents = FileAttachment("data/local_events.csv").csv({typed: true});
const upcomingEvents = FileAttachment("data/upcoming_events.csv").csv({typed: true});
const ridership = FileAttachment("data/ridership.csv").csv({typed: true});

const currentStaffing = {
  "Times Sq-42 St": 19,
  "Grand Central-42 St": 18,
  "34 St-Penn Station": 15,
  "14 St-Union Sq": 4,
  "Fulton St": 17,
  "42 St-Port Authority": 14,
  "Herald Sq-34 St": 15,
  "Canal St": 4,
  "59 St-Columbus Circle": 6,
  "125 St": 7,
  "96 St": 19,
  "86 St": 19,
  "72 St": 10,
  "66 St-Lincoln Center": 15,
  "50 St": 20,
  "28 St": 13,
  "23 St": 8,
  "Christopher St": 15,
  "Houston St": 18,
  "Spring St": 12,
  "Chambers St": 18,
  "Wall St": 9,
  "Bowling Green": 6,
  "West 4 St-Wash Sq": 4,
  "Astor Pl": 7
};
```

## Question 1
### How did local events impact ridership in summer of 2025? What effcet did the July 15th fare increase have?

```js
const ridershipWithTotal = ridership.map(d => ({
  ...d,
  total_traffic: d.entrances + d.exits,
  date_obj: new Date(d.date)
}));

const avgByStation = d3.rollup(
  ridershipWithTotal,
  v => d3.mean(v, d => d.total_traffic),
  d => d.station
);

const eventDates = new Set(localEvents.map(d => d.date));
const ridershipWithEvents = ridershipWithTotal.map(d => ({
  ...d,
  has_event: eventDates.has(d.date),
  is_post_fare_increase: new Date(d.date) >= new Date("2025-07-15")
}));
```

```js
Plot.plot({
  width: 1000,
  height: 400,
  marginLeft: 60,
  marginBottom: 60,
  x: {
    label: "Date",
    type: "time"
  },
  y: {
    label: "Total Daily Traffic (Entrances + Exits)",
    grid: true
  },
  color: {
    legend: true,
    label: "Event Day"
  },
  marks: [
    // Main ridership line with grouping by date
    Plot.lineY(
      ridershipWithEvents,
      Plot.groupX(
        {y: "sum"},
        {
          x: "date_obj",
          y: "total_traffic",
          stroke: "#4269d0",
          strokeWidth: 2
        }
      )
    ),
    // Event day markers
    Plot.dot(
      ridershipWithEvents.filter(d => d.has_event),
      Plot.groupX(
        {y: "sum"},
        {
          x: "date_obj",
          y: "total_traffic",
          fill: "#ff6b6b",
          r: 4,
          tip: true
        }
      )
    ),
    // Fare increase line annotation
    Plot.ruleX(
      [new Date("2025-07-15")],
      {
        stroke: "#e63946",
        strokeWidth: 2,
        strokeDasharray: "5,5"
      }
    ),
    // Fare increase text annotation
    Plot.text(
      [new Date("2025-07-15")],
      {
        x: new Date("2025-07-15"),
        y: 1800000,
        text: ["Fare Increase: $2.75 → $3.00"],
        dy: -10,
        fill: "#e63946",
        fontSize: 12,
        fontWeight: "bold"
      }
    )
  ]
})
```

```js
const eventImpact = Array.from(
  d3.rollup(
    ridershipWithEvents,
    v => {
      const eventDays = v.filter(d => d.has_event);
      const nonEventDays = v.filter(d => !d.has_event);
      const eventAvg = d3.mean(eventDays, d => d.total_traffic) || 0;
      const nonEventAvg = d3.mean(nonEventDays, d => d.total_traffic) || 0;
      return {
        event_avg: eventAvg,
        non_event_avg: nonEventAvg,
        increase_pct: nonEventAvg > 0 ? ((eventAvg - nonEventAvg) / nonEventAvg) * 100 : 0,
        event_days: eventDays.length
      };
    },
    d => d.station
  ),
  ([station, stats]) => ({station, ...stats})
).sort((a, b) => b.increase_pct - a.increase_pct).slice(0, 5);
```

```js
Plot.plot({
  width: 800,
  height: 400,
  marginLeft: 180,
  x: {
    label: "Ridership Percentage % Increase on Event Days",
    grid: true
  },
  y: {
    label: null
  },
  marks: [
    Plot.barX(eventImpact, {
      x: "increase_pct",
      y: "station",
      fill: "#ff6b6b",
      tip: true
    }),
    Plot.text(eventImpact, {
      x: "increase_pct",
      y: "station",
      text: d => `+${d.increase_pct.toFixed(1)}%`,
      dx: 20,
      fontSize: 11
    }),
    Plot.ruleX([0])
  ]
})
```

// Bin entrances for histogram
const entranceBins = Plot.binX(dailyWithEvents, { x: "entrances", thresholds: 10 });
const maxBin = d3.max(entranceBins, d => d.length ?? 0);

// Plot: Daily Entrances Histogram
Plot.plot({
  height: 360,
  width: 700,
  x: { label: "Daily Entrances" },
  y: { label: "Number of Days" },
  marks: [
    Plot.barY(entranceBins, { x: "x1", x2: "x2", y: "length", fill: "#69b3a2" }),
    Plot.ruleX([{ x: 18000 }], { stroke: "red", strokeWidth: 2, strokeDasharray: "4 4" }),
    Plot.text(
      [{ x: 18000, y: maxBin + 1, label: "Fare Increase July 15" }],
      { x: "x", y: "y", text: "label", fill: "red", dy: -10 }
    )
  ]
});

// --- QUESTION 2: Response Time by Station ---
const responseStats = Array.from(
  d3.group(incidents, d => d.station),
  ([station, rows]) => ({ station, median: d3.median(rows, d => d.response_time_minutes) })
).sort((a, b) => d3.descending(a.median, b.median));

const worstStation = responseStats[0];
const bestStation = responseStats.at(-1);

// Plot: Median Response Time
Plot.plot({
  height: 700,
  marginLeft: 180,
  x: { label: "Median Response Time (minutes)" },
  marks: [
    Plot.barX(responseStats, { x: "median", y: "station", fill: "#4287f5" }),
    Plot.text(
      [{ x: worstStation.median, y: worstStation.station, label: `Worst (${worstStation.median.toFixed(1)}m)` }],
      { x: "x", y: "y", text: "label", fill: "red", dx: 5 }
    ),
    Plot.text(
      [{ x: bestStation.median, y: bestStation.station, label: `Best (${bestStation.median.toFixed(1)}m)` }],
      { x: "x", y: "y", text: "label", fill: "green", dx: 5 }
    )
  ]
});

// --- QUESTION 3: Staffing Needs for Summer 2026 ---
const expected2026 = Array.from(
  d3.rollup(upcoming_events, v => d3.sum(v, d => d.expected_attendance), d => d.nearby_station),
  ([station, total]) => ({ station, total })
);

const highIncidents = d3.rollup(
  incidents.filter(d => d.severity === "high"),
  v => v.length,
  d => d.station
);

const staffingNeedScores = expected2026.map(e => ({
  station: e.station,
  expected: e.total,
  high: highIncidents.get(e.station) ?? 0,
  staff: currentStaffing[e.station] ?? 0,
  score: e.total * 0.6 + (highIncidents.get(e.station) ?? 0) * 0.3 - (currentStaffing[e.station] ?? 0) * 0.1
})).sort((a, b) => d3.descending(a.score, b.score));

const top3Staffing = staffingNeedScores.slice(0,3).map(d => ({
  x: d.score,
  y: d.station,
  label: "Top 3"
}));

// Plot: Staffing Need
Plot.plot({
  height: 650,
  marginLeft: 180,
  x: { label: "Staffing Need Score" },
  marks: [
    Plot.barX(staffingNeedScores, { x: "score", y: "station", fill: "#f5a142" }),
    Plot.text(top3Staffing, { x: "x", y: "y", text: "label", fill: "red", dx: 5 })
  ]
});

// --- Output Top 3 Stations ---
staffingNeedScores.slice(0,3)
```