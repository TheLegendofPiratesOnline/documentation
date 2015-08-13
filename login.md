# TLOPO Login API Documentation

This file will explain exactly how to use the login API, and how the general launcher works.

## In a nutshell

All responses will be provided in JSON format.

* Launcher POSTs a request to api.piratesonline.co/login/
* Web responds in one of multiple ways
* Launcher interprets web response and launches game if successful


# Calling the API

### Headers
All calls to the API should be made via ```POST``` to api.piratesonline.co/login/ using a urlencoded form. To do this, add ```'Content-type': 'application/x-www-form-urlencoded'``` to your headers.

### Parameters
| Params     | Description                                        |
|------------|----------------------------------------------------|
| username   | The username of the account you desire to login to |
| password   | The password of the account you desire to login to |

### API Response

The API will respond in one of 9 ways.

| Response        |    Code    | Description                                       |
|-----------------|------------|---------------------------------------------------|
| UnknownError    |     0      | An unknown error has occurred.                    |
| Invalid ID/Pass |     1      | The submitted Username/Password was incorrect.    |
| ServerError     |     2      | A server error has occurred.                      |
| TwoStepRequired |     3      | This account has two-step authentication enabled. |
| AccountDisabled |     4      | This account is banned.                           |
| ServerClosed    |     5      | The server is closed.                             |
| IPBlacklisted   |     6      | This IP is banned.                                |
| Success         |     7      | This account has successfully logged in           |
| EmailUnverified |     8      | This accounts email has not been verified         |


## Failure responses
If any one of these responses are received, the login process has ended with an error. The launcher will notify the end-user that an error has occurred and prompt them with the reason.
### Invalid username/password
```
status = 1
message = The username or password is incorrect.
```
```
{"status": "1", "message": "The username or password is incorrect."}
```
### Account Banned
```
status = 4
message = Your account is banned. Please try to login from website for more info.
```
```
{"status": "4", "message": "Your account is banned. Please try to login from website for more info."}
```

### Server Closed
```
status = 5
message = The server is currently closed.
```
```
{"status": "5", "message": "The server is currently closed."}
```
### Email Unverified
```
status = 8
message = This account has not been verified yet. Please check your email.
```
```
{"status": "8", "message": "This account has not been verified yet. Please check your email."}
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
{"status": "7", "message": "OK", "token": "login-token", "gameserver": "localhost"}
```

The user can now launch the game! Set the environment variables for the gameserver and the token and then boot the game.

| Variable         | Description                        |
|------------------|------------------------------------|
| TLOPO_GAMESERVER | The gameserver to connect to.      |
| TLOPO_PLAYCOOKIE | The token provided by the server.  |
