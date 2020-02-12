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
**Send a `POST` request to our token endpoint to exchange an authorization code for an access token**
https://oauth.molofinance.com/token/



# API
The Molo partner API can be used by any partner who has obtained an access token.
You can access these endpoints by using the `bearer token` authentication header and entering this token as the value.


## Create an application
```shell
curl
```
**Send a `POST` request to https://partner.molofinance.com/v1/applications/ to create a new application.**
You will need to provide the following parameters:

- `property_value` A decimal value with 2 decimal places. This value is presumed to be in GBP
- `monthly_rent` A decimal value with 2 decimal places. This value is presumed to be in GBP
- `desired_loan_amount` A decimal value with 2 decimal places. This value is presumed to be in GBP
- `loan_term` An integer number of years
- `mortgage_type` The type of mortgage the customer wants. A choice of:
    - "new_purchase"
    - "remortgage_current"
    - "remortgage_and_borrow"
- `borrower_type` The type of borrower, a choice of:
    - "individual_borrower"
    - "ltd_company"

**Send a `PATCH` request to https://partner.molofinance.com/v1/applications/{pk}/ to update an application.**
- RESPONSE AND FIELDS TBC

**Send a `GET` request to https://partner.molofinance.com/v1/applications/ to retrieve a list of past applications.**
- RESPONSE TBC

**Send a `GET` request to https://partner.molofinance.com/v1/applications/{pk}/ to retrieve details of a specific application.**
- RESPONSE TBC


## Create an applicant
```shell
curl
```
**Send a `POST` request to https://partner.molofinance.com/v1/applicants/ to create a new applicant.**
You will need to provide the following parameters:

- `application_id` The ID of the application this applicant should be attached to.
- `is_lead` Is this applicant the lead applicant on this application? Each application requires one and only one lead applicant. A boolean
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
- `middle_name` The customer's middle name
- `last_name` The customer's last name
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
- `is_uk_resident` Has the customer been a UK resident for at least 3 years, boolean
- `is_bankrupt` Has the customer been bankrupt in the last 6 years, boolean
- `is_investment` Is the property for investing purposed? boolean
- `btl_mortgages_count` How many other buy-to-let mortgages does the customer already have?
- `is_ltd_personal_guarantee` Is the customer providing a personal guarantee on behalf of their limited company. Boolean

**Send a `PATCH` request to https://partner.molofinance.com/v1/applicants/{pk}/ a update an applicant.**
- RESPONSE AND FIELDS TBC

**Send a `GET` request to https://partner.molofinance.com/v1/applicants/ to retrieve a list of applicants.**
- RESPONSE TBC
- DO WE NEED THIS?

**Send a `GET` request to https://partner.molofinance.com/v1/applicants/{pk}/ to retrieve a specific applicant.**
- RESPONSE TBC

**Send a `DELETE` request to https://partner.molofinance.com/v1/applicants/{pk}/ to delete a specific applicant.**
- RESPONSE TBC


## Get a decision in principal
```shell
curl
```
**Send a `POST` request to https://partner.molofinance.com/v1/applications/{pk}/get-dip/ to get a decision in principal.**
There are no fields to send.
The response will be in the form:

- `dip_agreed` Whether Molo can give you a decision in principal. A boolean.
- `link_to_dip` A link to the DIP itself so that it can be downloaded.
- `rejection_reason` If Molo cannot offer a DIP, we will give you a reason why not.


## Get relevant products
```shell
curl
```
**Send a `GET` request to https://partner.molofinance.com/v1/applications/{pk}/relevant-products/ to retrieve a list of relevant products.**

The response will be in the form:

- `id` The ID of the product
- `name` The name of the product
- `rate_type` The type of rate on this mortgage. One of:
    - "tracker"
    - "fixed"
- `code` The product code
- `ltv` The loan-to-value required for this product
- `initial_monthly_repayment` The initial monthly repayment, a decimal value in GBP
- `product_fee` The product fee, a decimal value in GBP
- `fixation_period` An integer number of years that this interest is fixed for.
- `reversion_rate` The reversion rate
- `initial_rate` The initial rate
- `aprc` The APRC of this product
- `valuation_fee` The valuation fee on this product
- `true_cost_of_product` The true cost of this product
- `mortgage_type` The type of this mortgage, one of:
    - "new_purchase"
    - "remortgage_current"
    - "remortgage_and_borrow"
- `borrower_type` The type of borrower for this product, one of:
    - "individual_borrower"
    - "ltd_company"


**Send a `GET` request to https://partner.molofinance.com/v1/applications/{pk}/relevant-products/{pk} to retrieve details of a specific product.**

The response will be in the form:

- `id` The ID of the product
- `name` The name of the product
- `initial_rate` The initial rate
- `rate_type` The type of rate on this mortgage. One of:
    - "tracker"
    - "fixed"
- `fixation_period` An integer number of years that this interest is fixed for.
- `reversion_rate` The reversion rate
- `reversion_spread`
- `reversion_base_rate_type`
- `loan`
- `loan_term`
- `initial_monthly_repayment` The initial monthly repayment, a decimal value in GBP
- `followed_by_monthly_repayment`
- `total_amount_to_be_repaid`
- `total_interest_payable`
- `aprc` The APRC of this product
- `true_cost_of_product` The true cost of this product
- `product_fee` The product fee, a decimal value in GBP
- `valuation_fee` The valuation fee on this product
- `legal_fees`
- `overpayment_allowance`
- `first_year_early_repayment_fee`
- `second_year_early_repayment_fee`
- `third_year_early_repayment_fee`
- `fourth_year_early_repayment_fee`
- `fifth_year_early_repayment_fee`
- `mortgage_exit_administration_fee`
