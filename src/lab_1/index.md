<!--
---
title: "Lab 1: Passing Pollinators"
toc: true
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).

```js
const text = view(Inputs.text())
```
This is the value of text: ${text}

```js
Plot.plot({
    width: 300,
    height: 200,
    marks: [
        Plot.frame(),
        Plot.text(["text"], {framanchor: "middle", rotate: 90})
    ]
})
```
```js
// view(aapl)
Inputs.table(aapl)
```

```js
Plot.plot({
    height: 200,
    y: {
        grid: true
    },
    marks: [
        Plot.frame(),
        Plot.line(aapl, { 
            x: "Date", 
            y: d => d["Close"] + 100, 
            stroke:"pink", 
            strokeWidth:20
         }),
        Plot.dot(aapl,{ x: "Date", y: "Close", fill: "white", r: 1, tip: true} ),
        Plot.ruleY([0,50,100]),
        // Plot.ruleY([100])
        // Plot.ruleX(new Date("2015-01-01"))
        // Plot.line(goog, { x: })
    ]
})
``` -->

## LAB 1: Pollinators

```js
const pollinator = FileAttachment("./data/pollinator_activity_data.csv").csv()
```

```js
Inputs.table(pollinator)
```

<!--```js
Plot.plot({
    height: 200,
    marks:  [
        Plot.frame(),
        Plot.barY(pollinator, {
            x: "rowid",
            y: "bill_length_mm"
        } )
    ]
})
``` -->

```js
// #1 What is the body mass and wing span distribution of each pollinator species observed?
Plot.plot({
    title: "Body Mass vs Wing Span by Pollinator Species",
    height: 250,
    x: { label: "Average Body Mass (g)" },
    y: { label: "Average Wing Span (mm)" }, 
     color: { label: "Pollinator Species", legend: true },
    marks:  [
        Plot.frame(),
        Plot.dot(pollinator, {
            x: "avg_body_mass_g",
            y: "avg_wing_span_mm",
            fill: "pollinator_species",
            r: 5,
        })
    ]
})

```

```js
// #2 What is the ideal weather (conditions, temperature, etc) for pollinating?

Plot.plot({
    title: "Average Visit Count by Weather Condition",
    height: 250,
    x: { label: "Weather Condition" },
    y: { label: "Average Visit Count" },
    marks: [
    Plot.frame(),
    Plot.barY(
      pollinator,
      Plot.groupX(
        { y: "mean" },
        {
          x: "weather_condition",
          y: "visit_count",
          fill: "weather_condition"
        }
      )
    )
  ]
})
```

```js
//#3 Which flower has the most nectar production?
Plot.plot({
  title: "Average Nectar Production by Flower Species",
  height: 250,
  x: { label: "Average Nectar Production (μL)" },
  y: { label: "Flower Species" },
  marginLeft: 90,
  marks: [
    Plot.frame(),
    Plot.barX(
      pollinator,
      Plot.groupY(
        { x: "mean" },
        {
          x: "nectar_production",
          y: "flower_species",
          fill: "lightgreen"
        }
      )
    )
  ]
})
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Lab 1 Dashboard</title>
  <script type="module" src="https://cdn.jsdelivr.net/npm/@observablehq/plot@0.10/dist/plot.umd.min.js"></script>
</head>
<body>
  <div id="dashboard"></div>
  <script type="module">
    // Your Plot.plot code here
  </script>
</body>
</html>
