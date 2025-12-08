---
title: "Lab 3: Mayoral Mystery"
toc: false
---

---

## <span style="font-family: Times New Roman, serif;"> LAB 3: Mayoral Mystery | Most Likely to Succeed Edition</span>
### <span style="font-family: Times New Roman, serif;">*by Madison Watkins*</span>

---

<style>
  body {
    background: linear-gradient(135deg, #1631a89f 0%, #1631a89f 50%, #1631a89f 100%);
    font-family: 'Comic Sans MS', cursive, sans-serif;
  }
  
  .yearbook-header {
    background: #8b0000;
    color: #ffd700;
    padding: 30px;
    text-align: center;
    border: 8px double #ffd700;
    margin: 20px 0;
    box-shadow: 0 8px 16px rgba(0,0,0,0.3);
  }
  
  .yearbook-header h1 {
    font-size: 3em;
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
  
  .superlatives {
    background: white;
    border: 5px solid #8b0000;
    padding: 25px;
    margin: 20px 0;
    box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  }
  
  .superlatives h2 {
    color: #8b0000;
    text-align: center;
    font-size: 2em;
    border-bottom: 3px dotted #ffd700;
    padding-bottom: 10px;
  }
  
  .stats-ribbon {
    background: #8b0000;
    color: #ffd700;
    padding: 20px;
    text-align: center;
    border-radius: 15px;
    margin: 20px 0;
    border: 4px solid #ffd700;
  }
  
  .stats-ribbon strong {
    font-size: 1.5em;
    text-shadow: 2px 2px #000;
  }
  
  .section-title {
    background: linear-gradient(90deg, #8b0000, #b22222);
    color: #ffd700;
    padding: 15px;
    font-size: 1.8em;
    text-align: center;
    border: 4px solid #ffd700;
    margin: 30px 0 20px 0;
    box-shadow: 0 4px 6px rgba(0,0,0,0.2);
    font-weight: bold;
  }
  
  .analysis-box {
    background: #fff9e6;
    border-left: 6px solid #8b0000;
    padding: 20px;
    margin: 20px 0;
    font-style: italic;
    box-shadow: 3px 3px 10px rgba(0,0,0,0.1);
  }
  
  .game-plan {
    background: white;
    border: 6px double #8b0000;
    padding: 25px;
    margin: 30px 0;
    box-shadow: 0 6px 12px rgba(0,0,0,0.2);
  }
  
  .game-plan h2 {
    color: #8b0000;
    text-align: center;
    font-size: 2.2em;
    margin-bottom: 20px;
  }
  
  .game-plan h3 {
    color: #b22222;
    font-size: 1.5em;
    margin-top: 20px;
  }
  
  .star-emoji {
    color: #ffd700;
    font-size: 1.5em;
  }
</style>
<div class="yearbook-header">
  <h1>🏆 NYC MAYORAL RACE 2024 🏆</h1>
  <div class="yearbook-subheader">
    "Most Likely to Be Mayor" - Official Campaign Yearbook
  </div>
</div>

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


<div class="section-title">

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

#### *Analysis:* 
The candidate performed strongly overall, winning ${districtsWon} out of ${totalDistricts} districts (${((districtsWon/totalDistricts)*100).toFixed(1)}%) with a ${overallWinPercentage}% vote share. The map shows the ranging district-level performance, with a darker green indicating districts where the candidate won a higher percentage of the voter share. 

#### *Summary*
**Vote Share:** ${overallWinPercentage}% |
**Districts Won:** ${districtsWon} out of ${totalDistricts} |
**Total Votes:** ${candidateVotes.toLocaleString()} votes


---

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

    // Threshold line
    Plot.ruleX([3.5], {
      stroke: "#1631a89f",
      strokeWidth: 2,
      strokeDasharray: "4,4"
    }),

    // Annotation nudged so it's visibly tied to the threshold region
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

#### *Policy Strengths:* 
- Survey respondents showed strongest support for our affordable housing and public transit positions (both above 3.5/5). 
- Police reform was more divisive, suggesting we need clearer messaging on this issue for the next campaign. 
- ${((surveyStats.heard / surveyStats.total) * 100).toFixed(0)}% of respondents had heard of our candidate. 


---

### *Next campaign:* 

## Campaign Trail

```js
Plot.plot({
  width: 1000,
  height: 500,
  marginLeft: 60,
  grid: true,
  x: {
    label: "Number of Campaign Events →",
    domain: [0, 7],
    type: "linear"
  },
  y: {
    label: "↑ Vote Share (%)",
    domain: [0, 100],
    type: "linear"
  },
  color: {
    legend: true,
    label: "Income Level"
  },
  marks: [
    // Main scatter plot
    Plot.dot(
      campaignEffectiveness.map(d => ({
        ...d,
        events: +d.events,
        win_percentage: +d.win_percentage,
        attendance: +d.attendance
      })),
      {
        x: "events",
        y: "win_percentage",
        fill: "income_category",

        r: d => Math.sqrt(d.attendance) * 0.5,

        title: d =>
`District ${d.boro_cd}
Events: ${d.events}
Vote Share: ${d.win_percentage.toFixed(1)}%
Attendance: ${d.attendance}
Income: ${d.income_category}`
      }
    ),

    // Regression line for events → vote share
    Plot.linearRegressionY(
      campaignEffectiveness,
      {
        x: "events",
        y: "win_percentage",
        stroke: "#1631a89f",
        strokeWidth: 3,
        strokeDasharray: "4,4"
      }
    ),

    // Optional: a second trend showing attendance → popularity
    Plot.linearRegressionY(
      campaignEffectiveness,
      {
        x: "attendance",
        y: "win_percentage",
        stroke: "#1631a89f",
        strokeWidth: 2,
        strokeDasharray: "2,5",
        // Put it behind so it doesn't overwhelm
        opacity: 0.4
      }
    )
  ]
})

```

*Campaign Strategy:* There's a clear positive relationship between the number of events we held and our vote share! 
- The dashed line shows the trend: more events = better results. 
- Bubble size represents attendance.

Our bigger events in middle-income areas drove the strongest turnout.
