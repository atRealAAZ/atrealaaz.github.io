---
title: "Part 6: Connecting the Pages"
date: 2023-03-28
tags:
  - Focus

---


<h1>Adding state</h1>

So far we just had a static website. However, with the building of the TransferPage and Overview components we have several different screens that we would like to show the user, depending on their actions on screen. 
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
we click on that button. In practice this means that the page we are on should be tracked, and the page we see should be changed to Overview whenever we click this button. We implement this in the following way, by adding a function in our ```App.js``` file to our App component:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...

class App extends Component {

  constructor(props) {
    super()
    this.state = initialState
  }

  onRouteChange = (dest) => {
    this.setState({
      route: dest
    })
  }

  render() {
    return (
      <div class = "app">
        {this.state.route === 'overview' 
        ?
          <Overview/>
        :
          <TransferPage/>
        }
      </div>
    )
  }
}

export default App
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

This ```onRouteChange``` function is now connected to our App component, and every time this function is invoked it changes the ```route``` variable of its ```state``` to the variable that is passed to it. We also pass a conditional statement to our render section, where everytime the component is re-rendered it checks our state; if the ```route``` property of our state evaluates to ```overview``` we render our Overview component, elsewise the TransferPage component. 

How do we change the ```route``` then? Very good question! Remember that we have a Button in our Navigation pane? We will tell it to change the state everytime we click on it. We do this by adding the following code to our App component: 


{% capture notice-2 %}
frontend/src/App.js
```javascript
    ...
      {this.state.route === 'overview' 
        ?
          <Overview
          onRouteChange = {this.onRouteChange}
          />
        :
          <TransferPage
          onRouteChange = {this.onRouteChange}
          />
      }
    ...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

We then change ```Transfers.js``` in the following way:

{% capture notice-2 %}
frontend/src/main/Transfers.js
```javascript
...

class TransferPage extends Component {
  render () {
    return (
      <>
        <Navigation
        onRouteChange = {this.props.onRouteChange}
        />
    ...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

We then add code to ```Navigation.js```:


{% capture notice-2 %}
frontend/src/navigation/Navigation.js
```javascript
...

class Navigation extends Component {
  render () {
    return (
      <>
        <Navbar fixed = "top" className="justify-content-end">
          <Button 
            variant = "primary"
            onClick = {() => {this.props.onRouteChange('overview')}}>Overview
          </Button>
        </Navbar>
      </>
    )
  }
}

...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

What happened in these 3 steps is that our Overview and TransferPage components now have a property by the name of ```onRouteChange``` which calls the onRouteChange function of our App component. This property is then itself inherited by an even lower-level component, the Button in our Navigation component. The Button has itself an onClick property, which means that when it is clicked, it will invoke the onRouteChange property of the Navigation (which is itself a property of the TransferPage component, etc.) This onRouteChange will then set the state of our App to the variable which is passesd on to it, which in this case is ```overview```. This is in practice the essence of React, where you can have methods that can be inherited by lower-level components change the state of higher-level components. 

Now, everytime we click on the Overview button in our navigation pane, the ```route``` property of our ```state``` property of our ```App``` component is changed to ```overview```, which will then render our ```Overview``` component. So every time we click the Overview button, we get to see the Overview page. Neat, no?

We now do the same for our Transfer button which lives in our Overview page, which should have similar functionality, transporting us to the Transfer page every time it is clicked:


{% capture notice-2 %}
frontend/src/main/Overview.js
```javascript
...

class Overview extends Component {
  render () {
    return (
      <>
        <Navigation
        onRouteChange = {this.props.onRouteChange}
        />
        <Card>
          <Card.Body>  
            <AccountInformation/>
          </Card.Body>
          <Card.Body>
            <Button 
              variant="primary"
              onClick = {() => {this.props.onRouteChange('transfer')}}>Transfer 
            </Button>
            <TransactionTable/>
          </Card.Body>
        </Card>
      </>
    )
  }
}

...
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

Now, everytime the Transfer button in the Overview page is clicked, you are transported to the Transfer page, and everytime you click the Overview button, you are transported to the Overview page! Try it!

Well done, you now have a dynamic web app that can change based on user behaviour!















