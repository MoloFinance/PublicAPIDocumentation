---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL

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
> Using http basic auth in the headers, use your `client_id` as the username and your `client_secret`

> then post the following payload as x-www-form-urlencoded data:

```json
{
    "code": <AUTH_CODE>,
    "grant_type": "authorization_code",
    "redirect_uri": <REDIRECT_URI>,
}
```


```shell
curl -X POST \
  https://oauth.molofinance.com/token/ \
  -H 'Accept: */*' \
  -H 'Authorization: <HTTP_BASIC_AUTH_HEADER>' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Host: oauth.molofinance.com' \
  -d 'code=<AUTH_CODE>&' \
     'grant_type=authorization_code&' \
     'redirect_uri=<REDIRECT_URI>'
```

> **Expected response: Status 200**

```json
{
    "access_token": <ACCESS_TOKEN>,
    "expires_in": 36000,
    "token_type": "Bearer",
    "scope": "read write",
    "refresh_token": <REFRESH_TOKEN>,
}
```

Send a `POST` request to our token endpoint to exchange an authorization code for an access token
https://oauth.molofinance.com/token/


## Refresh an access token:
> Using http basic auth in the headers, use your `client_id` as the username and your `client_secret`

> then post the following payload as x-www-form-urlencoded data:

```json
{
    "refresh_token":<REFRESH_TOKEN>,
    "grant_type":"refresh_token",
}
```

```shell
curl -X POST \
  http://oauth.molofinance.com/token/ \
  -H 'Accept: */*' \
  -H 'Authorization: <HTTP_BASIC_AUTH_HEADER>' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Host: oauth.molofinance.com' \
  -d 'refresh_token=<REFRESH_TOKEN>&' \
     'grant_type=refresh_token'
```

> **Expected response: Status 200**

```json
{
    "access_token": <NEW_ACCESS_TOKEN>,
    "expires_in": 36000,
    "token_type": "Bearer",
    "scope": "read write",
    "refresh_token": <NEW_REFRESH_TOKEN>
}
```
Send a `POST` request to our token endpoint to refresh an access token
https://oauth.molofinance.com/token/


## Revoke an access token:
> Using http basic auth in the headers, use your `client_id` as the username and your `client_secret`

> then post the following payload as x-www-form-urlencoded data:

```json
{
    "token": <TOKEN>,
    "token_type_hint": ["access_token"|"refresh_token"],
}
```

```shell
curl -X POST \
  http://oauth.molofinance.com/revoke_token/ \
  -H 'Accept: */*' \
  -H 'Authorization: <HTTP_BASIC_AUTH_HEADER>' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Host: oauth.molofinance.com' \
  -d 'token=<TOKEN>&' \
     'token_type_hint=refresh_token'
```

> **Expected response: Status 200**

Send a `POST` request to our revoke token endpoint to revoke a token.

https://oauth.molofinance.com/revoke_token/




# API
The Molo partner API can be used by any partner who has obtained an access token.
You can access these endpoints by using the `bearer token` authentication header and entering this token as the value.

## Mortgage applications
```shell
curl -X GET \
  https://partner.molofinance.com/v1/applications/ \
  -H 'Authorization: Bearer <ACCESS TOKEN>' \
  -H 'cache-control: no-cache'


```
> **Expected response: Status 200**

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
Send a GET request to https://partner.molofinance.com/v1/applications/ to see a list of current applications for a user
