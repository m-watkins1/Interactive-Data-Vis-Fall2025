
# Welcome to Lab 0: 
## An Interactive Data Visualization dashboard created by Madison Watkins for DATA 73200 at The Graduate Center, FALL 2025

Here Watkins will attempt to complete the Lab 0 assignment. Below you will explore a developed dashboard using Markdown, HTML, and JS.

# Introduction
This dashboard introduced me to the basic structure of an Observable Framework project.  
You’ll see examples of Markdown, HTML, and JavaScript all working together.

---

# Ex. Table
<table>
  <thead>
    <tr>
      <th>Feature</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>SECOND</td>
      <td>This is one sentence.</td>
    </tr>
    <tr>
      <td>THIRD</td>
      <td>This is a sentence about something else.</td>
    </tr>
    <tr>
      <td>FOURTH</td>
      <td>And then another one.</td>
    </tr>
  </tbody>
</table>

---

# TOP Restaurants | Ex. List
<ul>
  <li>Seaweed Handroll Bar</li>
  <li>Takos Al Pastor</li>
  <li>Maggiorata</li>
</ul>

---

# FAVORITE MEAL | Ex. Image
<img src="https://static.spotapps.co/spots/52/1ef0dac8544455b64a5638ad44218e/full"  />

---

# Favorite Restaurant | Text Input

Type the name of your favorite restaurant below 🍽️

```js
const name = view(
  Inputs.text({
    label: "Restaurant Name",
    placeholder: " ",
    value: "Anonymous"
  })
);

name

---
title: "Lab 0: Getting Started"
toc: true
---
