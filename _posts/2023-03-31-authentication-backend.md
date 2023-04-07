---
title: "Part 9: Authentication - Backend"
date: 2023-03-31
tags:
  - Focus

---

<h1>Building Authentication Backend</h1>

We built the authentication frontend in our previous installation. 

Now, if we type something into the fields, it is saved in state and can be sent to the backend to be processed. We now want to send that information to the backend, everytime it is entered. 

For that we create a new file _routes.py_ in our _backend/bank/authentication_ directory
```
backend
 - bank
  - authentication
   - __init__.py
   - models.py
   - routes.py
```

We open _routes.py_ and add the following code:


{% capture notice-2 %}
backend/bank/authentication/routes.py
```python
from bank import app
from bank.authentication.models import User
from bank.utils import *

from flask import request

@app.route('/register', methods = ['GET', 'POST'])
def register():
    message = request.get_json()
    email, username, password = message.values()['loginDetails']
    user_exists = db_query(User, 'username', username)[0]
    email_exists = db_query(User, 'email', email)[0]
    if user_exists:
        return gen_result_dict(
            success = False, 
            message = 'Username already exists, choose another username.'
        )
    if email_exists:
        return gen_result_dict(
            success = False, 
            message = 'Email address already taken.'
        )
    else:
        pass
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Here, we import the relevant imports. We then register a new route called '/register', which means that everytime something is sent to the '/register' address the method is invoked. We retrieve the variables
that were sent by the request body, which contains the email, username and password fields. We run the db_query function, which checks in the table that is provided to it (first argument, in this case the User) whether
an user exists with the attribute (2nd argument) equal to the value provided (third argument). Both the username and the email need to be unique. If one of those fields already exists with that value, a False is returned with a custom message. If those values don't exist yet, we need to add logic for what happens then. 

First we adapt our User class slightly:

{% capture notice-2 %}
backend/bank/authentication/models.py
```python
...

from werkzeug.security import generate_password_hash, check_password_hash

def elongate_account_number(idx, length = 10):
    len_idx = len(str(idx))
    len_remain = length - len_idx
    elongated = '0' * len_remain + str(idx)
    return elongated

class User(db.Model):
    ...

    def set_password(self, password):
        self.password = generate_password_hash(password)
        
    def check_password(self, password):
        return check_password_hash(self.password, password)

    def create_new_account(self, idx, signup_bonus):
        if signup_bonus['active']:
            self.balance = signup_bonus['amount']
        else:
            self.balance = 0

        suffix = elongate_account_number(idx)
        self.account_number = 'NL01AAZB' + suffix

    def __repr__(self):
        return '<User {}>'.format(self.username)
```

We update our database:

```
(venv) flask db migrate -m "added login methods"
INFO  [alembic.runtime.migration] Context impl SQLiteImpl.     
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
INFO  [alembic.autogenerate.compare] Detected added index 'ix_user_email' on '['email']'
```

```
(venv) flask db upgrade
INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade e39b32482124 -> 21fa7e8b99be, added login methods
```

{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

We have added a new method to our User class, set_password and check_password. Every time a user is created with the password provided, it will save that password as a hash. This is a good security practice, because if the database is compromised, the password hash can not be used to login. We also add a method to compare the hash of the entered password with the hash of the password that is stored in the database. We have also added a create_new_account method, which executes some logic when an account is created. In this case it checks if the signup bonus is active and will then add that balance to the user's account (this is nice for testing purposes later, so that when we create an account we have some balance to play with). We also create a unique bank account for the user in the IBAN format, starting with 'NL01AAZB' the identifier of my hypothetical bank, with trailing zeros and ending in the index of user, while being 18 characters long in total. 

We first change something in our _config.py_ file:

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
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

This way our signup bonus is active, so that every time a user creates an account he gets 20 for free. If only every bank did this! We continue in _routes.py_

{% capture notice-2 %}
backend/bank/authentication/routes.py
```python
...
from bank import app, db
from config import Config

...

def add_user_to_db(username, email, password):
    user_idx = get_idx()
    new_user = User(
        username = username, 
        email = email
    )
    new_user.set_password(password)
    new_user.create_new_account(
        user_idx, 
        signup_bonus = Config.SIGNUP_BONUS
    )
    db.session.add(new_user)
    db.session.commit()
    return new_user

