---
title: "Part 3: Creating an overview"
date: 2023-01-17
tags:
  - Focus

---

<h1>Overview</h1>

Here we will be creating our overview. 

What would we like to see? We would probably want to see our name, our account number, and our balance, together with all transactions below this.

Something along the lines of this:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/overview_look.png" alt="">

We will split this up in three parts, the account information part, the Transfer button, and the Transaction table.

We now have a folder structure as follows:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/hierarchy.png" alt="">

Our application will consist of multiple subcomponents, one of them being our Overview.

We will create a new ```subcomponents``` folder in the ```src``` folder which will contain the code for our Overview. This is good practice, keeping the code for several systems separate. In there we will create a new folder called overview, which will house our ```Overview.js``` code. Anytime you want to debug or change some code, you know the folder it will be in. 

The folder structure is then as follows:

```
src 
 - subcomponents
   - overview
     - Overview.js
   - other subcomponents to be added
```

We open our Overview.js file, which for now it a clean sheet.

We add the following code:

```javascript
import {Component} from 'react'

class Overview extends Component {
  render () {
    return (
      <>
        <AccountInformation/>
        <TransactionTable/>
      </>
    )
  }
}

export default Overview
```
We now have our boilerplate code that will contain the rest of our functions.

We now add the placeholders for our two lower level components:

```javascript
class Overview extends Component {
  render () {
    return (
      <>
        <AccountInformation/>
        <TransactionTable/>
      </>
    )
  }
}

class AccountInformation extends Component {
  render () {
    return (
      <>
      </>
    )
  }
}

class TransactionTable extends Component {
  render () {
    return (
      <>
      </>
    )
  }
} 

export default Overview
```
We will now start filling in our Components. 

We will use the ```Table``` component from ```react-bootstrap```.

```react-bootstrap``` is a React extension of the ```bootstrap``` framework, which is a web framework that allows for easy re-use of prebuilt components. We install it by running  

```
npm install react-bootstrap
```
in the terminal. We then add the following code:

```javascript
class AccountInformation extends Component {
  render () {
    return (
      <Table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Account Number</th>
            <th>Balance</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Mr. My Name</td>
            <td>0123456789</td>
            <td>100</td>
          </tr>
        </tbody>
      </Table>
    )
  }
}
```
The Table component makes use of the header and the body (thead and tbody). In there for each row (tr) you add an element for the header (th) and body (td).
We then move back to ```App.js``` and add 
```
import Overview from './subcomponents/overview/Overview'
```
add the top, so that the App can use the Overview component. We also add our imported Overview component and put it in the App function. Our ```App.js```
will then look like this:

{% capture notice-2 %}
frontend/src/App.js
```javascript
import './App.css';
import Overview from './subcomponents/overview/Overview'

function App() {
  return (
    <>
      <Overview/>
    </>
  );
}

export default App;
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

This also ensures that by making changes in ```Overview.js``` and saving, our entire application is re-rendered.

Checking our application, we see the following:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/account_info.png" alt="">

Quite ugly, but it works!

Let's also fill in the Transaction table. The information we would like to see is how many transactions there have been, from and to which accounts the money has been transferred, the amount, the currency, the category, and on which date the transaction occurred. Let's fill this in, with a placeholder for one transaction:


{% capture notice-3 %}
frontend/src/subcomponents/overview/Overview.js
```javascript
...

class TransactionTable extends Component {
  render () {
    return (
      <>
        <Table>
          <thead>
            <tr>
              <th>Transaction ID</th>
              <th>From Account</th>
              <th>To Account</th>
              <th>Amount</th>
              <th>Currency</th>
              <th>Category</th>
              <th>Date</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>1</td>
              <td>123456789</td>
              <td>987654321</td>
              <td>10</td>
              <td>EUR</td>
              <td>Pleasure</td>
              <td>18-01-2023</td>
           </tr>
         </tbody>
      </Table>
      </>
    )
  }
} 

...
```
{% endcapture %}

<div class="notice">{{ notice-3 | markdownify }}</div>

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/account_info_tx_table.png" alt="">

Again quite ugly, so let's change this. 

We install ```bootstrap``` by running
```
npm install bootstrap
```
and make the following modifications to the ```index.js``` file:

{% capture notice-3 %}
frontend/src/index.js
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import '../node_modules/bootstrap/dist/css/bootstrap.min.css'

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

reportWebVitals();
```
{% endcapture %}

<div class="notice">{{ notice-3 | markdownify }}</div>

By importing the bootstrap template, we can make use of improved graphics. Our table suddenly looks much nicer:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/overview_bootstrap.png" alt="">

Still, everything is very white. By making a small modification to the code there is more contrast:


{% capture notice-3 %}
frontend/src/subcomponents/overview/Overview.js
```javascript
...

class AccountInformation extends Component {
  render () {
    return (
      <Table striped bordered hover>

... 

class TransactionTable extends Component {
  render () {
    return (
      <>
        <Table striped bordered hover>
```
{% endcapture %}

<div class="notice">{{ notice-3 | markdownify }}</div>

resulting in:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/overview_bootstrap_gray.png" alt="">

Already looking much better!

Let's wrap it in a Card to give it a more natural look, with some distance to the edges:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...
import {Table, Card} from 'react-bootstrap'

class Overview extends Component {
  render () {
    return (
      <>
        <Card>
          <Card.Body>  
            <AccountInformation/>
          </Card.Body>
          <Card.Body>
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


<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/overview_card.png" alt="">

Looking quite nice, let's center it. 
