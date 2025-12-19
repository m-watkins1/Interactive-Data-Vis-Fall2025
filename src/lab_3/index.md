---
title: "Lab 3: Mayoral Mystery"
toc: false
---



<style>
  body {
    background: #1631a89f;
    color: #ffd700;
    font-family: 'Comic Sans MS', cursive, sans-serif;
    padding: 20px;
  }
  
  .yearbook-header {
    background: #8b0000;
    color: #ffd700;
    padding: 30px;
    text-align: center;
    border: 8px double #ffd700;
    margin: 20px auto;
    display: inline-block;
    box-shadow: 0 8px 16px rgba(0,0,0,0.3);
  }
  
  .yearbook-header h1 {
    font-size: 3em;
    text-align: center;
    margin: 0;
    text-shadow: 3px 3px #000;
    font-weight: bold;
  }
  
  .yearbook-subheader {
    font-size: 1.3em;
    font-style: italic;
    margin-top: 10px;
    color: #fff;
  }

  .paragraph {
    background: #8b0000;
    color: #ffd700;
    padding: 30px;
    text-align: center;
    border: 3px solid #ffd700;
    margin: 20px auto;
    box-shadow: 0 8px 16px rgba(0,0,0,0.3);
  }

  .paragraph-header h1 {
  font-size: 3em;
  text-align: center;
  margin: 0;
  text-shadow: 3px 3px #000;
  font-weight: bold;
  word-wrap: break-word;
  max-width: 90%;     /* keeps it from stretching edge-to-edge */
  margin-left: auto;
  margin-right: auto;
}

  
  .paragraph-subheader {
    font-size: 1.3em;
    font-style: italic;
    margin-top: 10px;
    color: #fff;
  }

</style>

</div>

<div class="yearbook-header">
  <h1>🏆 NYC MAYORAL RACE 2024 🏆</h1>
  <div class="yearbook-subheader">
    "Most Likely to Be Mayor" - Official Campaign Yearbook
  </div>
</div>


<div class="paragraph">
  <h1>LAB 3: Mayoral Mystery | Most Likely to Succeed Edition</h1>
  <div class="yearbook-subheader">
    by Madison Watkins
  </div>
</div>


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

## Election Results by District
How did the candidate do, overall? How do the results vary by district?


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
    domain: [-100, 0, 100],
    scheme: "Greens",
    legend: true,
    label: "Vote Share (%) - Darker Green = More Popular"
  },
  marks: [
    Plot.geo(districts, {
      fill: d => d.properties.win_percentage,
      stroke: "#1631a89f",
      strokeWidth: 2,
      title: d => `District ${d.properties.BoroCD}
Vote Share: ${d.properties.win_percentage?.toFixed(1)}%
${d.properties.won ? '✓ Awarded Mayor' : '✗ Try again in 4 years'}
Income: ${d.properties.income_category}
Turnout: ${d.properties.turnout_rate?.toFixed(1)}%`
    })
  ]
})
```

<div style="text-align: center;">

#### *Analysis:* 
The candidate performed strongly overall, winning ${districtsWon} out of ${totalDistricts} districts (${((districtsWon/totalDistricts)*100).toFixed(1)}%) with a ${overallWinPercentage}% vote share. The map shows the ranging district-level performance, with a darker green indicating districts where the candidate won a higher percentage of the voter share. 

#### *Stats Summary*
**Vote Share:** ${overallWinPercentage}% |
**Districts Won:** ${districtsWon} out of ${totalDistricts} |
**Total Votes:** ${candidateVotes.toLocaleString()} votes

</div>

```js
const surveyByDistrict = d3.rollup(
  survey,
  v => ({
    count: v.length,
    avgHousingAlignment: d3.mean(v, d => d.affordable_housing_alignment),
    avgTransitAlignment: d3.mean(v, d => d.public_transit_alignment),
    avgChildcareAlignment: d3.mean(v, d => d.childcare_support_alignment),
    avgBusinessAlignment: d3.mean(v, d => d.small_business_tax_alignment),
    avgPoliceAlignment: d3.mean(v, d => d.police_reform_alignment)
  }),
  d => d.boro_cd
);

