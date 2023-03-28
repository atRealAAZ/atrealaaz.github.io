---
title: "Part 4: Creating a Transfer Form"
date: 2023-01-17
tags:
  - Focus

---

<h1>Overview</h1>

The overview we built in the previous section is nice, showing us the information belonging to that account. It also shows us a table where all the transactions belonging to that account are listed.
However, for it to list transactions, we need to be able to make some!  

So what would our transfer functionality have to look like? We of course would have to be able to enter the account number we are sending the money to, together with the amount, and currency. Something along the lines of this:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_transfer/transfer_form.png" alt="">

<h1>Creating the Transfer Form</h1>

We create a ```Transfers.js``` in our ```main``` folder, providing us the following structure:

```
src 
 - subcomponents
   - main
     - Overview.js
     - Transfers.js
```

In this ```Transfers.js``` we populate it with our template code:


{% capture notice-2 %}
frontend/src/main/Transfers.js
```javascript
import {Component} from 'react'

class TransferPage extends Component {
    render () {
      return (
        <>
        </>
      )
    }
  }

export default TransferPage
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

We will use our newly-found ```Card``` components, to fill it up, together with a ```Form``` component.

[EXPLAIN FORM]

Modifying our code to import these changes, it looks like this:

{% capture notice-2 %}
frontend/src/main/Transfers.js
```javascript
import {Component} from 'react'

import {Card, Form} from 'react-bootstrap'

class TransferPage extends Component {
  render () {
    return (
      <>
        <Card>
          <Card.Body>
            <Form.Group className="mb-3">
              <Form.Label>To Account</Form.Label>
              <Form.Control/>
            </Form.Group>
            <Form.Group className="mb-3">
              <Form.Label>Amount</Form.Label>
              <Form.Control/>
            </Form.Group>
            <Form.Group className="mb-3">
              <Form.Label>Currency</Form.Label>
              <Form.Control/>
            </Form.Group>
          </Card.Body>
        </Card>
      </>
    )
  }
}

export default TransferPage
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

[[EXPLAIN mb-3]] The ```className="mb-3"``` ensures we get some more space.

This is nice, but we also need to add a button that initiates the transfer. 

We will add a button, and adapt the code slightly, because the code we wrote above is the form containing the information, and a separate conceptual part is the button, which are both shown on the page that contains the transfer functionality:

{% capture notice-2 %}
frontend/src/main/Transfers.js
```javascript
import {Component} from 'react'

import {Card, Form, Button} from 'react-bootstrap'

class TransferPage extends Component {
  render () {
    return (
      <>
        <Card>
          <Card.Body>
            <TransferForm/>
            <Button 
            variant="primary">Transfer 
            </Button>
          </Card.Body>
        </Card>
      </>
      )
  }
}

class TransferForm extends Component {
  render () {
    return (
      <>
      <Card>
          <Card.Body>
        <Form.Group className="mb-3">
          <Form.Label>To Account</Form.Label>
          <Form.Control/>
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Amount</Form.Label>
          <Form.Control/>
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Currency</Form.Label>
          <Form.Control/>
        </Form.Group>  
        </Card.Body>
        </Card>
      </>
    )
  }
}

export default TransferPage
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

We import the Button component, and add it, and change the layout a bit. The "primary" part in the button makes it blue, because I'm a bit sick of all the white/grey.

Going back to App.js, we change the code a bit so that we can see what our TransferPage looks like:

{% capture notice-2 %}
frontend/src/App.js
```javascript
import '../node_modules/bootstrap/dist/css/bootstrap.min.css';

import './App.css';
import Overview from './subcomponents/main/Overview'
import TransferPage from './subcomponents/main/Transfers'

function App() {
  return (
    <div class = "app">
      <TransferPage/>
    </div>
  );
}

export default App;
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

Saving and going to ```localhost:3000``` gives us: 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_transfer/transfer_form_finished.png" alt="">

Nice!




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

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/overview_navbar.png" alt="">

Great!






<h1>Adding state</h1>

So far we just had a static website. However, with the building of the TransferPage component we have several different screens that we would like to show the user, depending on their actions on screen. 
How can we make this possible? Why, great you ask, with <i>state</i>!

<h2>State</h2>

State in React is the functionality to save and show your webapp with a different configuration. This different configuration depends on the actions the user has taken. Every time the user changes something
on the website this new state is saved and the resulting webapp is rendered in a new way based on this configuration. 

We first add our imports from React, since we need to be able to use the Component component:

{% capture notice-2 %}
frontend/src/App.js
```javascript
import React, {Component} from 'react'

...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

We then rewrite our App function to a Component, so we can save state:

{% capture notice-2 %}
frontend/src/App.js
```javascript

class App extends Component {
  render() {
    return (
      <div class = "app">
        <TransferPage/>
      </div>
    )
  }
}

...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

We will now initialize state in this App Component by adding the following code snippet:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...

const initialState = {
  route: 'overview'
}

class App extends Component {

  constructor(props) {
    super()
    this.state = initialState
  }

...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

In this way the App Component is initialized to have state, furthermore at creation an initial state is added as we have defined above. This way we can later change its state depending on users' actions.

What we want to happen is that whenever we click on the Overview button in our Navigation pane we should be transported to our Overview page. So what we want to happen is that we want to render the Overview page whenever
we click on that button. In practice this means that the page we are on should be tracked, and the page we see should be changed to Overview whenever we click this button. We implement this in the following way:






export default App;








