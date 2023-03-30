---
title: "Part 7: Building the Transfer Functionality"
date: 2023-03-28
tags:
  - Focus

---

<h1>Building Transfer Functionality</h1>

We now have a frontend which changes at our command, but so far it's just an empty shell, a Chinese room. Our main functionality is absent.

Let's change that.

<h2>Restructuring</h2>
But before we do, let's change our backend folder structure a bit.

We will create a new folder called <i>bank</i> inside our <i>backend</i> folder. This folder will house all our code related to the app's functionality. Within this folder we create another folder called <i>main</i>. This will contain the main functionality of our banking application (versus other functionalities that we will see later). Since these folders contain scripts, we have to initialize them with an empty __init__.py file. Our <i>backend</i> folder structure then looks like this:

```
backend
 - venv
 - .flaskenv
 - backend.py
 - bank
  - __init__.py
  - main
   - __init__.py
```

This folder structure will help us in the future when the application will grow, giving us a folder structure with clear separation of concerns, increasing maintanability.

Since we have a new structure we also had to move some code around.

{% capture notice-2 %}
backend/backend.py
```python
from bank import app
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Our Flask application is imported in the backend file. For now it seems a bit pointless to have a file with just an import, but since we will add some functionality to this later, it is nice to keep the backend separate from the instantiation of the app. This means we will have to instantiate our app somewhere. We do this in the init file:

{% capture notice-2 %}
backend/bank/__init__.py
```python
from flask import Flask

app = Flask(__name__)

from bank.main import routes
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Normally, the __init__ file is empty, I could also have created a new file in the bank/main folder and instantiated it there, but since I already have the __init__ file anyway I might as well use it.

We also have to change our routes file:

{% capture notice-2 %}
backend/bank/main/routes.py
```python
from bank import app

@app.route('/')
def main():
    return {
        "message": "Well done!"
    }

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

So now we rewrote our old application to make use of our new structure. Verify it works by typing
```
flask run
```

Now that we rewrote our structure we can add our functionality, starting with the database.  

<h2>Database</h2>

For this we will use the <i>flask-sqlalchemy</i> package, which is a Flask wrapper around the <i>SQLAlchemy</i> package, which itself an ORM that allows you to interact with a database through code. 

```
(venv) $ pip install flask-sqlalchemy
```

We then also install <i>flask-migrate</i>, a Flask wrapper made by Miguel Grinberg around Alembic, a framework for database migrations. This is a very nice one, since in a production application your dataneeds may change, so having a robust way to address database migrations is great. 

```
(venv) $ pip install flask-migrate
```

To access these functionalities we change add some code to the initialization of our app:

{% capture notice-2 %}
backend/bank/__init__.py
```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

app = Flask(__name__)
db = SQLAlchemy(app)
migrate = Migrate(app, db)

from bank.main import routes
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We will now create a new file called _models.py_ in our _main_ folder:

{% capture notice-2 %}
backend/bank/main/models.py
```python

class Transaction(db.Model):
    id = db.Column(db.Integer, primary_key = True)
    from_account = db.Column(db.String(18))
    to_account = db.Column(db.String(18))
    amount = db.Column(db.Integer)
    currency = db.Column(db.String(3))
    date = db.Column(db.String(10))

    def __repr__(self):
        return '<Transaction {}>'.format(self.id)

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We have defined a Transaction class, which means on creation of the database a table will be created with the fields that are listed below it. We want a id of the transaction to be able to identify it, we need to save
who sent it and to whom (from_account and to_account), the amount of course, the currency and the date. Each of these columns needs to be created by the db.Column command, followed by the data type, and whether or not it is a primary key. The `__repr__` command allows us to print and query around it. 

Once we have done this, we modify our instantiation file to import the packages and instantiate our classes, as well as our main app:

{% capture notice-2 %}
backend/bank/__init__.py
```python
from flask import Flask
from config import Config
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

app = Flask(__name__)
app.config.from_object(Config)
db = SQLAlchemy(app)
migrate = Migrate(app, db)

from bank.main import routes, models
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

so we can make use of this model. 

We also create a _config.py_ file in our _backend_ directory to allow us to easily modify our app according to specifications, with all changes just having to be done in one file:

