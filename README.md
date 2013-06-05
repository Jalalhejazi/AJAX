AJAX
====
## Basically...

Send `GET`, `POST`, `PUT`, and `DELETE` requests via AJAX to the DataService API server. 
These four HTTP request methods correspond to the four CRUD verbs (Create = `POST`, Retrieve = `GET`, Update = `PUT`, Delete = `DELETE`). 


Have a look at the Wikipedia article on [REST (Representational State Transfer)](http://en.wikipedia.org/wiki/Representational_State_Transfer) if you're not familiar with this style of web services.

## But first, a major caveat (Same Origin Policy)

Due to browser security constraints, **you cannot easily fetch data via JavaScript from a URL on a different domain/port than the one the page is being served from**. For example, if your DataService is at http://API.example.org/ and the WebApp with your JavaScript is on http://www.example.org/, the browser will block AJAX requests to the DataService URL. See [http://en.wikipedia.org/wiki/Same_origin_policy](http://en.wikipedia.org/wiki/Same_origin_policy) for details. 

There are two possible workarounds:

* Configure Web.config in your ServiceStack to use CORS
* Use [**JSONP**](http://en.wikipedia.org/wiki/JSON#JSONP) for your requests.


## Examples

To retrieve a User with ID `1`:

<table>
  <tr>
    <th>Plain JSON (i.e. via Reverse Proxy)</th>
    <th>JSONP</th>
  <tr>
    <td>
```js
jQuery.ajax({
  url: '/users/1.json',
  success: function(data) {
    alert('User '+data.user.id+' is '+data.user.username+'!')
  }
})
```
    </td>
    <td>
```js
jQuery.ajax({
  url: 'http://API.example.org/users/1.json',
  dataType: 'jsonp',
  success: function(data) {
    alert('User '+data.user.id+' is '+data.user.username+'!')
  }
})
```
    </td>
  </tr>
</table>

You can also retrieve a User by their username, like this:

<table>
  <tr>
    <th>Plain JSON (i.e. via Reverse Proxy)</th>
    <th>JSONP</th>
  <tr>
    <td>
```js
jQuery.ajax({
  url: '/users/jalal.json',
  success: function(data) {
    // do something with the user data
  }
})
```
    </td>
    <td>
```js
jQuery.ajax({
  url: 'http://api.example.org/users/jalal.json',
  dataType: 'jsonp',
  success: function(data) {
    // do something with the user data
  }
})
```
    </td>
  </tr>
</table>

To create a new User, send a `POST` request along with the User's data:

<table>
  <tr>
    <th>Plain JSON (i.e. via Reverse Proxy)</th>
    <th>JSONP</th>
  <tr>
    <td>
```js
jQuery.ajax({
  url: '/users.json',
  method: 'POST',
  data: {
    user: {
      username: 'JH',
      display_name: 'Jalal Hejazi',
      password: 'secret',
      kind: 'Instructor',
      metadata: {
        'hair colour': 'Black and some White'
      }
    }
  },
  success: function(data) {
    alert("User "+data.user.username+" created with ID "+data.user.id+"!")
  },
  error: function(data, statusCode) {
    alert("ERROR: "+data)
  }
})
```
    </td>
    <td>
```js
jQuery.ajax({
  url: 'http://api.example.org/users.json',
  dataType: 'jsonp',
  data: {
    _method: 'POST',
    user: {
      username: 'JH',
      display_name: 'Jalal Hejazi',
      password: 'secret',
      kind: 'Instructor',
      metadata: {
        'hair colour': 'Black and some White'
      }
    }
  },
  success: function(data) {
    if (data.error)
      alert("ERROR: "+data.error)
    else
      alert("User "+data.user.username+" created with ID "+data.user.id+"!")
  }
})
```
    </td>
  </tr>
</table>


To authenticate a user (i.e. start a session for a user):

<table>
  <tr>
    <th>Plain JSON (i.e. via Reverse Proxy)</th>
    <th>JSONP</th>
  <tr>
    <td>
```js
jQuery.ajax({
  url: '/sessions.json',
  method: 'POST',
  data: {
    session: {
      username: 'JH',
      password: 'topsecret'
    }
  },
  success: function(data) {
    alert("Authenticated! Your token is: "+data.session.token)
  },
  error: function(data, statusCode) {
    alert("ERROR: "+data)
  }
})
```
    </td>
    <td>
```js
jQuery.ajax({
  url: '/sessions.json',
  data: {
    _method: 'POST',
    session: {
      username: 'JH',
      password: 'topsecret'
    }
  },
  success: function(data) {
    if (data.error)
      alert("ERROR: "+data.error)
    else
      alert("Authenticated! Your token is: "+data.session.token)
  }
})
```
    </td>
  </tr>
</table>

To validate a session token and retrieve user info from the session:

<table>
  <tr>
    <th>Plain JSON (i.e. via Reverse Proxy)</th>
    <th>JSONP</th>
  <tr>
    <td>
```js
jQuery.ajax({
  url: '/sessions/validate_token.json',
  method: 'GET',
  data: {
    token: <YOUR TOKEN>
  },
  success: function(data) {
    console.log(data.session)
    console.log(data.session.user)
  },
  error: function(data, statusCode) {
    alert("ERROR: "+data)
  }
})
```
    </td>
    <td>
```js
jQuery.ajax({
  url: '/sessions/validate_token.json',
  dataType: 'jsonp',
  data: {
    _method: 'GET',
    token: <YOUR TOKEN>
  },
  success: function(data) {
    if (data.error)
      alert("ERROR: "+data.error)
    else
      alert("Token is valid for user: "+data.session.user.username)
  }
})
```
    </td>
  </tr>
</table>


* Use [**Nodejs with ServiceStack**](http://DataService.supermobile.dk) for next generation DataService.
