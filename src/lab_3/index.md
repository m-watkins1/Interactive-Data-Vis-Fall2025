---
title: "Lab 3: Mayoral Mystery"
toc: false
---

# Lab 3: Mayoral Mystery

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
    scheme: "RdYlBu",
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