const districtTopIssues = Array.from(surveyByDistrict, ([boro_cd, stats]) => {
  const issues = [
    { name: "Affordable Housing", score: stats.avgHousingAlignment },
    { name: "Public Transit", score: stats.avgTransitAlignment },
    { name: "Childcare Support", score: stats.avgChildcareAlignment },
    { name: "Small Business", score: stats.avgBusinessAlignment },
    { name: "Police Reform", score: stats.avgPoliceAlignment }
  ];
  const topIssue = issues.reduce((max, issue) => issue.score > max.score ? issue : max);
  
  return {
    boro_cd,
    topIssue: topIssue.name,
    topScore: topIssue.score,
    ...stats
  };
});
```
```js
const districtCentroids = districts.features.map(feature => {
  const centroid = d3.geoCentroid(feature);
  const districtData = districtTopIssues.find(d => d.boro_cd === feature.properties.BoroCD);
  
  return {
    boro_cd: feature.properties.BoroCD,
    longitude: centroid[0],
    latitude: centroid[1],
    topIssue: districtData?.topIssue || "Unknown",
    topScore: districtData?.topScore || 0
  };
}).filter(d => d.topIssue !== "Unknown");
```

```js
const surveyStats = {
  heard: survey.filter(d => d.heard_of_candidate === "Yes").length,
  total: survey.length,
  voted: survey.filter(d => d.voted === "Yes").length,
  votedForUs: survey.filter(d => d.voted_for === "Candidate").length,
  avgHousingAlignment: d3.mean(survey, d => d.affordable_housing_alignment),
  avgTransitAlignment: d3.mean(survey, d => d.public_transit_alignment),
  avgChildcareAlignment: d3.mean(survey, d => d.childcare_support_alignment),
  avgBusinessAlignment: d3.mean(survey, d => d.small_business_tax_alignment),
  avgPoliceAlignment: d3.mean(survey, d => d.police_reform_alignment)
};

const issueData = [
  { issue: "Affordable Housing", alignment: surveyStats.avgHousingAlignment },
  { issue: "Public Transit", alignment: surveyStats.avgTransitAlignment },
  { issue: "Childcare Support", alignment: surveyStats.avgChildcareAlignment },
  { issue: "Small Business", alignment: surveyStats.avgBusinessAlignment },
  { issue: "Police Reform", alignment: surveyStats.avgPoliceAlignment }
].sort((a, b) => b.alignment - a.alignment);
```

---


### *What The Voters Want:* 
## Issue Alignment

```js
Plot.plot({
  width: 1000,
  height: 400,
  marginLeft: 150,
  x: {
    label: "Average Agreement (1=Strongly Disagree, 5=Strongly Agree)",
    domain: [0, 4]
  },
  y: {
    label: "Policy Issue",
    padding: 0.2
  },
  marks: [
    Plot.barX(issueData, {
      y: "issue",
      x: "alignment",
      fill: d =>
        d.alignment > 3.5 ? "#0f6a16ff" :
        d.alignment > 2.5 ? "#0f6a16ff" :
        "#0f6a16ff",
      title: d => `${d.issue}: ${d.alignment.toFixed(2)}/5.0`,
      sort: { y: "x", reverse: true }
    }),

    
    Plot.ruleX([3.5], {
      stroke: "#1631a89f",
      strokeWidth: 2,
      strokeDasharray: "4,4"
    }),


    Plot.text(
      [{ x: 3.0, y: issueData[0].issue, label: "Strong Support → " }],
      {
        x: "x",
        y: "y",
        text: "label",
        dy: -7,
        fill: "#002fffff",
        fontSize: 25
      }
    )
  ]
})

```

#### *Policy Analysis:* 
- Survey respondents showed strongest support for our affordable housing and public transit positions (both above 3.5/5). 
- Police reform was more divisive, suggesting we need clearer messaging on this issue for the next campaign. 
- ${((surveyStats.heard / surveyStats.total) * 100).toFixed(0)}% of respondents had heard of our candidate. 


---

<div style="text-align: center; margin: 20px auto; max-width: 900px;">
  <h3>Top Issues by District</h3>
  <p><em>Which policies resonate most in each community?</em></p>
</div>

```js
Plot.plot({
  width: 1000,
  height: 700,
  projection: {
    domain: districts,
    type: "mercator",
  },
  color: {
    type: "categorical",
    domain: ["Affordable Housing", "Public Transit", "Childcare Support", "Small Business", "Police Reform"],
    range: ["#8b0000", "#ffd700", "#0f6a16ff", "#1631a89f", "#ff6b35"],
    legend: true,
    label: "Most Popular Policy Issue"
  },
  marks: [
    Plot.geo(districts, {
      fill: "rgba(255, 249, 230, 0.3)",
      stroke: "#8b0000",
      strokeWidth: 2
    }),
    Plot.dot(districtCentroids, {
      x: "longitude",
      y: "latitude",
      fill: "topIssue",
      r: 10,
      stroke: "#8b0000",
      strokeWidth: 2.5,
      title: d => `District ${d.boro_cd}
Top Issue: ${d.topIssue}
Support Level: ${d.topScore.toFixed(2)}/5.0`
    })
  ]
})

```


  #### *Geographic Policy Insights:*
  This map reveals which policy issues resonate most strongly in each district based on survey responses. 
    Each colored dot represents the highest-rated policy issue for that district.
  
  *Key Finding*: Different communities prioritize different issues. Understanding these local preferences is crucial for targeted messaging in future campaigns.

<div class="paragraph">
  <h1>LAB 3: Mayoral Mystery | Most Likely to Succeed Edition</h1>
  <div class="yearbook-subheader">
    by Madison Watkins
  </div>
</div>