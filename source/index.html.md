---
title: API Reference

language_tabs:
  - python

toc_footers:
  - <a href='https://tlopo.com/'>Return to main website.</a>

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

## Login API

* `https://api.tlopo.com/login/`
* Authenticates invoker then returns gameserver information to login on the TLOPO client.
* Launcher uses HTTP `POST` to send a request to the API.
* Responds in JSON format.

### Headers
All calls to the API should be made via a HTTP ```POST``` to ```https://api.tlopo.com/login/``` using an urlencoded form.  To do this, add ```'Content-type' : 'application/x-www-form-urlencoded'``` to your headers.

### Parameters
* For all practical uses, all API requests should be made using the general account parameters.
* If the API responds with a request for a two-factor token, submit a new request containing the two-factor parameter.

The following tables outline the required parameters for both cases:

### General Accounts

> Sample Code - General Account

```python
# https://pypi.python.org/pypi/requests
import requests 
import urllib

params = urllib.urlencode({'username' : 'your_username_here',
                           'password' : 'your_password_here'})
headers = {'Content-Type' : 'application/x-www-form-urlencoded'}

r = requests.post('https://api.tlopo.com/login/', data=params, headers=headers)
print(r.json())
```

| Params     | Description                                           |
|------------|-------------------------------------------------------|
| username   | The username of the account you desire to login to.   |
| password   | The password of the account you desire to login to.   |

### Account with Two-factor Authentication

> Sample Code - 2FA Account

```python
# https://pypi.python.org/pypi/requests
import requests
import urllib

params = urllib.urlencode({'username' : 'your_username_here',
                           'password' : 'your_password_here',
                           'gtoken'   : 'your_2fa_token_here'})
headers = {'Content-Type' : 'application/x-www-form-urlencoded'}

r = requests.post('https://api.tlopo.com/login/', data=params, headers=headers)
print(r.json())
```

| Params     | Description                                           |
|------------|-------------------------------------------------------|
| username   | The username of the account you desire to login to.   |
| password   | The password of the account you desire to login to.   |
| gtoken     | The account's active two-factor authentication token. |

### Security Note

We strongly recommend requiring players to enter their username and password each time they use a launcher.  If an unauthorized user gains access to another player's account and violates the Terms of Service, the account owner will still be held responsible.

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
| Arrrmor           |     11     | This account is logging in from an unknown location. |


### Failure responses
If any one of these responses are received, the login process has ended with an error. The launcher will notify the player that an error has occurred and prompt them with the reason.

> JSON Responses

```json
/* INVALID USERNAME/PASSWORD */
{"status": 1,
 "message": "Incorrect username and/or password."}

/* ACCOUNT BANNED */
{"status": 4,
 "message": "This account is on hold. Please login to www.tlopo.com/account/banned_id/ for more information."}

/* SERVER CLOSED */
{"status": 5,
 "message": "The Legend of Pirates Online is currently closed for an update. We'll be back up soon!"}

/* UNVERIFIED EMAIL */
{"status": 8,
 "message": "This account's email hasn't been verified yet. Please check yer email."}

/* NO ACTIVE SESSION (DEPRECATED) */
{"status": 9,
 "message": "Ye do not have an active session right now.  Ye can sign up for one on our website!"}

/* RATE LIMITED */
{"status": 10,
 "message": "Ye are trying to login too fast.  Please try again in one minute."}

/* ARRMOR */
{"status": 11,
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
***Deprecated*** - No active playtime for the account.

### Status 10: Rate Limited
If an account sends too many login requests over a period of time.

### Status 11: Arrrmor
Two-step security using geolocation data.  Read more [here](https://tlopo.com/help/arrrmor/).


### Successful responses

### Partial-success: Two-Step Required

> JSON Response - Partical Success

```json
{"status": "3",
 "message": "A two-factor authentication code is required to login."}
```

You must send another request to the login API containing the user-supplied authentication token using the ```gtoken``` parameter.  If this token is invalid and/or expired, you will receive another partial-success login.  You must send the username and password again when sending this follow-up request.

### Successful Login

> JSON Response - Success

```json
{"status": "7",
 "message": "Have fun!",
 "token": "12345678abcdefgh",
 "gameserver": "prod-gs-protected-agent-1.tlopo.com"}
```

You have successfully authenticated with the API.

You have received the following API response and are now able to run the client using the `token` and `gameserver` provided.

### Running the Client

Upon receiving a status 7 response, you will have all of the proper information you need to login on the client.

To login on the client, set the following environment variables containing the information provided by the login API and then run the client.

> Sample Code

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

# Blog Posts

* Returns information on the latest news.
* All blog posts APIs are invoked using `HTTP GET`


## Launcher News API

* `https://api.tlopo.com/launcher`
* Returns a pre-formatted HTML document which is rendered inside TLOPO's launchers under "Game News".
  * This document contains the latest blog posts.

