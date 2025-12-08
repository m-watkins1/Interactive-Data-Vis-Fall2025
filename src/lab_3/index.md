---
title: "Lab 3: Mayoral Mystery"
toc: false
---

# Lab 3: Mayoral Mystery

---

<!-- Import Data -->
```js
const nyc = await FileAttachment("data/nyc.json").json();
const results = await FileAttachment("data/election_results.csv").csv({ typed: true });
const survey = await FileAttachment("data/survey_responses.csv").csv({ typed: true });
const events = await FileAttachment("data/campaign_events.csv").csv({ typed: true });


```


```js
// The nyc file is saved in data as a topoJSON instead of a geoJSON. Thats primarily for size reasons -- it saves us 3MB of data. For Plot to render it, we have to convert it back to its geoJSON feature collection. 
const districts = topojson.feature(nyc, nyc.objects.districts)
```

```js
const resultsMap = new Map(results.map(d => [
  d.boro_cd,
  {
    ...d,
    total_votes: d.votes_candidate + d.votes_opponent,
    win_percentage: (d.votes_candidate / (d.votes_candidate + d.votes_opponent)) * 100,
    won: d.votes_candidate > d.votes_opponent
  }
]));

districts.features.forEach(feature => {
  const cd = feature.properties.BoroCD;
  const electionData = resultsMap.get(cd);
  if (electionData) {
    feature.properties = { ...feature.properties, ...electionData };
  }
});
```

```js
const totalVotes = results.reduce((sum, d) => sum + d.votes_candidate + d.votes_opponent, 0);
const candidateVotes = results.reduce((sum, d) => sum + d.votes_candidate, 0);
const districtsWon = results.filter(d => d.votes_candidate > d.votes_opponent).length;
const totalDistricts = results.length;
const overallWinPercentage = ((candidateVotes / totalVotes) * 100).toFixed(1);
```

## Campaign Performance Dashboard
### *Overall Results*

**Vote Share:** ${overallWinPercentage}% |
**Districts Won:** ${districtsWon} out of ${totalDistricts} |
**Total Votes:** ${candidateVotes.toLocaleString()} votes


---

## Election Results by District
*Research Question:* *How did the candidate do, overall? How do the results vary by district?*

```js
// Simple rendering of the NYC districts topoJSON
Plot.plot({
  // this projection is already zoomed into NYC
  width: 1000,
  height: 700,
  projection: {
    domain: districts,
    type: "mercator",
  },
  color: {
    type: "diverging",
    domain: [0, 50, 100],
    scheme: "RdBU",
    legend: true,
    label: "Vote Share (%)"
  },
  marks: [
    Plot.geo(districts, {
      fill: d => d.properties.win_percentage,
      stroke: "white",
      strokeWidth: 1.5,
      title: d => `District ${d.properties.BoroCD}
Vote Share: ${d.properties.win_percentage?.toFixed(1)}%
${d.properties.won ? '✓ WON' : '✗ LOST'}
Income: ${d.properties.income_category}
Turnout: ${d.properties.turnout_rate?.toFixed(1)}%`
    })
  ]
})
```

*Analysis:* The candidate performed strongly overall, winning ${districtsWon} out of ${totalDistricts} districts (${((districtsWon/totalDistricts)*100).toFixed(1)}%) with a ${overallWinPercentage}% vote share. The map shows district-level performance, with blue indicating districts where the candidate won (>50% vote share) and red showing areas where they lost (<50%).

```js
const eventsByDistrict = d3.rollup(
  events,
  v => ({
    count: v.length,
    totalAttendance: d3.sum(v, d => d.estimated_attendance),
    avgAttendance: d3.mean(v, d => d.estimated_attendance)
  }),
  d => d.boro_cd
);

const campaignEffectiveness = results.map(d => ({
  boro_cd: d.boro_cd,
  income_category: d.income_category,
  win_percentage: (d.votes_candidate / (d.votes_candidate + d.votes_opponent)) * 100,
  events: eventsByDistrict.get(d.boro_cd)?.count || 0,
  attendance: eventsByDistrict.get(d.boro_cd)?.totalAttendance || 0,
  doors_knocked: d.gotv_doors_knocked,
  hours_spent: d.candidate_hours_spent
}));
```

## Campaign Trail: Did the Events Pay Off?
```js
Plot.plot({
  width: 1000,
  height: 500,
  marginLeft: 60,
  grid: true,
  x: {
    label: "Number of Campaign Events →"
  },
  y: {
    label: "↑ Vote Share (%)",
    domain: [0, 100]
  },
  color: {
    legend: true,
    label: "Income Level"
  },
  marks: [
    Plot.dot(campaignEffectiveness, {
      x: "events",
      y: "win_percentage",
      fill: "income_category",
      r: d => Math.sqrt(d.attendance) / 3,
      title: d => `District ${d.boro_cd}
Events: ${d.events}
Vote Share: ${d.win_percentage.toFixed(1)}%
Total Attendance: ${d.attendance}
Income: ${d.income_category}`,
      opacity: 0.7
    }),
    Plot.linearRegressionY(campaignEffectiveness, {
      x: "events",
      y: "win_percentage",
      stroke: "#6BC767",
      strokeWidth: 3,
      strokeDasharray: "5,5"
    })
  ]
})
```

*Campaign Strategy:* There's a clear positive relationship between the number of events we held and our vote share! 
- The dashed line shows the trend: more events = better results. 
- Bubble size represents attendance.

Our bigger events in middle-income areas drove the strongest turnout.