@app.route('/register', methods = ['GET', 'POST'])
def register():
    ...
    else:
        new_user = add_user_to_db(
            username, email, password
        )
        return gen_result_dict(
            success = True, 
            message = 'Account created successfully! Welcome {}!'.format(username),
            result = gen_result_dict(
                account_details = gen_result_dict(
                    username = username,
                    accountNumber = new_user.account_number,
                    balance = new_user.balance,
                )
            )
        )
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

This way, if the entered username and email address don't exist yet, it creates a new User object with the entered email and username, saving the password hash to it and assigning a balance and a username. This new User is then saved to the database. We return a response message with the information, namely that a new User was successfully created, including the existing account and transaction details that we will want to show later. Since we assume that a user registers once he wants to use the tool, we will return the information that will be shown immediately and have the user login. It's bad user experience if he then has to go back to the login screen first. Great, it is now possible to create a new user! Now, we want an existing user to be able to login. To that end we add another route in our _routes.py_ file:

{% capture notice-2 %}
backend/bank/authentication/routes.py
```python
...

@app.route('/login', methods = ['GET', 'POST'])
def login():
    message = request.get_json()['loginDetails']
    username, password = message.values()
    exists, user = db_query(User, 'username', username)
    if exists:
        if user.check_password(password):
            result = get_overview(user)
            return gen_result_dict(
                success = True, 
                message = 'Login successful!',
                result = result
            )
        else:
            return gen_result_dict(
                success = False, 
                message = 'Wrong info, fool.'
            )
    else:
        return gen_result_dict(
                success = False, 
                message = 'User not found.'
            )
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

The username and password are retrieved. It checks whether the user exists, and if not it returns a message. If it exists, it checks the password, if that is incorrect it returns a message. If the password is correct it gets the overview and returns it. This function get_overview(), we add it to the _main/routes.py_ file:

{% capture notice-2 %}
backend/bank/main/routes.py
```python
...

def get_overview(user):
    return gen_result_dict(
        account_details = gen_result_dict(
            username = user.username,
            accountNumber = user.account_number,
            balance = user.balance,
        )
    )

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

and add an import to our authentication routes so it can  make use of that

{% capture notice-2 %}
backend/bank/authentication/routes.py
```python
...
from bank.main.routes import get_overview

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

and 

{% capture notice-2 %}
backend/bank/__init__.py
```python
...

from bank.authentication import routes, models
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Our backend functionality now works, so we have to connect it to the frontend.

{% capture notice-2 %}
frontend/src/App.js
```javascript
...

class App extends Component {
  ...

  render()
    ...

    <Authentication
      state = {this.state}
      onRouteChange = {this.onRouteChange}
      onFormTextChange = {this.onFormTextChange}
      onAuthentication = {this.onAuthentication}
    >
}
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>


{% capture notice-2 %}
frontend/src/subcomponents/authentication/Authentication.js
```javascript
...

class Authentication extends Component {
  render() {
    return (
      <>
      {this.props.state.route === 'login'
      ?
        <LogIn
        onRouteChange = {this.props.onRouteChange}
        onFormTextChange = {this.props.onFormTextChange}
        onAuthentication = {this.props.onAuthentication}
        />
      :
        <Register
        onRouteChange = {this.props.onRouteChange}
        onFormTextChange = {this.props.onFormTextChange}
        onAuthentication = {this.props.onAuthentication}
        />
      }
      </>
    )
  }
}

...

class LogIn extends Component {
  render() {
        ...
          <Button 
          variant = "primary"
          onClick = {() => {this.props.onAuthentication('login')}} 
          >Login
          </Button>
          ...

class Register extends Component {
    render() {
        ...
          <Button 
          variant = "primary" 
          onClick = {() => {this.props.onAuthentication('register')}} 
          >Register
          </Button>
        ...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Nice! We can now create a new account and login with those credentials. Try it!

Finally, we will add a Button to log out in the navigation bar:

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
            onClick = {() => {this.props.onRouteChange('overview')}}
            >Overview
          </Button>
          <Button 
            variant = "primary"
            onClick = {() => {this.props.onRouteChange('login')}}
            >Sign Out
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

The method that we need (onRouteChange) is already registered luckily, so we don't need to add that again. Nice!