### Calling the API
To contact the API, submit a HTTPS GET request to the API URL.

> Sample Code

```python
# https://pypi.python.org/pypi/requests
import requests 

r = requests.get('https://api.tlopo.com/launcher/')
print(r.text)
```

### API Response
The API will respond with a pre-formatted HTML document.  This API is used inside TLOPO's launchers to render the 10 most recent blog posts.

The HTML responded will not contain any kind of background.  The intention is for our launchers to overlay this HTML on the 'Game News' section, thus having a background provided there.


## News Feed API

* `https://api.tlopo.com/news/feed/<number_of_posts>`
* Returns the latest news posts.
* Responds in JSON format.

### Calling the API
To contact the API, submit a HTTPS GET request to the API URL.

> Sample Code

```python
# https://pypi.python.org/pypi/requests
import requests 

r = requests.get('https://api.tlopo.com/news/feed/')
print(r.text)
```

By default, the URL will respond with the latest 5 news posts.  If you wish to receive a different amount, such as the latest 10, you must submit your GET request with the following structure: `https://api.tlopo.com/news/feed/10`.

### API Response
The API will respond a list of JSON objects.  Each JSON object will have the following keys:

> JSON Response

```json
{"url": "https://tlopo.com/news/post/126/",
 "date": "2017-12-13 19:00:00",
 "author": "John Carver",
 "id": 126,
 "title": "Twelve Days of Celebration!"}
```

|      Key      | Value                                                  |
|---------------|--------------------------------------------------------|
| author        | This is the author of the blog post.                   |
| date          | This is the date and time the blog post was published. |
| id            | This is the blog post's id/number.                     |
| title         | This is the title of the blog post.                    |
| url           | This is the direct URL to the blog post.               |

## Release Notes Feed API

* `https://api.tlopo.com/releases/feed/<number_of_posts>`
* Returns the latest releases.
* Responds in JSON format.

### Calling the API
To contact the API, submit a HTTPS GET request to the API URL.

> Sample Code

```python
# https://pypi.python.org/pypi/requests
import requests

r = requests.get('https://api.tlopo.com/releases/feed/')
print(r.text)
```

By default, the URL will respond with the latest 5 releases.  If you wish to receive a different amount, such as the latest 10, you must submit your GET request with the following structure: `https://api.tlopo.com/releases/feed/10`.

### API Response
The API will respond a list of JSON objects. Each JSON object will have the following keys:

> JSON Response

```json
{"url": "https://tlopo.com/release/4.3.2",
 "date": "2017-12-13 19:00:00",
 "version": "4.3.2"}
```

|      Key      | Value                                                     |
|---------------|-----------------------------------------------------------|
| date          | This is the date and time the release note was published. |
| version        | This is the release note's version number.                |
| url           | This is the direct URL to the release note.               |

## News Notification API

* `https://api.tlopo.com/news/notification`
* Returns the current news banner on the TLOPO website.
* Responds in JSON format.

### Calling the API
To contact the API, submit a HTTPS GET request to the API URL.

> Sample Code

```python
# https://pypi.python.org/pypi/requests
import requests 

r = requests.get('https://api.tlopo.com/news/notification/')
print(r.text)
```

### API Response
If there is an active banner, the API will respond a JSON object with the following keys:

> JSON Response

```json
{"message": "The Legend of Pirates Online is
             currently closed for an update.
             We'll be back up soon!",
 "datetime": "2018-01-07 19:33"}
```

|      Key      | Value                                               |
|---------------|-----------------------------------------------------|
| datetime      | This is the date and time the banner was published. |
| message       | This is the message inside the banner.              |

If there is not an active banner, the API will respond an empty JSON object.

# Downloads

* Handles downloads for game client.
* API invoked with HTTP GET request.
* May want to handle requests with a stream for big files.


