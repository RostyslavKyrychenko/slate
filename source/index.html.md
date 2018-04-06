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

#includes:
#  - errors

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
</ul>

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
"username": "webmaster1,
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

To authorize the user, it is required to send POST request to URL https://api.admitad.com/token/, using data format application/x-www-form/urlencoded and transfer the following parameters:

<table>
  <tr>
    <th>Name</th>
    <th>Required</th> 
    <th>Description</th>
  </tr>
  <tr>
    <td>client_id</td>
    <td>✔</td> 
    <td>ID of your application*</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>✔</td> 
    <td>A list of application access settings separated by a space that shall be requested.</td>
  </tr>
  <tr>
    <td>grant_type</td>
    <td>✔</td> 
    <td>Type of request: client_credentials</td>
  </tr>
</table>

It is required to use HTTP Basic authentification for the request using client_id* and client_secret* as access parameters. The authorization header is base64 encoded line that contains client_id and client_secret united by a colon.

<ul>
  <li>* The identifier (client_id) and the secret key (client_secret) of the application are available for the logged in publisher at the homepage for developers (when the “Get keys” button is clicked).</li>
</ul>

**Below is an example of forming a base64-encoded authorization header in Python 2.7 for client_id=’cb281d918a37e346b45e9aea1c6eb7’ and client_secret=’a0f8a8b24de8b8182a0ddd2e89f5b1’:**

`from base64 import b64encode`
`client_id='cb281d918a37e346b45e9aea1c6eb7'`
`client_secret='a0f8a8b24de8b8182a0ddd2e89f5b1'`
`data = client_id + ':' + client_secret`
`# data = 'cb281d918a37e346b45e9aea1c6eb7:a0f8a8b24de8b8182a0ddd2e89f5b1'`
`data_b64_encoded = b64encode(data)`

**Below is an example of a base64-encoded authorization header (the data_b64_encoded variable):**

`Y2IyODFkOTE4YTM3ZTM0NmI0NWU5YWVhMWM2ZWI3OmEwZjhhOGIyNGRlOGI4MTgyYTBkZGQyZTg5ZjViMQ==`

**Below is an example of a request using a curl utility for client_id=cb281d918a37e346b45e9aea1c6eb7, where b64XXX is the base64-encoded authorization header:**

`curl -H 'Authorization: Basic b64XXX' -X POST https://api.admitad.com/token/ -d 'grant_type=client_credentials&client_id=cb281d918a37e346b45e9aea1c6eb7&scope=advcampaigns banners websites'`

**Example of request:**

`POST /token/ HTTP/1.1`
`Host: api.admitad.com`
`Authorization: Basic b64XXX`
`Content-Type: application/x-www-form-urlencoded;charset=UTF-8`

`grant_type=client_credentials&client_id=cb281d918a37e346b45e9aea1c6eb7&scope=advcampaigns banners websites`

As a result of this request you will get a new access_token. The time to live of the token in seconds expires_in, refresh_token and additional information for users are returned as well:

`{`
    `"username": "webmaster1",`
    `"first_name": "name",`
    `"last_name"': "surname",`
    `"language": "ru",`
    `"access_token": "4b8b33955a",`
    `"token_type": "bearer",`
    `"expires_in": 604800,`
    `"refresh_token": "ea957cce42",`
    `"scope": "advcampaigns banners websites"`
`}`

## Access_token refresh

**Access_token refresh**

In case access_token expires, you can update it using refresh_token. Make POST request to URL https://api.admitad.com/token/ using data format application/x-www-form-urlencoded and transfer the following parameters:

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
    <td>grant_type</td>
    <td>✔</td> 
    <td>Type of request: client_credentials</td>
  </tr>
  <tr>
    <td>refresh_token</td>
    <td>✔</td> 
    <td>The value obtained after authorization.</td>
  </tr>
</table>

<ul>
  <li>* ID and the secret key of the application are available for the logged in publisher at the homepage for developers (when the “Get keys” button is clicked).</li>
</ul>

**Below is an example of a request using a curl utility for client_id=cb281d918a37e346b45e9aea1c6eb7, client_secret=a0f8a8b24de8b8182a0ddd2e89f5b1 and refresh_token=7521b7640c:**

