---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

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


Acquiring an access token: _Process to be defined_


# API

> Endpoints which Starling will use to request data:

> (Assumption that the user token is passed every time that contains the customer identifier)

```shell
GET	/v1/applications/

Response:
```
```json
[
  {
    "application_id": 123,
    "application_type": "individual",
    "status": "running_loan",
    "loan_amount": 200000,
    "current_loan_amount": "None",
  },
  {
    "application_id": 456,
    …
  }
]
```
```
GET	/v1/applications/current/

Response:
```
```json
[
  {
    "application_id": 123,
    "application_type": "individual",
    "status": "running_loan",
    "loan_amount": 200000,
    "current_loan_amount": "None",
  }
]
```

```
GET	/v1/applications/<id>/

Response:
```
```json
[
  {
    "application_id": 123,
    "application_type": "individual",
    "status": "running_loan",
    "loan_amount": 200000,
    "current_loan_amount": "None",
  }
]
```

```
TODO DIP (To be used with Mojo)
```

> Postman collection examples?

`(To be used with starling)`

Proposed implementation:
Our API will live on a separate subdomain. Name TBC, here we’ll just call it `partner-api.molofinance.com`

We will, of course, version it.

Starling’s "2-way" OAuth flow requires that we can perform as either side of a standard OAuth flow. That is to say, it is just 2 separate OAuth implementations, one which acts as a client and one which acts as an Identity Provider.
These individual flows are standard looking and so long as we don’t wire their endpoints together too tightly, they should be reusable for future partners who require OAuth.
I will not detail these OAuth endpoints here.

# Architecture 

Certification: Public certificate (.crt file)
Authentication: 
  - OAuth2.0 with Starling
  - Client secret JWT for non customer auth journey. 
API Gateway: AWS API Gateway