{% capture notice-2 %}
backend/config.py
```python
import os
from dotenv import load_dotenv

basedir = os.path.abspath(os.path.dirname(__file__))
load_dotenv(os.path.join(basedir, '.env'))

class Config(object):
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or \
        'sqlite:///' + os.path.join(basedir, 'app.db')
    SQLALCHEMY_TRACK_MODIFICATIONS = False
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>


We can now create the database by running the following commands:

```
flask db init
flask db migrate -m "initialization db"
flask db upgrade
```

We now have our database. Verify everything went correctly by opening a new terminal in the backend folder (make sure the venv is activated), activating it by typing
```
python
```
importing the relevant modelsl:

```
>>> from bank import app, db
>>> from bank.main.models import Transaction
```
and creating one:
```
>>> tx = Transaction(from_account = '1', to_account= '2', amount = 1, currency = 'EUR', date = 'now')
```
If you get no errors that's good, everything works!

(For more in-depth information on databases and migrations check out Miguel Grinberg's <a href="https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world">Flask Megatutorial</a>.)

We now have our infrastructure that is able save transactions. We are now going to build the transfer. 

Transfers happen from one user to another. We therefore need to add another model to our database, namely that of users. This will become part of the Authentication section of later. We will therefore create a new folder _authentication_ in our _bank_ folder in our _backend_ directory. In _backend/bank/authentication_ we first create an empty __init__.py file. We then create a _models.py_ in this same folder.  

The structure then so far is:

```
backend
 - bank
  - main
  - authentication
   - __init__.py (empty)
   - models.py
 ...
```

What attributes will our user need? He will need a username, email and password of course. Also an account number, the address of the money, and the balance needs to be tracked. How these attributes will exactly be created by the user we will see later, for now we will just assume we have them. 

So, we open _models.py_ and add the following code:

{% capture notice-2 %}
backend/bank/authentication/models.py
```python
from bank import db

class User(db.Model):
    id = db.Column(db.Integer, primary_key = True)
    username = db.Column(db.String(64), index = True, unique = True)
    email = db.Column(db.String(128), unique = True)
    password = db.Column(db.String(128))
    account_number = db.Column(db.String(18))
    balance = db.Column(db.Integer)

    def __repr__(self):
        return '<User {}>'.format(self.username)
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

The table is not created yet. For that we have to run 

```
(venv) flask db migrate -m "added User table"
INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
INFO  [alembic.autogenerate.compare] Detected added table 'user'
INFO  [alembic.autogenerate.compare] Detected added index 'ix_user_username' on '['username']'
```

and 

```
(venv) flask db upgrade
INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade 43563d061c6d -> e39b32482124, added user
```

Ok, so let's assume the user initiates a transfer from the frontend. What should happen in the backend? The relevant information should be sent to the backend, and it should then be processed. 

This code we will add to a new file called _routes.py_ which will be housed in the same _main_ folder as before:

{% capture notice-2 %}
backend/bank/main/routes.py
```python
from bank import app
from bank.main.models import *
from bank.authentication.models import User

from datetime import datetime

def add_tx_to_db(
    username, 
    transaction_details, 
):
    amount, to_account, currency = transaction_details.values()
    amount = int(amount)
    user_sender = User.query.filter_by(username = username).first()
    user_receiver = User.query.filter_by(account_number = to_account).first()
    today = datetime.strftime(datetime.today(), "%d-%m-%Y")
    tx = Transaction(
        from_account = user_sender.account_number,
        to_account = to_account,
        amount = amount, 
        currency = currency, 
        date = today
    )
    user_sender.balance -= amount
    user_receiver.balance += amount
    db.session.add_all(
        [
            tx,
            user_sender, 
            user_receiver
        ]
    )
    db.session.commit()
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

So what happens is that we assume we will get a dictionary with the amount of the transaction, the account it is sent to, and the currency (Remember we hadd these fields in our TransferForm that we built previously). We transform
the _amount_ variable to an _int_, since in our dictionary it became a _string_. Since our program only knows our username, it will obtain the account number belonging to this username from the database, as well as the user that the transfer is made to. We then have all the fields that we need to define our new transaction in the database, which we do. We subtract the amount from the sender's balance and add it to the receiver's. We then commit this transaction to the database so that it is saved there. 

However, we don't want to allow any transaction to occur. We have to validate it first. What kind of validations would we want? Well, let's see, a transaction can't be negative and it has to be a number. Furthermore the sender needs to have enough cash in bank and it needs to be done in a supported currency (for now we will just allow EUR and USD). Finally, the account has to exist and has to be another one than your own. If any of these conditions fail, we want the transaction to not go through. In code that looks like this:

{% capture notice-2 %}
backend/bank/main/routes.py
```python
...

def validate_transaction(username, transaction_details):
    amount, to_account, currency = transaction_details.values()
    try:
        amount = int(amount)
    except:
        return False, "Invalid amount."
    user = User.query.filter_by(username = username).first()
    to_account_query = User.query.filter_by(account_number = to_account).first()
    if to_account_query == None:
        return False, "Account does not exist!"
    if to_account == user.account_number:
        return False, "You cannot send money to yourself, fool."
    elif amount == '' or int(amount) == 0:
        return False, "Amount can't be 0."
    elif amount > user.balance:
        return False, "Nice try, but you don't have enough money!"
    elif currency != "EUR" and currency != "USD":
        return False, "Transaction in currency {} not supported.".format(currency)
    else:
        return True, "Transaction sent!"

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We return both whether the condition failed and the specific message, which we will later use to show to the user. 