`curl -X POST https://api.admitad.com/token/ -d 'grant_type=refresh_token&client_id=cb281d918a37e346b45e9aea1c6eb7&refresh_token=7521b7640c&client_secret=a0f8a8b24de8b8182a0ddd2e89f5b1'`

**Example of request:**

`POST /token/ HTTP/1.1`
`Host: api.admitad.com`
`Content-Type: application/x-www-form-urlencoded;charset=UTF-8`

`grant_type=refresh_token&client_id=cb281d918a37e346b45e9aea1c6eb7&refresh_token=7521b7640c&client_secret=a0f8a8b24de8b8182a0ddd2e89f5b1`

As a result of this request you will get a new access_token. The time to live of the token in seconds expires_in, refresh_token and additional information for users are returned as well:

`{`
    `"username": "webmaster1",`
    `"first_name": "name",`
    `"last_name"': "surname",`
    `"language": "ru",`
    `"access_token": "4b8b33955a",`
    `"token_type": "bearer",`
    `"expires_in": 604800,`
    `"refresh_token": "ea957cce42",`
    `"scope": "advcampaigns banners websites"`
`}`

## Authorization of applications on Admitad.com

**Authorization of applications on Admitad.com**

You may create any integrated application uploaded from your server using an IFrame element built on Admitad page. Such applications can display information by means of any technologies supported by the user browser: HTML, Javasсript, AJAX, Flash, etc.

**Due to the fact that Admitad.com uses a secure protocol (HTTPS), your website uploaded in our ‘container’ shall also use HTTPS protocol. Buy an SSL-certificate and install it on your server.**

**Application launch parameters**

When the application is displayed by means of a request line (GET or POST method), the following parameters are sent to the application:

<ul>
 <li>signed_request</li>
 <li>retloc is the URL of the page in which iframe with the application are built.</li>
</ul>

**Example of ``retloc``:**
`https%3A//admitad.com/appstore/app/getcoupon/`

**Example of a request**

`https://getcoupons.ru/?signed_request=d3ddf1100c5e47a466cafe1e0dc8cb40a4f7bc3219744be1e049dd6d7a76450c.eyJ1c2VybmFtZSI6ICJhZHZlcnRpc2VyMSIsICJmaXJzdF9uYW1lIjogIm5hbWUiLCAibGFzdF9uYW1lIjogInN1cm5hbWUiLCAiYWxnb3JpdGhtIjogIkhNQUMtU0hBMjU2IiwgImxhbmd1YWdlIjogInJ1IiwgImFjY2Vzc190b2tlbiI6ICIwODdkNmNjNDM3IiwgImV4cGlyZXNfaW4iOiA2MDgwMCwgImlkIjogMTMwOTAsICJyZWZyZXNoX3Rva2VuIjogIjc1MjFiNzY0MGMifQ==&retloc=https%3A//apps.admitad.com/ru/getcoupons/`

## Description of signed_request line

**Description of signed_request line**


The signed_request string is a joint signature with the use of the HMAC SHA256 method for a base64-encoded data string in JSON format, point (.), and the base64-encoded data string itself in JSON format.
Data are signed by the secret key of your application known to Admitad only. The signature helps you to get sure that the request was sent by Admitad. It is impossible to falsify signed_request line without a secret key.

<ul>
<li>the secret key of the application is available for the logged in publisher at the homepage for developers (when the “Get keys” button is clicked).</li>
</ul>

**Example of data transferred to the application:**

{
    'username':         'webmaster1',
    'id':               13090,
    'first_name':       'name',
    'last_name':        'surname',
    'algorithm':        'HMAC-SHA256',
    'language':         'ru',
    'access_token':     '087d6cc437',
    'refresh_token':    '7521b7640c',
    'expires_in':       604800
}

**Description of data fields:**

<table>
  <tr>
    <th>Name</th>
  </tr>
  <tr>
    <td>id</td>
  </tr>
  <tr>
    <td>first_name</td>
  </tr>
  <tr>
    <td>last_name</td>
  </tr>
  <tr>
    <td>algorithm</td>
  </tr>
  <tr>
    <td>language</td>
  </tr>
  <tr>
    <td>access_token</td>
  </tr>
  <tr>
    <td>refresh_token</td>
  </tr>
  <tr>
    <td>expires_in</td>
  </tr>
</table>

