---
title: API References

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL

toc_footers:
  - <a href='http://www.wingmoney.com'>Contact Wing for User Crendentails</a>


search: true
---

# Overview

Welcome to the Wing API! 

The wing API documentation provides pragmatic access to wing system. This document is a technical specification for interaction between wing systems with third party. It describes how message is defined and transmitted between Wing and Third party partners.


# Authentication

To be able to access to wing API,  <code>username</code>, <code>password</code>, <code>client_id</code>, <code>client_secret</code> are required to authenticate request. Once authentication fail, any access from third party would be blocked. Wing will share these information and URL endpoints once we start the integration. 

The authenication will return a **token** which will be use for processing Wing API. This token is valid for only 5 minutes.
You will need to request the token once again when it get expired.

## Get Access Token

> Example Request:

```shell
$ curl -X POST <URL>/oauth/token \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'username=<username>&password=<password>&grant_type=password&client_id=<client_id>&client_secret=<client_secret>&scope=trust'
```
> Example Response:

```json
{
    "access_token": "43d75463-062f-440c-88e6-db87fb4b9406",
    "token_type": "bearer",
    "expires_in": 299,
    "scope": "trust"
}
```
This endpoint is used to get **token**

### HTTP REQUEST

`POST /oauth/token`

Key | Required | Value 
-------------- | -------------- | --------------
username | true | YOUR_USERNAME
password | true | YOUR_PASSWORD
client_id | true | YOUR_CLIENT_ID
client_secret | true | YOUR_CLIENT_SECRET
grant_type | true | password

<aside class="notice">
You must replace these key with the provided credential.
</aside>

### RESPONSE

Key | Description
-------------- | --------------
access_token | Access token that acts as a session ID that the application uses for authenticate requests
expires_in | Token expiration time (in seconds)
token_type | Token Type
scope | Authorization delimiter

## Handling Authentication Error

> Example Error Response:

```json
{
    "error": "invalid_grant",
    "error_description": "Bad credentials"
}
```

In case of token expired or credentails are invalid, there will be error response.


# Wing Services

## Settlement

This endpoint is for request processing settlement. The settlement process including Validation and Commit.

### Validate Transaction

### HTTP REQUEST

> Example Request:

```shell
$ curl -X POST <URL>/api/v1/settlement/validate \
  -H 'authorization: Bearer <access_token>' \
  -H 'content-type: application/json' \
  -d '{
  "bank_name": "ACLEDA",
  "bank_account": "0000-0000-0000-0000",
  "from_date":"01/12/2018 00:33:00",
  "to_date":"04/12/2018 23:00:00"
}'
```

> Example Response:

```json
{
    "total": "560025",
    "currency": "KHR",
    "bank_acc_name": "MPWT",
    "bank_name": "ACLEDA",
    "from_date": "01/12/2018 00:33:00",
    "to_date": "04/12/2018 23:00:00",
    "bank_account": "0000-0000-0000-0000"
}
```

`POST /api/v1/settlement/validate`

Key | Required | Description 
-------------- | -------------- | --------------
bank_name | true | Name of the Bank to transfer to when settlement happen
bank_account | true | Bank Account# to transfer to
from_date | true | Start date of the transction to be settled
to_date | true | End date of the transction to be settled

### RESPONSE

Key | Description
-------------- | --------------
total | Total amount of the transaction between selected date range
currency | Currency of the amount to be settled (base on Biller/Wing# currency)
bank_name | Name of the Bank to transfer to when settlement happen
bank_acc_name | Bank Account Name to transfer to
bank_account | Bank Account# to transfer to
from_date | Start date of the transction to be settled
to_date | End date of the transction to be settled



### Commit Transaction

### HTTP REQUEST

> Example Request:

```shell
$ curl -X POST <URL>/api/v1/settlement/commit \
  -H 'authorization: Bearer <access_token>' \
  -H 'content-type: application/json' \
  -d '{}'
```

> Example Response:

```json
{
    "amount": "KHR 560,025",
    "total": "KHR 560,025",
    "transaction_status": "200",
    "transaction_id": "PYC089345"
}
```

`POST /api/v1/settlement/commit`

<aside class="notice">
There is no body data require in commit.
</aside>

### RESPONSE

Key | Description
-------------- | --------------
amount | Amount of the transaction
total | Total amount of the transaction
transaction_status | Status of the transaction
transaction_id | Transaction ID


# Errors Handling

> Example Error Response:

```json
{
    {
    "error_code": "E0204",
    "message": "ERROR: Not enough wallet balance to do transactions."
}
}
```

For error response, Wing API will send <code>error_code=200</code> with HTTP CODE 202.

Below are the possible error code:

Error Code | Meaning
---------- | -------
00067 | Sender account has been blocked, please call WING at 023 999 989.
00335 | Security Code & Wing Account mismatch. 
00340 | Security Code Expired. 
000030 | General Fail Exception. 
00002 | Token is expired
006035 | Transaction not found
E0204 | ERROR: Not enough wallet balance to do transactions.
00210 | You have exceeded the number of attempts allowed. Account blocked, please call WING at 023 999 989.
000038 | Biller/Merchant not found.
00066 | User not found.
00092 | Account Number is Invalid.
00940 | Requested Amount Is more than Maximum Transfer Allowed
00941 | Amount is Less than Minimum Transfer Allowed
60050011 | Maximum count of transaction per month for payer reached
60050012 | Maximum count of transaction per week for payer reached
60050013 | Maximum count of transaction per day for payer reached
60050021 | Maximum count of transaction per month for payee reached
60050022 | Maximum count of transaction per week for payee reached
60050023 | Maximum count of transaction per day for payee reached
60060011 | Maximum amount of transaction per month for payer reached
60060012 | Maximum amount of transaction per week for payer reached
60060013 | Maximum amount of transaction per day for payer reached
60060021 | Maximum amount of transaction per month for payee reached
60060022 | Maximum amount of transaction per week for payee reached
60060023 | Maximum amount of transaction per day for payee reached


