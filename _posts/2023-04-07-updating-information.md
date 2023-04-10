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
```python
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
    txs_raw = tx_query.all()
    if len(txs_raw) == 0:
        return False, []
    else:
        txs = get_dicts(txs_raw)
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

What happens here is that we query the database for all transactions that were sent by the user, then we query the database for all the transactions that were received by the user (since we want both), and then we take the union of that, resulting in all our transactions we are interested in. However, it returns a list of Transaction objects, which cannot be sent to our frontend since it is not in JSON format. When we query it it looks like this: 

```
>>> Transaction.query.first().__dict__
{'_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x000001C3B82AA320>, 
'from_account': '1', 'to_account': '2', 'currency': 'EUR', 'amount': 1, 'id': 1, 'date': 'now'}
>>>
```
All the info in there we want, except the _sa_instance_state which is not relevant for us directly. 

For that we add some code to _utils.py_:

{% capture notice-2 %}
backend/bank/utils.py
```python
...

def get_dict_from_object(
    sqlalchemy_obj, 
    to_exclude = '_sa_instance_state'
):
    db_dict = sqlalchemy_obj.__dict__
    new_dict = {
        key: db_dict[key]
        for key in db_dict.keys()
        if key not in to_exclude
    }
    return new_dict

def get_dicts(query):
    return [
        get_dict_from_object(obj) for obj in query
    ]

...
  
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

In the _get_dict_from_object_ we take a SQLAlchemy object, and create dict copying all values into that new dict, skipping the ones from the to_exclude list. Since we have a list of objects, we need to do this for every object in the list. We then have our result at the backend. We move back to our frontend.

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

Every time we login all our transactions are now fetched, but the table is not updated. Let's change that. 

We go to _Overview.js_ where we have our TransactionTable inherit the state and we change our TransactionTable component:

{% capture notice-2 %}
frontend/src/subcomponents/main/Overview.js
```javascript
...

class Overview extends Component {
  render()
        ...
          <TransactionTable
            state = {this.props.state}
          />
        ...

...

class TransactionTable extends Component {
  render () {
    let txTable = this.props.state.txTable
    return (
      <>
        <Table striped bordered hover>
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
          {txTable.tx_exists
          ?
            txTable.txs.map((tx) => (
              <tr>
                <td>{tx.id}</td>
                <td>{tx.to_account}</td>
                <td>{tx.from_account}</td>
                <td>{tx.amount}</td>
                <td>{tx.currency}</td>
                <td>{tx.date}</td>
              </tr>  
              )
            )
          :
            <p>No transactions found!</p>
          }     
         </tbody>
      </Table>
      </>
    )
  }
} 

...
  
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

In there we remove our placeholder code. We first check if there are any transactions (tx_exists = true), if there aren't we display the text of _No transactions found!_. If there are transactions, we display one transaction per row using the elements from the JSON object. 

Nice!

Everything will now work. If you create two accounts using our Register functionality, and send a few transactions from one account to the other, it now looks like this: 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_overview/updated_tx_table.PNG" alt="">

Try it!

There is one minor thing that still needs to be fixed. If we send a transaction, and we go back to our overview, our table is not updated. This is because clicking on the 'Overview' button in Navigation merely moves us to the overview, but a new query on the database is not run with the updated numbers. We want this to happen everytime we go back to our Overview. 

We already have most of our logic in our register and login methods. I could change the existing methods, but since I want to keep it clean I'll just add another small method that just queries the database in the backend, while keeping the same frontend method (onAuthentication). We do this in 

{% capture notice-2 %}
backend/bank/main/routes.py
```python
...

@app.route('/get_overview_route', methods = ['GET', 'POST'])
def get_overview_route():
    message = request.get_json()
    _, username, _ = message.values()['loginDetails']
    user = db_query(User, 'username', username)[1]
    result = get_overview(user)
    return gen_result_dict(
        success = True, 
        result = result
    )
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We just get the username, query the database for the overview, and return the result. 

In our Navigation we change the route that is invoked:

{% capture notice-2 %}
frontend/src/subcomponents/navigation/Navigation.js
```javascript
...

class Navigation extends Component {
  render () {
    return (
      <>
        <Navbar fixed = "top" className="justify-content-end">
          <Button 
            variant = "primary"
            onClick = {() => {this.props.onAuthentication('get_overview_route')}}
            >Overview
          </Button>
        ...

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We now need to add that method to our Navigation component:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...
class App extends Component {

  render() {
          : this.state.route === 'transfer'
          ?
            <TransferPage
            onRouteChange = {this.onRouteChange}
            sendTransaction = {this.sendTransaction}
            onFormTextChange = {this.onFormTextChange}
            onAuthentication = {this.onAuthentication}

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>


{% capture notice-2 %}
frontend/src/subcomponents/main/Transfers.js
```javascript
...
class TransferPage extends Component {
  render () {
    return (
      <>
        <Navigation
        onRouteChange = {this.props.onRouteChange}
        onAuthentication = {this.props.onAuthentication}

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We also change one last thing in App.js, since our Authentication methods all had messages, but we don't want to show one if you just move to your overview, we have to account for that:


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
      if (route !== 'get_overview_route') {
        alert(message)
      }
    ...

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

This way we don't want to show a message if one goes to their own overview. Nice!

Everything works now! Well done, try it!






