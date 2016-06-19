# TLOPO Login API Documentation

This file will explain exactly how to use the login API, and how the general launcher works.

## In a nutshell

All responses will be provided in JSON format.

* Launcher POSTs a request to `https://api.piratesonline.co/login/`.
* Web responds in one of multiple ways.
* Launcher interprets web response and launches game if successful.

# Calling the API

### Headers
All calls to the API should be made via a HTTP ```POST``` to `https://api.piratesonline.co/login/` using an urlencoded form. To do this, add ```'Content-type': 'application/x-www-form-urlencoded'``` to your headers.

### Parameters
| Params     | Description                                        |
|------------|----------------------------------------------------|
| username   | The username of the account you desire to login to.|
| password   | The password of the account you desire to login to.|

### API Response

The API will respond in one of 9 ways.

| Response        |    Code    | Description                                       |
|-----------------|------------|---------------------------------------------------|
| UnknownError    |     0      | An unknown error has occurred.                    |
| Invalid ID/Pass |     1      | The submitted Username/Password was incorrect.    |
| ServerError     |     2      | A server error has occurred.                      |
| TwoStepRequired |     3      | This account has two-step authentication enabled. |
| AccountDisabled |     4      | This account is banned or disabled.               |
| ServerClosed    |     5      | The server is closed.                             |
| IPBlacklisted   |     6      | This IP is banned.                                |
| Success         |     7      | This account has successfully logged in.          |
| EmailUnverified |     8      | This accounts email has not been verified.        |


## Failure responses
If any one of these responses are received, the login process has ended with an error. The launcher will notify the end-user that an error has occurred and prompt them with the reason.
### Invalid username/password
```
status = 1
message = Incorrect username and/or password.
```
```
{"status": "1", "message": "Incorrect username and/or password."}
```
### Account Banned
```
status = 4
message = This account is currently banned. Please log into our website for more information.
```
```
{"status": "4", "message": "This account is currently banned. Please log into our website for more information."}
```

### Server Closed
```
status = 5
message = The Legend of Pirates Online is currently closed for an update. We'll be back up soon!
```
```
{"status": "5", "message": "The Legend of Pirates Online is currently closed for an update. We'll be back up soon!"}
```
### Email Unverified
```
status = 8
message = This account hasn't been verified yet. Please check your email.
```
```
{"status": "8", "message": "This account hasn't been verified yet. Please check your email."}
```

## Successful responses

### Partial-success: Two-Step Required
```
status = 3
message = A Google Authenticator code is required to login.
```
```
{"status": "3", "message": "A Google Authenticator code is required to login."}
```
### Successful Login
```
status = 7
message = OK
```
```
{"status": "7", "message": "OK", "token": "login-cookie", "gameserver": "localhost"}
```

The user can now launch the game! Set the environment variables for the gameserver and the cookie, and then run the game!

| Variable         | Description                        |
|------------------|------------------------------------|
| TLOPO_GAMESERVER | The gameserver to connect to.      |
| TLOPO_PLAYCOOKIE | The cookie provided by the server. |
