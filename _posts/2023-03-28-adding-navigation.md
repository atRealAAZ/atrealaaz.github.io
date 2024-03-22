---
title: "Part 5: Adding Navigation"
date: 2023-03-28
tags:
  - Focus

---

<h1>Adding a Navigation Bar</h1>

We now have two 'pages' so to speak. We need to be able switch between these pages depending on the actions the user takes. To make this possible we will add a navigation bar, as the interface through which a user determines which page to see. 

For that I create a new folder ```navigation``` in ```src``` in which I create a ```Navigation.js``` file which will contain the code.


{% capture notice-2 %}
frontend/src/navigation/Navigation.js
```javascript
import React, {Component} from 'react'
import {Navbar, Button} from 'react-bootstrap'

import './Navigation.css'

class Navigation extends Component {
  render () {
    return (
      <>
        <Navbar fixed = "top" className="justify-content-end">
          <Button 
            variant = "primary">Overview
          </Button>
        </Navbar>
      </>
    )
  }
}

export default Navigation
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

We import our necessary imports, including the Navbar prebuilt component from React bootstrap. We create a small Navigation component, containing the variables which is now fixed at the top and shown at the right side which will contain our Button that will navigate us to our Overview (to be implemented). 

We have also created a ```Navigation.css``` file that lives in ```frontend/src/navigation/Navigation.css``` that looks as follows:

{% capture notice-2 %}
frontend/src/navigation/Navigation.css
```css
.navbar {
    padding-top: 0rem;
    padding-bottom: 0rem;
}
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

This ensures that the button has no padding (space around it which I find ugly). 

Now to ensure we see it, we have to change some code in our Overview and TransferForm files:

{% capture notice-2 %}
frontend/src/main/Transfers.js
```javascript
...

import Navigation from './../navigation/Navigation'

class TransferPage extends Component {
  render () {
    return (
      <>
        <Navigation/>
        <Card>
          <Card.Body>
            <TransferForm/>
          ...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>


{% capture notice-2 %}
frontend/src/main/Overview.js
```javascript
...

import Navigation from './../navigation/Navigation'

class Overview extends Component {
  render () {
    return (
      <>
        <Navigation/>
        <Card>
          <Card.Body>  
            <AccountInformation/>
          ...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

It will look as follows:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/overview_navbar.PNG" alt="">

Great!










