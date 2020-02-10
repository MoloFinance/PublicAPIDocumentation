---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

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

```
code:<AUTH_CODE>
grant_type:authorization_code
redirect_uri:<REDIRECT_URI>
```
```shell
curl -X POST \
  https://oauth.molofinance.com/token/ \
  -H 'Accept: */*' \
  -H 'Authorization: <BASIC_AUTH_HEADER_CODE>' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'code=6Kl461qi2CAtRchWZabDovT3FuOzg2&
      grant_type=authorization_code&
      redirect_uri=https%3A%2F%2Fdeveloper-sandbox.website.com%2Foauth-redirect%2Fmolofinance'
```
Send a `POST` request to our token endpoint to exchange an authorization code for an access token
https://oauth.molofinance.com/token/






# API
The Molo partner API can be used by any partner who has obtained an access token.
You can access these endpoints by using the `bearer token` authentication header and entering this token as the value.

## Request decision in principal
```shell
curl -X POST \
  https://partner.molofinance.com/request_dip/ \
  -H 'Accept: */*' \
  -H 'Authorization: <BASIC_AUTH_HEADER_CODE>' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'borrower_type=individual_borrower&customers=%5B%7B%22email%22%3A%22whatever%40thing.com%22%2C%20%22title%22%3A%22stuff%22%7D%2C%20%7B%22email%22%3A%20%22another%40one.com%22%2C%20%22title%22%3A%20%22mr%22%7D%5D&desired_loan_amount=200000&monthly_rent=2000&mortgage_type=new_purchase&property_value=500000&loan_term=15&is_uk_resident=true&is_bankrupt=false&is_investment=false&btl_mortgages_count=2'
```

If you are a broker, you can request a decision in principal (DIP) on behalf of your customer.
NB: This will include running a soft credit check on your customer.

You will need to provide the following parameters:

- `borrower_type` The type of borrower, a choice of:
  - "individual_borrower"
  - "ltd_company"
- `consents` **(Can we assume consent here, or will Mojo need to explicitely pass it to us?)**
- `customers` This is a list of customer objects, one for each applicant. These objects consist of:
    - `taxable_income` A decimal value with 2 decimal places. This value is presumed to be in GBP
    - `email` The customer's email address
    - `title` The customer's title, one of **(Do we really need this?)**
        - "mr"
        - "mx"
        - "mrs"
        - "miss"
        - "ms"
        - "sir"
        - "dr"
        - "lady"
        - "lord"
        - "other"
    - `first_name` The customer's first name
    - `last_name` The customer's last_name
    - `date_of_birth` The customer's date of birth, in ISO 8601 format (yyyy-mm-dd)
    - `mobile` The customer's mobile number in the form "+441615676878"
    - `address` An address object representing the customer's address, in the form:
        - `address_type` The customer's address type, a choice of:
            - "current"
            - "previous"
        - `country_iso` The ISO code for the country
        - `street` The street name
        - `building_number` The building number
        - `building_name` The building name
        - `sub_building_name` The sub-building name
        - `postal` The post code
        - `town` The town
        - `start_month` The month the customer moved into this address
        - `start_year` The year the customer moved into this address
        - `end_month` The month the customer moved out of this address
        - `end_year` The year the customer moved out of this address
  - `desired_loan_amount` A decimal value with 2 decimal places. This value is presumed to be in GBP
  - `monthly_rent` A decimal value with 2 decimal places. This value is presumed to be in GBP
  - `mortgage_type` The type of mortgage the customer wants. A choice of:
    - "new_purchase"
    - "remortgage_current"
    - "remortgage_and_borrow"
  - `property_value` A decimal value with 2 decimal places. This value is presumed to be in GBP
  - `loan_term` An integer number of years
  - `is_uk_resident` Has the customer been a UK resident for at least 3 years, boolean
  - `is_bankrupt` Has the customer been bankrupt in the last 6 years, boolean
  - `is_investment` Is the property for investing purposed? boolean
  - `btl_mortgages_count` How many other buy-to-let mortgages does the customer already have?

## Mortgage applications
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
Send a GET request to https://partner.molofinance.com/v1/applications/ to see a list of current applications for a user