**Below is an example of Python 2.7 code for encoding data, where client_secret = a0f8a8b241d8b8182a0ddd2e89f5b1:**

`import hmac`
`import json`
`from hashlib import sha256`
`from base64 import b64encode`
`data = {`
    `'username': 'advertiser1',`
    `'id': 13090,`
    `'first_name': 'name',`
    `'last_name': 'surname',`
    `'algorithm': 'HMAC-SHA256',`
    `'language': 'ru',`
    `'access_token': '087d6cc437',`
    `'refresh_token': '7521b7640c',`
    `'expires_in': 604800`
`}`
`data = b64encode(json.dumps(data))`
`signature = hmac.new(str(client_secret), msg=data, digestmod=sha256).hexdigest()`
`signed_request = '%s.%s' % (signature, data)`

**Below is an example of Python 2.7 code for encoding data:**

import hmac
import json
from hashlib import sha256
from base64 import b64decode

def decode_data(signed_request):
    signature, encoded_data = signed_request.split('.', 1)
    data = json.loads(b64decode(encoded_data))
    if data.get('algorithm').upper() != 'HMAC-SHA256':
        return
    expected_signature = hmac.new(
        str(client_secret), msg=encoded_data, digestmod=sha256).hexdigest()
    if signature != expected_signature:
        return
    return data

**Below is an example of signed data (the signed_request variable):**

`d3ddf1100c5e47a466cafe1e0dc8cb40a4f7bc3219744be1e049dd6d7a76450c.eyJ1c2VybmFtZSI6ICJhZHZlcnRpc2VyMSIsICJmaXJzdF9uYW1lIjogIm5hbWUiLCAibGFzdF9uYW1lIjogInN1cm5hbWUiLCAiYWxnb3JpdGhtIjogIkhNQUMtU0hBMjU2IiwgImxhbmd1YWdlIjogInJ1IiwgImFjY2Vzc190b2tlbiI6ICIwODdkNmNjNDM3IiwgImV4cGlyZXNfaW4iOiA2MDgwMCwgImlkIjogMTMwOTAsICJyZWZyZXNoX3Rva2VuIjogIjc1MjFiNzY0MGMifQ==`

## Help on access settings

To get the required rights during authorization, it is required to send scope parameter containing names of rights separated by a space.

