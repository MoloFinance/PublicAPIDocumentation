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

Once you have an access token, you can access the rest of these endpoints by using the `bearer token` authentication header and entering this token as the value.

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
    -H 'Content-Type: application/json' \
    -H 'Host: oauth.molofinance.com' \
    -d '{' \
            '"code": "<AUTH_CODE>"', \
            '"grant_type": "authorization_code"', \
            '"redirect_uri": "<REDIRECT_URI>"', \
        '}'
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

**Send a `POST` request to our token endpoint to exchange an authorization code for an access token**

[https://oauth.molofinance.com/token/](https://oauth.molofinance.com/token/)


## Refresh an access token:
> Using http basic auth in the headers, use your `client_id` as the username and your `client_secret`

> then post the following payload as x-www-form-urlencoded data:

```json
{
    "refresh_token": <REFRESH_TOKEN>,
    "grant_type": "refresh_token",
}
```

```shell
curl -X POST \
    http://oauth.molofinance.com/token/ \
    -H 'Accept: */*' \
    -H 'Authorization: <HTTP_BASIC_AUTH_HEADER>' \
    -H 'Content-Type: application/json' \
    -H 'Host: oauth.molofinance.com' \
    -d '{' \
            '"refresh_token": "<REFRESH_TOKEN>"', \
            '"grant_type": "refresh_token"', \
       '}'
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

**Send a `POST` request to our token endpoint to refresh an access token**
[https://oauth.molofinance.com/token/](https://oauth.molofinance.com/token/)


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
    -H 'Content-Type: application/json' \
    -H 'Host: oauth.molofinance.com' \
    -d '{' \
            '"token": "<TOKEN>",' \
            '"token_type_hint": "refresh_token",' \
       '}'
```

> **Expected response: Status 200**

**Send a `POST` request to our revoke token endpoint to revoke a token.**

[https://oauth.molofinance.com/revoke_token/](https://oauth.molofinance.com/revoke_token/)



# Applications

## Create a new application

```shell
curl -X POST \
    https://partner.molofinance.com/api/v1/applications/ \
    -H 'Accept: */*' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
    -d '{' \
           '"mortgage_type": "new_purchase",' \
           '"borrower_type": "individual_borrower",' \
           '"loan_term": 6,' \
           '"desired_loan_amount": 130000,' \
           '"monthly_rent": 2000,' \
           '"property_value": 200000'
           '"reference_id": "custome_reference_id"' 
       '}'
```

> **Expected response: Status 201**

```json
{
    "id": 1000002346
}
```

**Send a `POST` request to our applications endpoint to create a new application.**

[https://partner.molofinance.com/api/v1/applications/](https://partner.molofinance.com/api/v1/applications/)

You will need to provide the following parameters:

- `property_value` Optional. A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `monthly_rent` Optional. A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `desired_loan_amount` A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `loan_term` An integer number of years.
- `mortgage_type` The type of mortgage the customer wants. A choice of:
    - "new_purchase"
    - "remortgage_current"
    - "remortgage_and_borrow"
- `borrower_type` The type of borrower, a choice of:
    - "individual_borrower"
    - "ltd_company"
- `reference_id` Optional. A lead reference id. 

## Update an application

```shell
curl -X PATCH \
    http://partner.molofinance.com/api/v1/applications/<APPLICATION_ID>/ \
    -H 'Accept: */*' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
    -d '{' \
            '"loan_term": 7', \
       '}'
```


> **Expected response: Status 200**

**Send a `PATCH` request to our applications endpoint to update an application.**

[https://partner.molofinance.com/api/v1/applications/{pk}/](https://partner.molofinance.com/api/v1/applications/{pk}/)

You may send any combination of the following parameters:

- `property_value` Optional. A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `monthly_rent` Optional. A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `desired_loan_amount` A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `loan_term` An integer number of years.
- `mortgage_type` The type of mortgage the customer wants. A choice of:
    - "new_purchase"
    - "remortgage_current"
    - "remortgage_and_borrow"
- `borrower_type` The type of borrower, a choice of:
    - "individual_borrower"
    - "ltd_company"
- `reference_id` Optional. A lead reference id.


## Retrieve a list of applications

```shell
curl -X GET \
    https://partner.molofinance.com/api/v1/applications/ \
    -H 'Accept: */*' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
```

> **Expected response: Status 200**

```json
[
    {
        "id": 1000002171,
        "status": "pending_product_selection",
        "mortgage_type": "new_purchase",
        "borrower_type": "individual_borrower",
        "created_by": 1387,
        "created_at": "2019-05-15T13:05:41.479039Z",
        "updated_at": "2019-10-18T14:20:41.747390Z"
    },
    {
        "id": 226,
        "status": "valid",
        "mortgage_type": "new_purchase",
        "borrower_type": "individual_borrower",
        "created_by": 292,
        "created_at": "2018-05-17T16:56:06.290373Z",
        "updated_at": "2019-10-18T14:20:19.535138Z"
    },
    ...
]
```

**Send a `GET` request to our applications endpoint to retrieve a list of past applications.**

[https://partner.molofinance.com/api/v1/applications/](https://partner.molofinance.com/api/v1/applications/)

You will get list of applications with next parameters:

- `id` Application identifier.
- `status` Application status. A choice of:
    - `stored` - default 
    - `pending_decision`
    - `valid` - DIP valid
    - `denied_dip` - Denied DIP
    - `denied_dip_kyc` - Denied DIP because of failed KYC.           
- `mortgage_type` The type of mortgage the customer wants. A choice of:
    - "new_purchase"
    - "remortgage_current"
    - "remortgage_and_borrow"
- `borrower_type` The type of borrower, a choice of:
    - "individual_borrower"
    - "ltd_company"
- `created_by` Account id which create application. 
- `created_at` Date when application was created.
- `updated_at` Lase application update date.

## Retrieve details of a specific application

```shell
curl -X GET \
    https://partner.molofinance.com/api/v1/applications/ \
    -H 'Accept: */*' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
```

> **Expected response: Status 200**

```json
{
    "id": 1000002171,
    "status": "pending_product_selection",
    "mortgage_type": "new_purchase",
    "borrower_type": "individual_borrower",
    "created_by": 1387,
    "created_at": "2019-05-15T13:05:41.479039Z",
    "updated_at": "2019-10-18T14:20:41.747390Z",
    "desired_loan_amount": "66000.00",
    "property_value": 110000.0,
    "monthly_rent": 1100.0,
    "loan_term": 5,
    "reference_id": "1106683832, 281da6a4-8748-abc3-9b20-5a1d789e340d",
    "dip_pdf": "",
    "applicants": [
        3548,
        3542
    ]
}
```

**Send a `GET` request to our applications endpoint to retrieve details of a specific application.**

[https://partner.molofinance.com/api/v1/applications/{pk}/](https://partner.molofinance.com/api/v1/applications/{pk}/)

You will get detail application response with next parameters:

- `id` Application identifier.
- `status` Application status. A choice of:
    - `stored` - default 
    - `pending_decision`
    - `valid` - DIP valid
    - `denied_dip` - Denied DIP
    - `denied_dip_kyc` - Denied DIP because of failed KYC.           
- `property_value` A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `monthly_rent` A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `desired_loan_amount` A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `loan_term` An integer number of years.
- `mortgage_type` The type of mortgage the customer wants. A choice of:
    - "new_purchase"
    - "remortgage_current"
    - "remortgage_and_borrow"
- `borrower_type` The type of borrower, a choice of:
    - "individual_borrower"
    - "ltd_company"
- `applicants` List of applicants id. More info about [Applicant](#applicant).
- `dip_pdf` Pdf document in **base64** format when [dip](#get-a-decision-in-principle) is `valid` or empty string.
- `created_by` Account id which create application. 
- `created_at` Date when application was created.
- `updated_at` Lase application update date.
- `reference_id` A lead reference id.

## Get a decision in principle

```shell
curl -X POST \
    http://partner.molofinance.com/api/v1/applications/<APPLICATION_ID>/get-dip/ \
    -H 'Accept: */*' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
    -d '{' \
            '"allow_soft_credit_search": "true"', \
       '}'
```

> **Expected response: Status 200**

```json
{
    "decision": "valid"
}
```

**Send a `POST` request to our applications dip endpoint to get a decision in principle.**

[https://partner.molofinance.com/api/v1/applications/{pk}/get-dip/](https://partner.molofinance.com/api/v1/applications/{pk}/get-dip/)

You must send the parameter:

- `allow_soft_credit_search` A boolean, you must send "true" to get a dip

The response will be in the form:

- `decision` Whether Molo can give you a decision in principle. A choice of:
    - "valid" 
    - "denied_dip"
    - "denied_dip_kyc"

## Get a max loan calculation

```shell
curl -X POST \
    http://partner.molofinance.com/api/v1/applications/calculate_max_loan/ \
    -H 'Accept: */*' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
    -d '{' \
            '"mortgage_type":"new_purchase",' \
            '"borrower_type":"individual_borrower",' \
            '"taxable_income":70000,' \
            '"property_value":230000,' \
            '"estimated_rent_per_month":2000,' \
            '"is_ex_local": "true"' \
       '}'
```

> **Expected response: Status 200**

```json
{
    "max_loan": 172500.0
}
```

**Send a `POST` request to our max loan calculation endpoint to retrieve a maximum loand calculation.**

[https://partner.molofinance.com/api/v1/applications/calculate_max_loan/](https://partner.molofinance.com/api/v1/applications/calculate_max_loan/)

You may send any combination of the following parameters:

- `is_new_property` Optional, A boolean
- `is_hmo` Optional, A boolean
- `is_ex_local` Optional, A boolean
- `borrower_type` The type of borrower, a choice of:
    - "individual_borrower"
    - "ltd_company"
- `mortgage_type` The type of mortgage the customer wants. A choice of:
    - "new_purchase"
    - "remortgage_current"
    - "remortgage_and_borrow"
- `property_value`  A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP. Validated to be between £1 and £4,000,000
- `estimated_rent_per_month` A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP. Validated to be between £1 and £999,999,999.
- `taxable_income` Optional. A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP. Validated to be between £30,000 and £999,999,999.

The response will be in the form:

- `max_loan` The maximum loan Molo will make based on the provided criteria.


# Applicant

## Create a new applicant

```shell
curl -X POST \
    http://partner.molofinance.com/api/v1/applicants/ \
    -H 'Accept: application/json' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
    -d '{' \
            '"application": <APPLICATION_ID>,' \
            '"is_lead": "false,' \
            '"taxable_income": 100000,' \
            '"email": "em@il.com",' \
            '"title": "mx",' \
            '"first_name": "Jan,' \
            '"middle_name": "Hankerton",' \
            '"last_name": "Sparksfeld",' \
            '"date_of_birth": "1980-02-12",' \
            '"mobile": "+441615676878",' \
            '"address": {' \
                '"building_number": "123",' \
                '"street": "Fake Street",' \
                '"building_name": "Rose Manor",' \
                '"sub_building_name": "Gatehouse",' \
                '"postal": "NW1 1AL",' \
                '"town": "London"' \
            '}',
            '"is_privacy_policy_accepted": "true",'
       '}'
```

> **Expected response: Status 201**

```json
{
    "id": 3721
}
```

**Send a `POST` request to our applicants endpoint to create a new applicant.**

[https://partner.molofinance.com/api/v1/applicants/](https://partner.molofinance.com/api/v1/applicants/)

You will need to provide the following parameters:

- `application` The ID of the application this applicant should be attached to.
- `is_lead` Is this applicant the lead applicant on this application? Each application requires one and only one lead applicant. A boolean.
- `taxable_income` A decimal value. Up to 11 digits long, including 2 decimal places. This value is presumed to be in GBP.
- `email` The customer's email address.
- `title` The customer's title, one of:
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
- `first_name` Optional. The customer's first name
- `middle_name` Optional. The customer's middle name
- `last_name` Optional. The customer's last name
- `date_of_birth` The customer's date of birth, in ISO 8601 format (yyyy-mm-dd)
- `mobile` The customer's mobile number in the form "+441615676878"
- `address` An address object representing the customer's current address, in the form:
    - `building_number` The building number
    - `street` The street name
    - `building_name` The building name
    - `sub_building_name` The sub-building name
    - `postal` The post code
    - `town` The town
- `is_privacy_policy_accepted` A boolean, you must send "true" to create a new applicant


## Partial update an applicant

```shell
curl -X PATCH \
    http://partner.molofinance.com/api/v1/applicants/<APPLICANT_ID>/ \
    -H 'Accept: application/json' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
    -d '{'
            '"is_lead": "false",' \
            '"taxable_income": 100000,' \
            '"email": "em@il.com",' \
            '"title": "mx",' \
            '"first_name": "Jan,' \
            '"middle_name": "Hankerton",' \
            '"last_name": "Sparksfeld",' \
            '"date_of_birth": "1980-02-12",' \
            '"mobile": "+441615676878",' \
            '"address": {' \
                "building_number": "123",' \
                "street": "Fake Street",' \
                "building_name": "Rose Manor",' \
                "sub_building_name": "Gatehouse",' \
                "postal": "NW1 1AL",' \
                "town": "London"' \
            '}'
       '}'
```

> **Expected response: Status 200**

**Send a `PATCH` request to our applicants endpoint a update an applicant.**

[https://partner.molofinance.com/api/v1/applicants/{pk}/](https://partner.molofinance.com/api/v1/applicants/{pk}/)


## Update an applicant

```shell
curl -X PUT \
    http://partner.molofinance.com/api/v1/applicants/<APPLICANT_ID>/ \
    -H 'Accept: application/json' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
    -d '{'
            '"is_lead": "false",' \
            '"taxable_income": 100000,' \
            '"email": "em@il.com",' \
            '"title": "mx",' \
            '"first_name": "Jan",' \
            '"middle_name": "Hankerton",' \
            '"last_name": "Sparksfeld",' \
            '"date_of_birth": "1980-02-12",' \
            '"mobile": "+441615676878",' \
            '"address": {' \
                "building_number": "123",' \
                "street": "Fake Street",' \
                "building_name": "Rose Manor",' \
                "sub_building_name": "Gatehouse",' \
                "postal": "NW1 1AL",' \
                "town": "London"' \
            '}'
       '}'
```

> **Expected response: Status 200**

**Send a `PUT` request to our applicants endpoint a update an applicant.**

[https://partner.molofinance.com/api/v1/applicants/{pk}/](https://partner.molofinance.com/api/v1/applicants/{pk}/)


## Retrieve details of a specific applicant

```shell
curl -X GET \
    http://partner.molofinance.com/api/v1/applicants/<APPLICANT_ID>/ \
    -H 'Accept: application/json' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -H 'Host: partner.molofinance.com' \
```

> **Expected response: Status 200**

```json
{
    "id": 3721,
    "application": 1000002350,
    "is_lead": false,
    "taxable_income": "100000.00",
    "email": "em@il.com",
    "title": "mx",
    "first_name": "Jan",
    "middle_name": "Hankerton",
    "last_name": "Sparksfeld",
    "date_of_birth": "1980-02-12",
    "mobile": "+441615676878",
    "address": {
        "street": "123",
        "building_number": "Fake Street",
        "building_name": "Rose Manor",
        "sub_building_name": "Gatehouse",
        "postal": "NW1 1AL",
        "town": "London"
    }
}
```

**Send a `GET` request to our applicants endpoint to retrieve a specific applicant.**

[https://partner.molofinance.com/api/v1/applicants/{pk}/](https://partner.molofinance.com/api/v1/applicants/{pk}/)


## Delete a specific applicant

```shell
curl -X DELETE \
    http://partner.molofinance.com/api/v1/applicants/<APPLICANT_ID> \
    -H 'Accept: application/json' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
```

> **Expected response: Status 204**

**Send a `DELETE` request to our applicants endpoint to delete a specific applicant.**

[https://partner.molofinance.com/api/v1/applicants/{pk}/](https://partner.molofinance.com/api/v1/applicants/{pk}/)
