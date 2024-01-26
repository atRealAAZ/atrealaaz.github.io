---
title: "Part 11: Pagination"
date: 2023-04-10
tags:
  - Focus

---

<h1>Adding Pagination</h1>

All the functionality is there, but if you've been playing with it a bit you may have noticed something. The more transactions you send, your screen will fill up:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_overview/no_pag.PNG" alt="">

This is of course quite ugly, we'll have to change this. For this we'll add Pagination. Simply said, Pagination is when you cut up your big list, and show it in a user-friendly format.

We'll have to change some things. First we'll go to our Config.py file where we'll add a new variable to denote how many tx we will want to see at once.


{% capture notice-2 %}
backend/config.py
```python
...

class Config(object):
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or \
        'sqlite:///' + os.path.join(basedir, 'app.db')
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    SIGNUP_BONUS = {
        'active': True,
        'amount': 20
    }
    TX_PER_PAGE = 10  

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Great. We will further continue with the backend.

<h2>Pagination Backend</h2>

In our main/routes.py file we will add the following code:

{% capture notice-2 %}
backend/bank/main/routes.py
```python
...

def gen_pagination_dict(
    has_prev,
    items,
    has_next
):
    return {
        'has_prev': has_prev,
        'items': items,
        'has_next': has_next
    } 

def get_paginated_object(sqlalchemy_obj):
    paginates = []
    num_pages = sqlalchemy_obj.paginate(
        page = 1, 
        per_page = Config.TX_PER_PAGE
    ).pages
    for page_num in range(1, num_pages + 1):
        page = sqlalchemy_obj.paginate(
            page = page_num, 
            per_page = Config.TX_PER_PAGE
        )
        paginate = gen_pagination_dict(
            page.has_prev,
            [get_dict_from_object(tx) for tx in page.items], 
            page.has_next
        )
        paginates.append(paginate)
    return paginates

def fetch_transactions(account_number):
    ...
    txs_raw = tx_query.all()
    if len(txs_raw) == 0:
        return False, []
    else:
        paginated_txs = get_paginated_object(tx_query)
        return True, paginated_txs

def get_overview(user):
        ...
        tx_table = gen_result_dict(
            tx_exists = tx_exists, 
            txs = txs,
            page = 0
        )
    )

...

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

What happens here is that we split up our big list of dictionaries (transactions) into a list with pages. Each page is a dictionary which will contain three items: whether there is a next page, a previous page, and the list of transactions on that page. We make use of the SQLAlchemy functionality where we can paginate a query, where it will show us how many pages there are given our TX_PER_PAGE configuration. For each page it will then add the dictionary with the relevant information. Our result is a list with pages, where each page is a dictionary with has_prev (bool), a certain number of transactions (list), and has_next (bool). We will return this value to the frontend, where it can cycle through the pages. 

In our Overview we change:

{% capture notice-2 %}
frontend/src/subcomponents/main/Overview.js
```javascript
...

          <tbody>
          {txTable.tx_exists
          ?
            txTable.txs[txTable.page].items.map((tx) => (
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

...

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

So we now pick the nth element of the list (with n denoted by the page) and render the items of it (the transactions). 

We now see the top 10:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_overview/pag_no_buttons.PNG" alt="">

A good start, but we still need to be able to cycle through the pages. For that we have to add a new component, which we will do in our Overview.js file:

{% capture notice-2 %}
frontend/src/subcomponents/main/Overview.js
```javascript

class PaginationButtons extends Component {
  render () {
    let txTable = this.props.state.txTable
    return (
    <>
      {txTable.txs[txTable.page]['has_prev']
      ?
        <Button 
        variant="primary"
        >Previous
        </Button>
      : 
        null
      }
      {txTable.txs[txTable.page]['has_next']
      ?
        <Button 
        variant="primary"
        >Next
        </Button>
      :
       null
      }
    </>
    )
  }
}

...

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Here we have a new Component, which checks for that page that is being displayed whether there is a previous page, if there is it renders a button, else it doesn't show. The same goes for Next. Very nice, now we add that to our Overview:

{% capture notice-2 %}
frontend/src/subcomponents/main/Overview.js
```javascript
...

class TransactionTable extends Component {
  render () {
    let txTable = this.props.state.txTable
    return (
      <>
        <Table striped bordered hover>
         ...
        </Table>
        {txTable['tx_exists']
        ?
          <Pagination
          txTable = {txTable}
          onNavigatePagination = {this.props.onNavigatePagination}
          />
        :
          <>
          </>
        }
      </>
    )
  }
} 

...

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We added some logic, where Pagination will only show up if there are any transactions at all. Otherwise it is unnecessary of course. 

Nice, if you move back to the app you can now see a button in the bottom: 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_overview/pag_button_no_func.PNG" alt="">

It doesn't work yet thought. Clicking it does nothing Let's change that. For that we add a method to our App component:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...

  onNavigatePagination = (value) => {
    let page = this.state.txTable.page
    if (value === 'Next') {
      this.setState({
        txTable: {
          ...this.state.txTable,
          page: page + 1
        }
      })
    } else {
      this.setState({
        txTable: {
          ...this.state.txTable,
          page: page - 1
        }
      })
    } 
  }

...

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Every time the button is clicked, it will change the state depending on the value, it will either add 1 to the page if the value is next, or else subtract 1. So when 'Next' is clicked, it will add 1 to the page, meaning that the page after that is rendered (if there is one). Similar with Previous. 

And of course, all the components that use it have to intherit it:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...

class App extends Component {

  render() {
      ...
        {this.state.route === 'overview' 
        ?
          <Overview
          state = {this.state}
          onRouteChange = {this.onRouteChange}
          onNavigatePagination = {this.onNavigatePagination}
          />

...

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

and in Overview.js

{% capture notice-2 %}
frontend/src/subcomponents/main/Overview.js
```javascript
...

class Overview extends Component {
  render () {
    return (
      <>
            ...
            <TransactionTable
            state = {this.props.state}
            onNavigatePagination = {this.props.onNavigatePagination}
            />
            ...


class TransactionTable extends Component {
  render () {
        ...
          <Pagination
          txTable = {txTable}
          onNavigatePagination = {this.props.onNavigatePagination}
          />
        ...


class Pagination extends Component {
  render () {
    let txTable = this.props.txTable
    return (
    <>
      {txTable.txs[txTable.page]['has_prev']
      ?
        <Button 
        variant="primary"
        onClick = {() => this.props.onNavigatePagination('Previous')}
        >Previous
        </Button>
      : 
        null
      }
      {txTable.txs[txTable.page]['has_next']
      ?
        <Button 
        variant="primary"
        onClick = {() => this.props.onNavigatePagination('Next')}
        >Next
        </Button>
      :
       null
      }
    </>
    )
  }
}

...

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Great, pagination now works! You can toggle between the pages by clicking Next and Previous. Try it!

Well done! We have now built a bank!