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
    <div className = "app">
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