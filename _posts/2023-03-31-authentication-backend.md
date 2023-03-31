---
title: "Part 9: Authentication - Backend"
date: 2023-03-28
tags:
  - Focus

---

<h1>Building Authentication Backend</h1>

We built the authentication frontend in our previous installation. 

Before we can build the backend functionality there is one last thing we need to take care of. The fields that are typed in the Authentication fields are not saved somewhere. So you can type whatever you want and nothing will happen. 

We will use a similar technique as for the tracking of the TransferForm fields. 

We first add a new object to App.js and change our state:

{% capture notice-2 %}
frontend/src/App.js
```javascript
...

const initialLoginDetails = {
  username: '',
  email: '',
  password: ''
}

...

const initialState = {
  route: 'login',
  loginDetails: initialLoginDetails,
  txDetails: initialTxDetails
}

...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

On initialization the app will be laoded with these initial objects. This ensures consistency. 

We then take our previous defined method onFormTextChange and have the Authentication component inherit it:

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
        />

        ...
```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

and have Authentication inherit it to the even lower-level components:


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
        />
      :
        <Register
        onRouteChange = {this.props.onRouteChange}
        onFormTextChange = {this.props.onFormTextChange}
        />
      }
      </>
    )
  }
}

class LogIn extends Component {
        ...
        <Form.Group className="mb-3">
          <Form.Label>Username</Form.Label>
          <Form.Control type="username"
          onChange = {(event) => {this.props.onFormTextChange('loginDetails', 'username', event.target.value)}}
          />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Password</Form.Label>
          <Form.Control type="password" 
          onChange = {(event) => {this.props.onFormTextChange('loginDetails', 'password', event.target.value)}}
          />
        </Form.Group>
        ...

class Register extends Component {
        ...
        <Form.Group className="mb-3">
          <Form.Label>Email</Form.Label>
          <Form.Control type="email"
          onChange = {(event) => {this.props.onFormTextChange('loginDetails', 'email', event.target.value)}}
          />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Username</Form.Label>
          <Form.Control type="username"
          onChange = {(event) => {this.props.onFormTextChange('loginDetails', 'username', event.target.value)}}
          />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Password</Form.Label>
          <Form.Control type="password" 
          onChange = {(event) => {this.props.onFormTextChange('loginDetails', 'password', event.target.value)}}
          />
        </Form.Group>
        ...

```
{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

Now, if we type something into the fields, it is saved in state and can be sent to the backend to be processed. 



