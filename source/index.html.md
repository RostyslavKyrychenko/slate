---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='https://www.admitad.com/en/sign_in/'>Sign In for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction


Admitad API is a functional interface which gives access to the whole functionality of the admitad.com. It is compatible with all programming languages which supports JSON.



This interface helps you to create applications for Admitad affiliate network. You can build up applications to analyze your own statistics and accounting records or to put them up for sale in the Admitad App Store.



First of all we recommend to create an account on admitad.com and get acquainted with the main service items, then decide what kind of application you want to create.

After the final idea formation it`s useful to turn a hand to the architecture development and study the opportunities of our API, with the help of which you create your application.

# Authorization Description

Open protocol OAuth 2.0 is used for the purposes of Admitad applications authorization. The current documentation includes an authorization mechanism that complies with the protocol specification:

        Authorization of applications on Admitad.com
        <br>
        Authorization of third-party web-applications
        <br>
        Client authorization
        <br>
        Access_token refresh
        <br>
        Help on access settings
        <br>

Upon authorization you will be provided with an access_token with the help of which you can make requests to Admitad API. Depending on the set of acquired access rights some API methods may not be available.

## Authorization of third-party web-applications

**Opening authorization dialogue**

    To authorize the user, it is necessary to redirect the user’s browser on URL https://api.admitad.com/authorize/, using application/x-www-form-urlencoded data format and transmit the following parameters:

<table>
  <tr>
    <th>Name</th>
    <th>Required</th> 
    <th>Description</th>
  </tr>
  <tr>
    <td>client_id</td>
    <td>✔</td> 
    <td>ID of your application.*</td>
  </tr>
  <tr>
    <td>redirect_uri</td>
    <td>✔</td> 
    <td>URL to which the user will be redirected after authorization.**</td>
  </tr>
  <tr>
    <td>state</td>
    <td></td> 
    <td>The value used by the client to check the state between request and the server response.***</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>✔</td> 
    <td>A list of application access settings separated by a space that shall be requested.</td>
  </tr>
  <tr>
    <td>response_type</td>
    <td>✔</td> 
    <td>Type of response you would like to get.</td>
  </tr>
</table>

<ul>
  <li>*   The ID of the application is available for the logged in publisher at the homepage for developers (when the “Get keys” button is clicked).</li>
  <li>**  The domain of the specified URL shall comply with the primary domain in the settings of the application)</li>
  <li>*** The server returns this value when it redirects the user agent back to the client</li>
<ul>

Example of request:

`https://api.admitad.com/authorize/?scope=advcampaigns banners websites&state=7c232ff20e64432fbe071228c0779f&redirect_uri=https://google.com/&response_type=code&client_id=cb281d918a37e346b45e9aea1c6eb7`

If the user is not logged in, a dialogue box will be displayed offering to enter login and password.

**Granting access rights**

When logged in the user will be offered to authorize the application by providing access to required settings requested by means of parameter scope. A complete list of settings is available in the section of application access rights.

**Getting code parameter**

Upon successful app authorization the user’s browser will be redirected to redirect_uri, the URL specified when the authorization dialogue appears. The code to get an access token code will be transferred in GET-parameter to the specified address:
`https://redirect_uri/?state=7c232ff20e64432fbe071228c0779f&code=c75ebf64ad48a352630b6d953ce365`

In case of an error, the user’s browser will be redirected with the error code and description:
`https://redirect_uri/?state=7c232ff20e64432fbe071228c0779f&error_description=client_id+cb281d918a37e346b45e9aea1c6eb7+doesn't+exist&error=invalid_client`

**Getting access_token**

To get access_token it is required to send POST request to URL https://api.admitad.com/token/ using data format application/x-www-form-urlencoded and transfer the following parameters:

<table>
  <tr>
    <th>Name</th>
    <th>Required</th> 
    <th>Description</th>
  </tr>
  <tr>
    <td>client_id</td>
    <td>✔</td> 
    <td>ID of your application.*</td>
  </tr>
  <tr>
    <td>client_secret</td>
    <td>✔</td> 
    <td>Secret key of your application</td>
  </tr>
  <tr>
    <td>code</td>
    <td>✔</td> 
    <td>The code received at the previous stage of authorization (parameter redirect_uri).</td>
  </tr>
  <tr>
    <td>grant_type</td>
    <td>✔</td> 
    <td>Type of request - authorization_code</td>
  </tr>
  <tr>
    <td>redirect_uri</td>
    <td>✔</td> 
    <td>Address, to which the user will be re-addressed after authentication (the domain of the indicated address must correspond to the main domain in the application settings).</td>
  </tr>
</table>

    The request should use HTTP Basic authentification with the use of client_id* and client_secret* as access settings. The header of authorization is a base64-encoded string that contains colon-concatenated client_id and client_secret.

            * The identifier (client_id) and the secret key (client_secret) of the application are available for the logged in publisher at the homepage for developers (when the “Get keys” button is clicked).

**Below is an example of forming a base64-encoded authorization header in Python 2.7 for client_id=’cb281d918a37e346b45e9aea1c6eb7’ and client_secret=’a0f8a8b24de8b8182a0ddd2e89f5b1’:**

    `from base64 import b64encode
    client_id='cb281d918a37e346b45e9aea1c6eb7'
    client_secret='a0f8a8b24de8b8182a0ddd2e89f5b1'
    data = client_id + ':' + client_secret
    # data = 'cb281d918a37e346b45e9aea1c6eb7:a0f8a8b24de8b8182a0ddd2e89f5b1'
    data_b64_encoded = b64encode(data)`

    **Below is an example of a base64-encoded authorization header (the data_b64_encoded variable):**

    `Y2IyODFkOTE4YTM3ZTM0NmI0NWU5YWVhMWM2ZWI3OmEwZjhhOGIyNGRlOGI4MTgyYTBkZGQyZTg5ZjViMQ==`

**Below is an example of a request using a curl utility for client_id=cb281d918a37e346b45e9aea1c6eb7, where b64XXX is the base64-encoded authorization header:**

`curl -H 'Authorization: Basic b64XXX' -X POST https://api.admitad.com/token/ -d 'code=c75ebf64ad48a352630b6d953ce365&client_secret=a0f8a8b24de8b8182a0ddd2e89f5b1&grant_type=authorization_code&client_id=cb281d918a37e346b45e9aea1c6eb7&redirect_uri=https%3A%2F%2Fgoogle.com%2F'`

**Example of request:**

`POST /token/ HTTP/1.1`
`Host: api.admitad.com`
`Content-Type: application/x-www-form-urlencoded;charset=UTF-8`

`code=c75ebf64ad48a352630b6d953ce365&client_secret=a0f8a8b24de8b8182a0ddd2e89f5b1&grant_type=authorization_code&client_id=cb281d918a37e346b45e9aea1c6eb7&redirect_uri=https%3A%2F%2Fgoogle.com%2F`

As a result of this request you will get a new access_token. The time to live of the token in seconds expires_in, refresh_token and additional information for users are returned as well:

`{  
"username": "webmaster1",&nbsp;
"first_name": "name",  
"last_name"': "surname",  
"language": "ru",  
"access_token": "4b8b33955a",  
"token_type": "bearer",  
"expires_in": 604800,  
"refresh_token": "ea957cce42",  
"scope": "advcampaigns banners websites"  
}`

## Client authorization

**Client authorization**



# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

