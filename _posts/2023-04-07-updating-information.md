---
title: "Part 10: Updating Information"
date: 2023-04-07
tags:
  - Focus

---

<h1>Updating our Overview</h1>

We can now login and transfer money. However, our overview isn't updated. So let's change this. 

We add another state object to our initialState:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...

const initialAcctDetails = {
  username: '',
  accountNumber: '',
  balance: 0
}

const initialState = {
  route: 'login',
  loginDetails: initialLoginDetails,
  txDetails: initialTxDetails,
  acctDetails: initialAcctDetails
}    

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We further change the code slightly in our onAuthentication method, and pass _state_ to our Overview:

{% capture notice-2 %}
frontend/src/App.js
```javascript

class App extends Component {
  ...

  onAuthentication = async (route) => {
    ...
    let success = response['success']
    let message = response['message']
    let result = response['result']
    if (success) {
      this.setState({
        ...this.state,
        route: 'overview',
        acctDetails: result['account_details']
      })
    }
    alert(message)

  render() {
    ...

        ?
          <Overview
          state = {this.state}
          onRouteChange = {this.onRouteChange}
          />
        :

...
  

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We then inherit it down to the relevant component that shows it:

{% capture notice-2 %}
frontend/src/subcomponents/main/Overview.js
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
            <AccountInformation
            state = {this.props.state}
            />

...

class AccountInformation extends Component {
  render () {
    let acctInfo = this.props.state.acctDetails
    return (
      <Table striped bordered hover>
        <thead>
          <tr>
            <th>Name</th>
            <th>Account Number</th>
            <th>Balance</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>{acctInfo.username}</td>
            <td>{acctInfo.accountNumber}</td>
            <td>{acctInfo.balance}</td>
          </tr>
        </tbody>
      </Table>
    )
  }
}
  
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

and in the AccountInformation table, we replace the previous placeholders by the actual values. Now, everytime we login, our information is fetched from the database and shown in our overview! 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_overview/updated_acct_info.PNG" alt="">

Nice!

<h2>Updating our Transaction table</h2>

Our account information is shown, but not the transactions that are made. Let's change that.

We go to our main/routes.py file:

{% capture notice-2 %}
backend/bank/main/routes.py
```javascript
...

def fetch_transactions(account_number):
    tx_query = Transaction.query.filter_by(
        to_account = account_number
    ).union(
        Transaction.query.filter_by(
        from_account = account_number
        )
    ).order_by(
        Transaction.id.desc()
    )
    txs = tx_query.all()
    # paginated_tx = get_paginated_object(tx_query)
    if len(tx_query.all()) == 0:
        return False, []
    else:
        return True, txs

def get_overview(user):
    tx_exists, txs = fetch_transactions(user.account_number)
    return gen_result_dict(
        account_details = gen_result_dict(
            username = user.username,
            accountNumber = user.account_number,
            balance = user.balance,
        ),
        tx_table = gen_result_dict(
            tx_exists = tx_exists, 
            txs = txs
        )
    )

...
  
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

What happens here is that BLA EXPLAIN

We add another object to our state:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...


const initialTxTable = {
  exists: false,
  tx: []
}

const initialState = {
  route: 'login',
  loginDetails: initialLoginDetails,
  txDetails: initialTxDetails,
  acctDetails: initialAcctDetails,
  txTable: initialTxTable
}

...
  
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

and in our onAuthentication method

{% capture notice-2 %}
frontend/src/App.js
```javascript
...


class App extends Component {

  onAuthentication = async (route) => {
    ...

    if (success) {
      this.setState({
        ...this.state,
        route: 'overview',
        acctDetails: result['account_details'],
        txTable: result['tx_table']
      })
    }

    ...

...
  
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>




