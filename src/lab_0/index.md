---
title: "Lab 0: Getting Started"
toc: true
---

# Welcome to Lab 0: Getting Started for A Second Time !

## Introduction
This dashboard introduced me to the basic structure of an Observable Framework project.  
You’ll see examples of Markdown, HTML, and JavaScript all working together.

---

### Overview
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

### Example List
<ul>
  <li>First item</li>
  <li>Second item</li>
  <li>Third item</li>
</ul>

---

### Example Image
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
