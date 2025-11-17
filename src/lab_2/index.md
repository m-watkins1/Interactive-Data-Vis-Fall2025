---
title: "Lab 2: Subway Staffing"
toc: true
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).

# Lab TWO

Specifically, your dashboard should answer:

1. How did local events impact ridership in summer 2025? What effect did the July 15th fare increase have?
2. How do the stations compare when it comes to response time? Which are the best, which are the worst?
3. Which three stations need the most staffing help for next summer based on the 2026 event calendar?

[BONUS] If you had to prioritize one station to get increased staffing, which would it be and why?
The data could yield additional interesting trends, but these questions are the most important. The data has been provided in multiple datasets, all in the /data folder.
  

<!-- Import Data -->
```js
const incidents = FileAttachment("./data/incidents.csv").csv({ typed: true })
const local_events = FileAttachment("./data/local_events.csv").csv({ typed: true })
const upcoming_events = FileAttachment("./data/upcoming_events.csv").csv({ typed: true })
const ridership = FileAttachment("./data/ridership.csv").csv({ typed: true })
```

<!-- Include current staffing counts from the prompt -->

```js
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
}
```

```js
## 1. Local Event Impact on Ridership

daily = {
  const byDate = d3.group(ridership, d => d.date);
  return Array.from(byDate, ([date, rows]) => ({
    date,
    entrances: d3.sum(rows, d => d.entrances)
  })).sort((a,b) => d3.ascending(a.date, b.date));
}

daily_with_events = {
  const eventCount = d3.rollup(local_events, v => v.length, d => d.date);
  return daily.map(d => ({
    ...d,
    events: eventCount.get(d.date) ?? 0
  }));
}

Plot.plot({
  height: 340,
  x: {label: "Date"},
  y: {label: "Total Entrances"},
  marks: [
    Plot.line(daily_with_events, {x: "date", y: "entrances"}),
    Plot.dot(daily_with_events, {
      x: "date",
      y: "entrances",
      r: d => d.events * 3,
      fill: "orange",
      title: d => `${d.events} events`
    }),
    Plot.ruleX([new Date("2025-07-15")], {stroke: "red"}),
    Plot.text(
      [{date: new Date("2025-07-15"), label: "Fare Increase"}],
      {x: "date", y: 0, dy: -20, text: "label", stroke: "white", strokeWidth: 3}
    )
  ]
})

## 2. 

response_stats = {
  const g = d3.group(incidents, d => d.station);
  return Array.from(g, ([station, rows]) => ({
    station,
    median: d3.median(rows, d => d.response_time_minutes),
    count: rows.length
  })).sort((a,b) => d3.descending(a.median, b.median));
}
 
 Plot.plot({
  height: 600,
  marginLeft: 150,
  x: {label: "Median Response Time (min)"},
  marks: [
    Plot.barX(response_stats, {x: "median", y: "station"}),
    
    // Worst (top)
    Plot.text([response_stats[0]], {
      x: "median",
      y: "station",
      text: d => `Worst (${d.median.toFixed(1)}m)`,
      dx: 10,
      fill: "red"
    }),

    // Best (bottom)
    Plot.text([response_stats.at(-1)], {
      x: "median",
      y: "station",
      text: d => `Best (${d.median.toFixed(1)}m)`,
      dx: 10,
      fill: "green"
    })
  ]
})

## 3. 
expected_2026 = {
  const g = d3.rollup(upcoming_events, v => d3.sum(v, d => d.expected_attendance), d => d.nearby_station);
  return Array.from(g, ([station, total]) => ({station, total}));
}

high_incidents = {
  const g = d3.rollup(incidents.filter(d => d.severity === "high"), v => v.length, d => d.station);
  return g;
}

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
}

need_scores = {
  return expected_2026.map(e => ({
    station: e.station,
    expected: e.total,
    high: high_incidents.get(e.station) ?? 0,
    staff: current_staff[e.station] ?? 0,
    score: e.total * 0.6 + (high_incidents.get(e.station) ?? 0) * 0.3 - (current_staff[e.station] ?? 0) * 0.1
  })).sort((a,b) => d3.descending(a.score, b.score));
}

Plot.plot({
  height: 600,
  marginLeft: 150,
  x: {label: "Need Score (higher = more staffing needed)"},
  marks: [
    Plot.barX(need_scores, {x: "score", y: "station"}),

    // Annotate top 3
    Plot.text(need_scores.slice(0,3), {
      x: "score",
      y: "station",
      dx: 10,
      text: d => `Top`
    })
  ]
})

need_scores.slice(0,3)

```