<table>
  <tr>
    <th>Name</th>
    <th>Access</th> 
  </tr>
  <tr>
    <td>public_data</td> 
    <td>
      <ul>
        <li>Types of ad spaces</li>
        <li>Ad space languages</li>
        <li>Advertising services</li>
        <li>Ad space categories</li>
        <li>Ad space regions</li>
        <li>List of currencies</li>
        <li>Coupon categories</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>websites</td> 
    <td>
      <ul>
        <li>List of ad spaces</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_websites</td> 
    <td>
      <ul>
        <li>Create an ad space</li>
        <li>Edit an ad space</li>
        <li>Ad space confirmation</li>
        <li>Delete an ad space</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>advcampaigns</td> 
    <td>
      <ul>
        <li>List of affiliate programs</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>advcampaigns_for_website</td> 
    <td>
      <ul>
        <li>List of programs for the ad space</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_advcampaigns</td> 
    <td>
      <ul>
        <li>Join affiliate programs</li>
        <li>Unjoin affiliate programs</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>banners</td> 
    <td>
      <ul>
        <li>Banners list</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>landings</td> 
    <td>
      <ul>
        <li>List of landing pages</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>banners_for_website</td> 
    <td>
      <ul>
        <li>List of banners for the ad space</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>payments</td> 
    <td>
      <ul>
        <li>List of payment requests</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_payments</td> 
    <td>
      <ul>
        <li>Creation of payment requests</li>
        <li>Delete payment requests</li>
        <li>Confirm payment requests</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>announcements</td> 
    <td>
      <ul>
        <li>List of notifications</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>referrals</td> 
    <td>
      <ul>
        <li>List of referrals</li>
      </ul>
    </td>
  </tr>
   <tr>
    <td>coupons</td> 
    <td>
      <ul>
        <li>List of coupons</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>coupons_for_website</td> 
    <td>
      <ul>
        <li>List of coupons for the ad space</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>private_data</td> 
    <td>
      <ul>
        <li>Info about the publisher (name, language)</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>tickets</td> 
    <td>
      <ul>
        <li>List of publisher tickets</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_tickets</td> 
    <td>
      <ul>
        <li>Create a ticket</li>
        <li>Comment on the ticket</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>private_data_email</td> 
    <td>
      <ul>
        <li>Info about the publisher (name, language, e-mail)</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>private_data_phone</td> 
    <td>
      <ul>
        <li>Info about the publisher (name, language, phone number)</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>private_data_balance</td> 
    <td>
      <ul>
        <li>Info about the publisher balance</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>validate_links</td> 
    <td>
      <ul>
        <li>Check links</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>deeplink_generator</td> 
    <td>
      <ul>
        <li>Deeplink generator</li>
      </ul>
    </td>
  </tr>
   <tr>
    <td>statistics</td> 
    <td>
      <ul>
        <li>Publisher reports</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>opt_codes</td> 
    <td>
      <ul>
        <li>List of Postback URLs</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_opt_codes</td> 
    <td>
      <ul>
        <li>Create Postback URL by action</li>
        <li>Editing Postback URL by action</li>
        <li>Creating of Postback URLs by the program status change</li>
        <li>Editing Postback URL by the program change status</li>
        <li>Removing Postback URLs</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>webmaster_retag</td> 
    <td>
      <ul>
        <li>List of ReTag tags</li>
        <li>Available program levels</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_webmaster_retag</td> 
    <td>
      <ul>
        <li>Create ReTag tags</li>
        <li>Edit ReTag tags</li>
        <li>Delete ReTag tags</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>broken_links</td> 
    <td>
      <ul>
        <li>List of broken links</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_broken_links</td> 
    <td>
      <ul>
        <li>Fixing broken links</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>lost_orders</td> 
    <td>
      <ul>
        <li>List of lost orders</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_lost_orders</td> 
    <td>
      <ul>
        <li>Creation of lost order</li>
        <li>Canceling a lost order</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>broker_application</td> 
    <td>
      <ul>
        <li>List of broker applications</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>manage_broker_application</td> 
    <td>
      <ul>
        <li>Creating broker applications</li>
      </ul>
    </td>
  </tr>
</table>

**Example:**

`scope=public_date20%websites%20coupons`

## Help on API response codes

**Help on API response codes**

<ul>
  <li>in case of a positive result, the response will be 200, 201 or 202.</li>
  <li>if the URL is incorrect, the response will be 404.</li>
  <li>if not enough rights for the transaction, the response will be 403 or 401.</li>
  <li>in case of any errors, the response will be 400 or 500.</li>
  <li>in case the maximum number of requests for the period is exceeded, the response will be 503. The current limit - up to 60 requests per minute for the application.</li>
</ul>

The response of API server is in JSON format. In case of an error, the response of the server will look like this:

HTTP/1.1 401 UNAUTHORIZED
Server: nginx/1.2.3
Date: Mon, 17 Sep 2012 10:07:39 GMT
Content-Type: application/json; charset=utf-8
Transfer-Encoding: chunked
Connection: keep-alive
Vary: Authorization, Cookie
WWW-Authenticate: Bearer realm="", error="invalid_token", error_description="Token doesn't exist"
Content-Language: ru
Set-Cookie: section=webmaster; expires=Tue, 17-Sep-2013 13:07:39 GMT; Path=/

{"error_description": "Token doesn't exist", "error_code": 1, "error": "invalid_token"}

Transferred data include the name of the error error. They can also include additional information:

<ul>
  <li>**error_description** - error description</li>
  <li>**error_code** - additional error code</li>
</ul>

**Additional error codes:**

<table>
  <tr>
    <th>Value</th>
    <th>Description</th> 
  </tr>
  <tr>
    <td>0</td>
    <td>The token expired</td> 
  </tr>
  <tr>
    <td>1</td>
    <td>Incorrect or invalid token</td> 
  </tr>
  <tr>
    <td>2</td>
    <td>The key has no sufficient access rights for performing the operation</td> 
  </tr>
  <tr>
    <td>3</td>
    <td>Incorrect request</td> 
  </tr>
  <tr>
    <td>4</td>
    <td>The maximum number of requests for the period expired</td> 
  </tr>
  <tr>
    <td>5</td>
    <td>The refresh token is unavailable</td> 
  </tr>
  <tr>
    <td>6</td>
    <td>The maximum number of tokens has been exceeded</td> 
  </tr>
