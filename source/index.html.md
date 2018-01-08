---
title: API Reference

language_tabs:
  - python

toc_footers:
  - <a href='https://piratesonline.co/'>Return to main website.</a>

includes:
  - errors

search: true
---

# Introduction

```python
# Due to the SSL on our API, you must use
# Python 3 in order to make any API requests.

# If you don't, you'll get this nasty error:
# requests.exceptions.SSLError: [SSL: SSLV3_ALERT_HANDSHAKE_FAILURE] sslv3 alert handshake failure (_ssl.c:661)
```

Welcome to The Legend of Pirates Online API!

You can use our API to access The Legend of Pirates Online API endpoints, which can get information on news, servers and system status.

All API requests must be made using `HTTPS`, **not** `HTTP`.

# Authentication

* `https://api.piratesonline.co/login/`
* Authenticates invoker then returns gameserver information to login on the TLOPO client.
* Launcher uses HTTP `POST` to send a request to the API.
* Responds in JSON format.

## Contacting the API
### Headers
All calls to the API should be made via a HTTP ```POST``` to ```https://api.piratesonline.co/login/``` using an urlencoded form.  To do this, add ```'Content-type': 'application/x-www-form-urlencoded'``` to your headers.

### Parameters
* For all practical uses, all API requests should be made using the general account parameters.
* If the API responds with a request for a two-factor token, submit a new request containing the two-factor parameter.

The following tables outline the required parameters for both cases:

### General Accounts

```python
# ** General Account Login **

# https://pypi.python.org/pypi/requests
import requests 
import urllib

params = urllib.urlencode({'username': 'your_username_here',
                           'password': 'your_password_here'})
headers = {'Content-Type': 'application/x-www-form-urlencoded'}

r = requests.post('https://api.piratesonline.co/login/', data=params, headers=headers)
print r.json()
```

| Params     | Description                                           |
|------------|-------------------------------------------------------|
| username   | The username of the account you desire to login to.   |
| password   | The password of the account you desire to login to.   |

### Account with Two-factor Authentication

```python
# ** 2FA Account Login **

# https://pypi.python.org/pypi/requests
import requests
import urllib

params = urllib.urlencode({'username' : 'your_username_here',
                           'password' : 'your_password_here',
                           'gtoken'   : 'your_2fa_token_here'})
headers = {'Content-Type': 'application/x-www-form-urlencoded'}

r = requests.post('https://api.piratesonline.co/login/', data=params, headers=headers)
print r.json()
```

| Params     | Description                                           |
|------------|-------------------------------------------------------|
| username   | The username of the account you desire to login to.   |
| password   | The password of the account you desire to login to.   |
| gtoken     | The account's active two-factor authentication token. |


## API Response

The API will respond in one of 12 ways.

| Response          |    Code    | Description                                          |
|-------------------|------------|------------------------------------------------------|
| UnknownError      |     0      | An unknown error has occurred.                       |
| Invalid ID/Pass   |     1      | The submitted Username/Password was incorrect.       |
| Server Error      |     2      | A server error has occurred.                         |
| Two Step Required |     3      | This account has two-step authentication enabled.    |
| Account Disabled  |     4      | This account is banned or disabled.                  |
| Server Closed     |     5      | The server is closed.                                |
| IP Blacklisted    |     6      | This IP is banned.                                   |
| Success           |     7      | This account has successfully logged in.             |
| Email Unverified  |     8      | This account's email has not been verified.          |
| No Playtime       |     9      | This account does not have an active playtime.       |
| Rate Limited      |     10     | This account is trying to log in too quickly.        |
| Arrrmor           |     11     | This account is logging in from an unknown loaction. |


### Failure responses
If any one of these responses are received, the login process has ended with an error. The launcher will notify the player that an error has occurred and prompt them with the reason.

```json
/* ACTUAL PROD JSON RESPONSES */

/* INVALID USERNAME/PASSWORD */
{"status": "1",
 "message": "Incorrect username and/or password."}

/* ACCOUNT BANNED */
{"status": "4",
 "message": "This account is on hold. Please login to www.piratesonline.co/account/banned_id/ for more information."}

/* SERVER CLOSED */
{"status": "5",
 "message": "The Legend of Pirates Online is currently closed for an update. We'll be back up soon!"}

/* UNVERIFIED EMAIL */
{"status": "8",
 "message": "This account's email hasn't been verified yet. Please check yer email."}

/* NO ACTIVE SESSION (DEPRECATED) */
{"status": "9",
 "message": "Ye do not have an active session right now.  Ye can sign up for one on our website!"}

/* RATE LIMITED */
{"status": "10",
 "message": "Ye are trying to login too fast.  Please try again in one minute."}

/* ARRMOR */
{"status": "11",
 "message": "Ye are trying to login from a new location. Please check yer email."}
```

### Status 1: Invalid username/password
The submitted Username/Password was incorrect.

### Status 4: Account Banned
This account is banned or disabled.

### Status 5: Server Closed
The servers are currently closed for an update.

### Status 8: Email Unverified
This account's email hasn't been verified yet.

### Status 9: No Active Playtime
**Deprecated** No active playtime for the account.

### Status 10: Rate Limited
If an account sends too many login requests over a period of time.

### Status 11: Arrrmor
Two-step security for geolocation


### Successful responses

### Partial-success: Two-Step Required

```json
/* PARTICALLY SUCCESSFUL LOGIN */
{"status": "3", "message": "A two-factor authentication code is required to login."}
```

You must send another request to the login API containing the user-supplied authentication token using the ```gtoken``` parameter.  If this token is invalid and/or expired, you will receive another partial-success login.  You must send the username and password again when sending this follow-up request.

### Successful Login

```json
/* SUCCESSFUL LOGIN */
{"status": "7", "message": "Have fun!", "token": "12345678abcdefgh", "gameserver": "prod-gs-protected-agent-1.legendofpiratesonline.com"}
```

You have successfully authenticated with the API.

You have received the following API response and are now able to run the client using the `token` and `gameserver` provided.

## Running the Client

Upon receiving a status 7 response, you will have all of the proper information you need to login on the client.

To login on the client, set the following environment variables containing the information provided by the login API and then run the client.

```python
import os

os.environ['TLOPO_GAMESERVER'] = url_from_server
os.environ['TLOPO_PLAYCOOKIE'] = cookie_from_server

# Open client here.
```

| Environment Variable | Description                         |
|----------------------|-------------------------------------|
| TLOPO_GAMESERVER     | The gameserver provided by the API. |
| TLOPO_PLAYCOOKIE     | The token provided by the API.      |