## Download API
* Different download servers depending on game distribution.
* Operating system name in download URL.
* The section on the [patcher JSON](https://thelegendofpiratesonline.github.io/documentation/#patcher-json) describes client topology.

### Download URLs

> Sample Code - Windows Download Server

```python
# https://pypi.python.org/pypi/requests
import requests 

r = requests.get('https://download.tlopo.com/win32/resources/default/phase_3.mf.bz2')
print(r.content)
```

> Sample Code - Stream Request

```python
# https://pypi.python.org/pypi/requests
import requests 

r = requests.get('https://download.tlopo.com/win32/resources/default/phase_3.mf.bz2', stream=True)
with open(filename, 'wb') as content:
    for chunk in r.iter_content(chunk_size=128):
        content.write(chunk)
```

> Sample Code - Decompress bz2

```python
# https://pypi.python.org/pypi/requests
import requests
from bz2 import BZ2Decompressor

r = requests.get('https://download.tlopo.com/win32/resources/default/phase_3.mf.bz2', stream=True)
decompressor = BZ2Decompressor()
with open('resources/default/phase_3.mf', 'wb') as content:
    for chunk in r.iter_content(chunk_size=128):
        content.write(decompressor.decompress(chunk))
```

Download urls must be built depending on the host operating system, the filepath, and the file followed by `.bz2`. 

* `https://download.tlopo.com/<operating_system>/<file_path>/<file>.bz2`
* Filepath and file must be read from the [patcher JSON](https://thelegendofpiratesonline.github.io/documentation/#patcher-json).
* Download API will respond with a binary in compressed `.bz2` format, this must be decompressed for general use.


|  Distribution         | Operating System |  Download Server and OS Name            |
|-----------------------|------------------|-----------------------------------------|
|  Live (Prod) Server   |      Windows     | https://download.tlopo.com/win32/       |
|  Live (Prod) Server   |      MacOS       | https://download.tlopo.com/mac/         |
|  Live (Prod) Server   |      Linux       | Not Available                           |
|  Test Server          |      Windows     | https://download-test.tlopo.com/win64/  |
|  Test Server          |      MacOS       | https://download-test.tlopo.com/mac/    |
|  Test Server          |      Linux       | https://download-test.tlopo.com/linux2/ |
|  Dev (QA) Server      |      Windows     | https://download-dev.tlopo.com/win64/   |
|  Dev (QA) Server      |      MacOS       | https://download-dev.tlopo.com/mac/     |
|  Dev (QA) Server      |      Linux       | https://download-dev.tlopo.com/linux2/  |

Note: Inconsistencies due to Test/QA running on ["Pypperoni" compiler](https://github.com/Pypperoni/pypperoni) codebase. Live files will be very different from Test/QA. Installing to same directory may result in issues.

## Patcher JSON

* `patcher.json` describes a client installation.
* `https://download.tlopo.com/<operating_system>/patcher.json`
* Used to get names and locations of all other client files on the download server.
* Reference [Download APIs](https://thelegendofpiratesonline.github.io/documentation/#download-api) section for proper paths.

### Calling the API

>Sample Code - Windows Download Server

```python
# https://pypi.python.org/pypi/requests
import requests

r = requests.get('https://download.tlopo.com/win32/patcher.json')
print(r.text)
```

To contact the API, submit a HTTPS GET request to the API URL.

### API Response 

>JSON Response - Windows Download Server

```json
{"files": {
        "resources/default/phase_3.mf": {
            "path": "resources/default",
            "hash": "5da15c4",
            "patches": [
                "phase_3.mf_f1bae71_5da15c4.patch",
                "phase_3.mf_96121de_f1bae71.patch"
            ]
        },
        ...
    }
"launcher-version": "1.4.0", 
"server-version": "1.24.8", 
"mac_launcher_version": "1.5.2"}
```

The API will respond with a large JSON object containing 4 keys; 3 for versions, and the 4th key "files". The files top-level key contains sub-keys for every file for a client installation.

The name of the sub-key contains the path and file name. For example, `resources/default/` is the path and `phase_3.mf` is the file. These paths are to be used for both when saving the files to the client, and when downloading from from the server.

For instance, to download the `phase_3.mf` file we would call `https://download.tlopo.com/win32/resources/default/phase_3.mf.bz2`. Refer to the Download API section for more information.

Each file key has the following sub-keys:

| Key     | Value                                 |
|---------|---------------------------------------|
| path    | The path for download and saving      |
| hash    | SHA256 file hash - 1st 7 characters   |
| patches | Patch files, see the patching section |

## Patching

* Sometimes a file may have accompanying `.patch` files with it. 
* These are specified in the [Patcher JSON](https://thelegendofpiratesonline.github.io/documentation/#patcher-json) section.
* Small files to patch resources, save on bandwidth. 

### Downloading a Patch
Use the standard download API format along with `patcher/<PATCHFILE.patch>.bz2`

For example, `https://download.tlopo.com/win32/patches/phase_3.mf_f1bae71_5da15c4.patch`

### Applying a Patch

TLOPO's standard launcher comes bundled with `patcher.exe` or `patcher.app`, a command line tool to apply patches.

`patcher <patch_file> <old_file>`

# Gameserver

## Ocean API

* `https://api.tlopo.com/shards`
* Reports the status of each ocean with information on current events, such as an active invasion.
* Responds in JSON format.

### Calling the API
To contact the API, submit a HTTPS GET request to the API URL.

> Sample Code

```python
# https://pypi.python.org/pypi/requests
import requests 

r = requests.get('https://api.tlopo.com/shards/')
print(r.text)
```

### API Response
The API will respond a large JSON object containing numerous keys.  Each top-level key is the ocean's "base channel", which differentiates each server from one another inside TLOPO's internal network.

For example, `401000000` is Abassa and `413000000` is Poderoso.

Each of these keys will have the value of another JSON object.  That object contains the following keys:

### Ocean Information

|      Key      | Value                                              |
|---------------|----------------------------------------------------|
| available     | This is whether or not the ocean is enterable.     |
| created       | This is the time the server started in epoch.      |
| fleet         | This is information regarding any active fleets.   |
| invasion      | This is information regarding any active invasion. |
| name          | This is the name of the ocean.                     |
| population    | This is the ocean's current population count.      |

> JSON Response

```json
{"401000000": {"available": true,
               "name": "Abassa",
               "created": 1515367988,
               "fleet": {
                   "started": 1515371588.921096,
                   "shipsRemaining": 1,
                   "state": "deployed",
                   "type": "qar"},
               "invasion": {},
               "population": 271}}
```

`fleet` and `invasion` both will contain a JSON object value which contains information regarding each of their statuses respectively.

### Fleets
|      Key       | Value                                              |
|----------------|----------------------------------------------------|
| shipsRemaining | This is number of remaining ships in the fleet.    |
| started        | This is the time the fleet started in epoch.       |
| state          | This is the current status of the fleet.           |
| type           | This is the type of fleet currently sailing.       |

### Invasions
This feature is still under development.  When invasions are released we will update this API doc.


# System Services

## Online Status API
* `https://api.tlopo.com/system/status`
* Returns information regarding each of TLOPO's services and their online status.
* Responds in JSON format.

### Calling the API
To contact the API, submit a HTTPS GET request to the API URL.

> Sample Code

```python
# https://pypi.python.org/pypi/requests
import requests 

r = requests.get('https://api.tlopo.com/system/status/')
print(r.text)
```

### API Response
The API will respond with a large JSON object.  This object will have the following keys:

|      Key       | Value                                                                 |
|----------------|-----------------------------------------------------------------------|
| message        | This is a special message when our system status cannot be displayed. |
| notices        | This is the active notices posted our system status.                  |
| servers        | This is a listing of each server and their status.                    |
| status         | This is the current overall status of our services.                   |
> JSON Response

```json
{
    "status": 1,
    "servers": {
        "web": [
            {"status": 1, "name": "API 2"},
            ...
        ],
        "oceans": [
            {"status": 1, "name": "Valor"},
            ...
        ],
        "gameserver_functions": [
            {"status": 1, "name": "Inventory Manager"},
            ...
        ],
        "client_agents": [
            {"status": 1, "name": "Connection Agent 3"},
            ...
        ]
    }
    "notices": {
        "2018-01-05 11:03:45.955882": {
            "text": "This is a published notice regarding a widespread outage.",
            "flag": 16
        }
        ...
    }
    "message": "If the status cannot be shown, this message will explain why."
}
```

### Notices
The `notices` key will contain additional keys.  Their values will have all active system status notices as a timestamp key.  These notices give information in update form regarding outages, updates, or other detected system issues.  Those timestamp keys will have the following values:

| Key  | Value                                |
|------|--------------------------------------|
| text | The message published in the notice. |
| flag | The type of notice published.        |

There are currently 5 types of flags.  Each flag represents a different type of notice, as shown below:

| Flag | Description                                |
|------|--------------------------------------------|
|  1   | All systems are alive and operational.     |
|  2   | Non-error information regarding status.    |
|  4   | Update information regarding prior status. |
|  8   | Information regarding an isolated error.   |
|  16  | Information regarding a widespread outage. |

### Servers

The `servers` key will contain a JSON object value which breaks down each server into their respective category:

|       Category       | Description                          |
|----------------------|--------------------------------------|
| client_agents        | TLOPO's Client/Connection Agents     |
| gameserver_functions | TLOPO's Internal Gameserver Services |
| oceans               | TLOPO's Oceans In-game               |
| web                  | TLOPO's Web Services                 |

Each of these categories are keys.  Each key will have a list value containing multiple JSON objects.  Each of objects are the servers in that category. They will have following information:

|   Key    | Value                                 |
|----------|---------------------------------------|
| name     | The name of the server.               |
| status   | The server's online status as 1 or 0. |