</table>

# API Parameters

This section contains the description of API requests parameters.

<ul>
  <li>limit, offset parameters. Description of the parameters that limit the number of returned records.</li>
  <li>“Lanuage” parameter. Описание параметра языка.</li>
</ul>

## limit, offset parameters

<table>
  <tr>
    <th>Parameter</th>
    <th>Default value</th> 
    <th>Maximum value</th>
  </tr>
  <tr>
    <td>limit</td>
    <td>20</td> 
    <td>500</td>
  </tr>
  <tr>
    <td>offset</td>
    <td>0</td> 
    <td></td>
  </tr>
</table>

## “Lanuage” parameter

<table>
  <tr>
    <th>Parameter</th>
    <th>Default value</th> 
    <th>Possible values</th>
  </tr>
  <tr>
    <td>language</td>
    <td>ru</td> 
    <td>es, en, ru, tr, pl</td>
  </tr>
</table>

# API Methods

In this section, you will find the complete list of methods available in API. The possibilities provided by the API almost completely duplicate the functionality of a publisher on the website. Thus, you obtain a wide range of possibilities for automation of your or publishers’ work process.
<ul>
<li>User information. Using this method, you can obtain the publisher’s private information.</li>

<li>Publisher tickets. This section contains the methods for working with tech support.</li>

<li>News. Using this method, you can obtain the latest news.</li>

<li>Auxiliary information. This section contains the methods that allow obtaining our system’s general data that is not user-specific: the list of languages, categories, currencies, etc.</li>

<li>Referrals. Using this method, you will obtain all available information about publisher’s referrals.</li>

<li>Checking links. This section shows the method for checking the Admitad affiliate links.</li>

<li>Deeplink generator. This section contains the method for generating deeplinks.</li>

<li>Coupons. This section contains methods of working with coupons.</li>

<li>Banners. The set of methods for working with banners.</li>

<li>Landing pages. The set of methods for working with landing pages.</li>

<li>Publisher ad spaces. This section contains the complete set of methods for ad spaces management.</li>

<li>Notifications. Using this method, you may receive or delete notifications sent to the publisher.</li>

<li>Payment requests. This set of methods is required for working with payments to the publisher.</li>

<li>Affiliate programs. Using the methods from this section, you will be able to interact with the affiliate programs on behalf of the publisher.</li>

<li>Publisher reports. The complete set of methods for retrieving all kinds of statistics at publisher’s disposal.</li>

<li>Postback URLs. Methods of postback URLs management.</li>

<li>Retag. Methods of Retag codes management.</li>

<li>Broken links. The set of methods for working with broken links.</li>

<li>Lost orders. Methods of lost order management.</li>

<li>API applications. Набор методов для работы с брокерскими заявками.</li>
</ul>

## User information

### Information about the publisher

## Publisher tickets

## News

## Referrals

## Checking links

## Deeplink generator

## Coupons

## Banners

## Landing pages

## Auxiliary information

## Publisher ad spaces

## Notifications

## Payment requests

## Affiliate programs

## Publisher reports

## Postback URLs

## Retag

## Broken links

## Lost orders

## API applications

# Client Libraries

Here you can find the links to the libraries that may be useful when working with the API.

<ul>
  <li>Python libraries</li>
  <li>PHP libraries</li>
  <li>Javascript libraries</li>
</ul>

## Python libraries

The library allows sending requests to Admitad API, retrieving and updating the access keys. The documentation is available for downloading here:
<ul>
  <li>admitad-python-api (https://github.com/admitad/admitad-python-api)</li>
</ul>

## PHP libraries

The library allows sending requests to Admitad API, retrieving and updating the access keys. The documentation is available for downloading here:

<ul>
  <li>admitad-php-api (https://github.com/admitad/admitad-php-api)</li>
</ul>

## Javascript libraries

This script is embedded in the application page that is run in iframe, and makes it possible to, e.g., adjust the size of the main window to the size of the application; it also contains several other features that facilitate the work for the application developer.

The library and detailed information are available for downloading here:

<ul>
  <li>admitad-js (https://github.com/admitad/admitad-js)</li>
</ul>


