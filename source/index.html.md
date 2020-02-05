---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python

toc_footers:
  - <a href='https://molofinance.com/'>molofinance.com</a>

includes:
  - errors

search: true
---

# Introduction

The Molo API is designed to be a predictable and intuitive interface. We offer both a REST API and webhooks.

# Authentication

The Molo API implements OAuth 2.0 to allow users to log in to applications without exposing their credentials. The process involves several steps:

- Acquire an access token, and optionally a refresh token
- Use the access token to make authenticated requests
- If you were issued a refresh token: refresh the access token when it expires

[//]: # (We don't currently support this endpoint, this is here for completeness sake. Uncomment later.)
[//]: # (##Acquiring an authorization code:)
[//]: # (Send a `GET` request to our authorize endpoint to recieve an authorization code which you can then exchange for an access token.)

[//]: # (> `https://oauth.molofinance.com/authorize/?state=<STATE>&response_type=code&client_id=<CLIENT_ID>`)

[//]: # (> Where `STATE` is your state)
[//]: # (> and `CLIENT_ID` is the ID that Molo has issued you)

[//]: # (```shell)
[//]: # (curl -X GET \)
[//]: # (  'https://oauth.molofinance.com/authorize/?state=%3CSTATE%3E&response_type=code&client_id=%3CCLIENT_ID%3E' \)
[//]: # (  -H 'Postman-Token: 08292e87-c21e-4d93-a49b-43f92c859ccf' \)
[//]: # (  -H 'cache-control: no-cache')
[//]: # (```)


## Exchange an authorization code for an access token:

Send a `POST` request to our token endpoint to exchange an authorization code for an access token
https://oauth.molofinance.com/token/

> Using http basic auth in the headers, use your `client_id` as the username and your `client_secret`

> then post the following payload as x-www-form-urlencoded data:

```
code:<AUTH_CODE>
grant_type:authorization_code
redirect_uri:<REDIRECT_URI>
```


```shell
curl -X POST \
  https://oauth.molofinance.com/token/ \
  -H 'Accept: */*' \
  -H 'Authorization: Basic d1QxWHJ3Q1hHdHJPbmY4WnBBc1R4MW5YQzdsd3hYVVZncmdkZG1ycTozMnd6Rnh1amZvSVk5emowbjY4TkxNWXdEQUhUY0lBVzVBVk0zU1NheTZPUXJtMkxzS0ZIbVRZUHllb2xZcFpZTzE5Mnk1TVgyaTNaazVvVGp1cDlXQkZKUGR0ZHFGdUtHRGtHNlBzUkNWVzB4TURGaTRYeG5DTDRXbmtkc01NVQ==' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Host: oauth.molofinance.com' \
  -d 'code=6Kl461qi2CAtRchWZabDovT3FuOzg2&grant_type=authorization_code&redirect_uri=https%3A%2F%2Fdeveloper-sandbox.website.com%2Foauth-redirect%2Fmolofinance'
```

# API
The Molo partner API can be used by any partner who has obtained an access token.
You can access these endpoints by using the `bearer token` authentication header and entering this token as the value.

## Mortgage applications
Send a GET request to https://partner.molofinance.com/v1/applications/ to see a list of current applications for a user

```shell
curl -X GET \
  https://partner.molofinance.com/v1/applications/ \
  -H 'Authorization: Bearer <ACCESS TOKEN>' \
  -H 'cache-control: no-cache'


```
> Expected response

```json
[
  {
    "id": 123,
    "status": "running_loan",
    "status_display": "Running Loan",
    "application_type": "remortgage_current",
    "application_type_display": "remortgage current balance",
    "desired_loan_amount": 200000,
    "outstanding_balance": 170000,
  },
  {
    "id": 456,
    …
  }
]
```