These functions are nice, but we need to connect this to our app. We add the following code:

{% capture notice-2 %}
backend/bank/main/routes.py
```python
...
@app.route('/send_transaction', methods = ['GET', 'POST'])
def send_transaction():
    message = request.get_json()
    username = message['username']
    transaction_details = message['transactionDetails']
    validated, validation_msg = validate_transaction(
        username, transaction_details
    )
    if validated:
        add_tx_to_db(
            username, transaction_details
        )
    result = gen_result_dict(
        result = validated,
        message = validation_msg
    )
    return result
...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Everytime we get a request, we obtain the transaction information from the request, we validate it and if it is validated we add it to the database. We then return whether it was validated and the message to the frontend.

The gen_result_dict is a function that I add in a utils file, since all subfunctions of the app may make use of it:


{% capture notice-2 %}
backend/bank/utils.py
```python
def gen_result_dict(**kwargs):
    return kwargs
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

which will have to be imported:

{% capture notice-2 %}
backend/bank/main/routes.py
```python
from bank import app 
from bank.main.models import *
from bank.authentication.models import User
from bank.utils import *

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>


Now let's connect this to our frontend! 

In our App.js file, we add the following method to our App component, after the initialization of the state and before the render method:

{% capture notice-2 %}
frontend/src/App.js
```javascript
class App extends Component {

    ...

    sendTransaction = async () => {
        const requestOptions = {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                username: this.state.name,
                transactionDetails: this.state.transactionDetails
            })
        }
        let response = await (await fetch('http://127.0.0.1:5001/send_transaction', requestOptions)).json()
        let message = response['message']
        alert(message)
    }

    ...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

What this method does is that everytime the sendTransaction method is invoked, it will send certain information from the current state in JSON form to the http://127.0.0.1:5001/send_transaction, which is the address of our backend, and more specifically the backend method that validates the transactions, since that method is listening to that address. Our backend function is run which validates the transaction and adds the transaction to the database if validated, and returns a validation boolean and a message, which it assigns to the _response_ variable. It will then display the message to our user. Based on the message we will know whether it succeeded. 

We also add the following code (adding initialTransactionDetails and modifying initialState) iwhich we will need for later:

{% capture notice-2 %}
frontend/src/App.js
```javascript
const initialTransactionDetails = {
  txAmount: 0,
  txToAccount: '',
  txCurrency: ''
}

const initialState = {
  username: '',
  route: 'overview',
  transactionDetails: initialTransactionDetails
}
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

The method is nice, but how does the application know when it needs to be invoked? Well, we want it to be invoked when we want to send a transaction, i.e. when we press the _Transfer_ button on the Transfer page. So we need to have the relevant button inherit this method:


{% capture notice-2 %}
frontend/src/App.js
```javascript
...

class App extends Component {

    ...

    render() {
        return (
        <div class = "app">
            {this.state.route === 'overview' 
            ?
            <Overview
            onRouteChange = {this.onRouteChange}
            />
            :
            <TransferPage
            onRouteChange = {this.onRouteChange}
            sendTransaction = {this.sendTransaction}
            />
            }
        </div>
        )
    }
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
        />
        <Card>
          <Card.Body>
            <TransferForm/>
            <Button 
              variant="primary"
              onClick = {this.props.sendTransaction}
              >Transfer 
            </Button>
          </Card.Body>
        </Card>
      </>
      )
  }
}

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

If we now try to initiate a transfer we get an error, something along the lines of 'Access to fetch has been blocked by CORS policy'. This is because we are sending requests from frontend to backend on the same machine. This is a security feature. To get over this we open our terminal and install (in our venv) 

```
pip install flask-cors
```
and change our initialization file:

{% capture notice-2 %}
frontend/src/subcomponents/main/Transfers.js
```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
from flask_cors import CORS

from config import Config

app = Flask(__name__)
app.config.from_object(Config)
cors = CORS(app)
db = SQLAlchemy(app)
migrate = Migrate(app, db)

from bank.main import routes, models
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Run the frontend by opening a terminal in your frontend directory and typing:

```
$ npm start
```
it should now run and go to http://localhost:3000/.

Run the backend by typing in your terminal (in your virtual env)

```
(venv) $ flask run
```

Now navigate to the Transfer page and click on Transfer. If everything went alright you should now see a notification with 'Account does not exist!'






