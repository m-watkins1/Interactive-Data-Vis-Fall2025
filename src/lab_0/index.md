<<<<<<< HEAD
# Welcome to Lab 0: Getting Started for A Second, Third, Fourth, Fifth Time !

## Introduction
This dashboard introduced me to the basic structure of an Observable Framework project.  
You’ll see examples of Markdown, HTML, and JavaScript all working together.

---

# Overview
Below is an example of an HTML table and list .

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

# Example List
<ul>
  <li>First item</li>
  <li>Second item</li>
  <li>Third item</li>
</ul>

---

# Example Image
<img src="https://static.spotapps.co/spots/52/1ef0dac8544455b64a5638ad44218e/full"  />

---

# Favorite Restaurant | Text Input

Type the name of your favorite restaurant below 🍽️

```js
const name = view(
  Inputs.text({
    label: "Restaurant Name",
    placeholder: " ",
    value: " ...."
  })
);

name
=======
# Welcome to Lab 0
## An Interactive Data Visualization dashboard created by Madison Watkins for DATA 73200 at The Graduate Center, FALL 2025
### Here Watkins will attempt to complete the Lab 0 assignment. 
### Below you will explore a developed dashboard using Markdown, HTML, and JS.

## Cities and Populations Table
<table id="city-table">
  <tr>
    <th>City</th>
    <th>Population (millions)</th>
  </tr>
  <tr>
    <td>Brooklyn</td>
    <td>2.6</td>
  </tr>
  <tr>
    <td>Los Angeles</td>
    <td>3.8</td>
  </tr>
  <tr>
    <td>Chicago</td>
    <td>2.7</td>
  </tr>
  <tr>
    <td>Houston</td>
    <td>2.3</td>
  </tr>
</table>

---

## Filter Cities by Minimum Population
<p>Minimum population: <span id="min-pop">0</span> million</p>
<input type="range" min="0" max="5" step="0.1" value="0" id="pop-slider">
<ul id="city-list"></ul>

---

## Los Angeles 
<img src="https://la.urbanize.city/sites/default/files/styles/2018_article_image_1140x538/public/LA%20Pano%20West.JPG?itok=PRcSTLiS" alt="Los Angeles panorama" width="600">

---

<script>
// Grab table rows (skip header)
const rows = document.querySelectorAll("#city-table tr:not(:first-child)");
const cityList = document.getElementById("city-list");
const slider = document.getElementById("pop-slider");
const minPopText = document.getElementById("min-pop");

// Function to update the list based on slider
function updateList() {
  const minPop = parseFloat(slider.value);
  minPopText.textContent = minPop;

  // Clear current list
  cityList.innerHTML = '';

  // Add cities meeting the minimum population
  rows.forEach(row => {
    const city = row.children[0].textContent;
    const population = parseFloat(row.children[1].textContent);
    if (population >= minPop) {
      const li = document.createElement("li");
      li.textContent = `${city} — ${population} million`;
      cityList.appendChild(li);
    }
  });
}

// Initialize list
updateList();

// Update list when slider changes
slider.addEventListener("input", updateList);
</script>

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).
>>>>>>> 602f760 (Commit local changes and add dist build for GitHub Pages)
