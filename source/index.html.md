---
title: API Reference


toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the The Legend of Pirates Online API! You can use our API to access The Legend of Pirates Online API endpoints, which can get information on news, servers and system status.

# Authentication

* Launcher POSTs a request to `https://api.piratesonline.co/login/`.
* Web responds in one of multiple ways.
* Launcher interprets web response and launches game if successful.
  * API will respond in JSON format.


## Contacting the API

### Headers
All calls to the API should be made via a HTTP ```POST``` to ```https://api.piratesonline.co/login/``` using an urlencoded form. To do this, add ```'Content-type': 'application/x-www-form-urlencoded'``` to your headers.

### Parameters
- For all practical uses, all API requests should be made using the general account parameters.  If the API responds with a request for a two-factor token, submit a new request containing the two-factor parameter.
- The following tables outline the required parameters for both cases:

#### General Accounts
| Params     | Description                                           |
|------------|-------------------------------------------------------|
| username   | The username of the account you desire to login to.   |
| password   | The password of the account you desire to login to.   |

#### Account with Two-factor Authentication

| Params     | Description                                           |
|------------|-------------------------------------------------------|
| username   | The username of the account you desire to login to.   |
| password   | The password of the account you desire to login to.   |
| gtoken     | The account's active two-factor authentication token. |


### API Response

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


## Failure responses
If any one of these responses are received, the login process has ended with an error. The launcher will notify the player that an error has occurred and prompt them with the reason.

### Invalid username/password
The submitted Username/Password was incorrect.

```
status = 1
message = Incorrect username and/or password.
```
```json
{"status": "1", "message": "Incorrect username and/or password."}
```

### Account Banned
This account is banned or disabled.

```
status = 4
message = This account is on hold. Please login to www.piratesonline.co/account/banned_id/ for more information.
```
```json
{"status": "4", "message": "This account is on hold. Please login to www.piratesonline.co/account/banned_id/ for more information."}
```

### Server Closed
The server is closed.

```
status = 5
message = The Legend of Pirates Online is currently closed for an update. We'll be back up soon!
```
```json
{"status": "5", "message": "The Legend of Pirates Online is currently closed for an update. We'll be back up soon!"}
```

### Email Unverified
This account's email hasn't been verified yet.

```
status = 8
message = This account's email hasn't been verified yet. Please check yer email.
```
```json
{"status": "8", "message": "This account's email hasn't been verified yet. Please check yer email."}
```

### No Active Playtime
**Deprecated** no active playtime for the account

```
status = 9
message = Ye do not have an active session right now.  Ye can sign up for one on our website!
```
```json
{"status": "9", "message": "Ye do not have an active session right now.  Ye can sign up for one on our website!"}
```

### Rate Limited
If an account sends too many login requests over a period of time

```
status = 10
message = Ye are trying to login too fast.  Please try again in one minute.
```
```json
{"status": "10", "message": "Ye are trying to login too fast.  Please try again in one minute."}
```

### Arrrmor
Two-step security for geolocation

```
status = 11
message = Ye are trying to login from a new location. Please check yer email.
```
```json
{"status": "11", "message": "Ye are trying to login from a new location. Please check yer email."}
```

## Successful responses

### Partial-success: Two-Step Required
```
status = 3
message = A two-factor authentication code is required to login.
```
```json
{"status": "3", "message": "A two-factor authentication code is required to login."}
```

#### Follow-up:
You must send another request to the login API containing the user-supplied authentication token using the ```gtoken``` parameter.  If this token is invalid and/or expired, you will receive another partial-success login.  You must send the username and password again when sending this follow-up request.

### Successful Login
```
status = 7
message = Have fun!
```
```json
{"status": "7", "message": "Have fun!", "token": "12345678abcdefgh", "gameserver": "prod-gs-protected-agent-1.legendofpiratesonline.com"}
```

## Running the Client
Upon receiving a status 7 response, you will have all of the proper information you need to login on the client.

To login on the client, set the following environment variables containing the information provided by the login API and then run the client.

| Environment Variable | Description                         |
|----------------------|-------------------------------------|
| TLOPO_GAMESERVER     | The gameserver provided by the API. |
| TLOPO_PLAYCOOKIE     | The token provided by the API.      |

