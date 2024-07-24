HIFI users need to add on\-ramp and/or off\-ramp account(s) prior to making transfers.


* **On\-ramping** is the process that enables users to convert fiat currencies into cryptocurrencies. An on\-ramp account will serve as the source of an on\-ramp transaction.
* **Off\-ramping** is the process that allows the conversion of digital assets back into fiat currencies. An off\-ramp account will serve as the destination of an off\-ramp transaction.


1\. On\-ramp Account
====================


An on\-ramp account serves as a source, enabling users to convert fiat currencies into cryptocurrencies. 


1\.1 Add an USD Onramp (Plaid) Bank Account
-------------------------------------------


To add an on\-ramp account, HIFI and Plaid have partnered to enable you to use a Plaid processor token to add the user's bank account through the [USD\-Onramp\-Plaid](/reference/add-a-bank-account-for-usd-onramp-via-plaid-processor-token) endpoint. To create a Plaid processor token for adding HIFI accounts, please follow this [guide](/docs/generate-plaid-processor-token).


**Request**


cURL
```
curl --request POST \
     --url 'https://production.hifibridge.com/account/usd/onramp/plaid?userId=75d7c01f-5f93-4490-8b93-a62fd8020358' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "accountType": "CHECKING",
  "plaidProcessorToken": "processor-sandbox-0asd1-a92nc",
  "bankName": "Chase"
}
'

```

**Response**


JSON
```
{
  "status": "ACTIVE",
  "invalidFields": [],
  "message": "Bank account added successfully",
  "id": "8caf905c-282b-4b0d-ba15-beac9c2aee92"
}

```

You can use the resulting account id as the source of an onramp transaction.


2\. Off\-ramp Account
=====================


An off\-ramp account serves as the destination that allows the conversion of digital assets back into fiat currencies.


To add an off\-ramp account, HIFI supports [ACH](https://www.consumerfinance.gov/ask-cfpb/what-is-an-ach-transaction-en-1065/) and [WIRE](https://www.consumerfinance.gov/ask-cfpb/what-is-a-wire-transfer-en-1163/) destination bank accounts for USD, and [SEPA](https://www.ecb.europa.eu/paym/integration/retail/sepa/html/index.en.html) destination bank accounts for EUR.


2\.1 Add an ACH destination bank account
----------------------------------------


To add an ACH destination bank account, you can pass the bank account details through the [usd\-offramp](/reference/add-a-bank-account-for-usd-onramp-copy) endpoint to get an off\-ramp account ID, which serves as the destination of an off\-ramp transaction.


**Request**


cURL
```
curl --request POST \
     --url 'https://production.hifibridge.com/account/usd/offramp?userId=75d7c01f-5f93-4490-8b93-a62fd8020358' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "currency": "usd",
  "accountOwnerType": "individual",
  "bankName": "Demo Bank",
  "accountOwnerName": "John Doe",
  "accountNumber": "123456789",
  "routingNumber": "987654321",
  "streetLine1": "123 Demo Street",
  "streetLine2": "Apt 456",
  "city": "Demo City",
  "state": "CA",
  "postalCode": "90210",
  "country": "USA"
}
'

```

**Response**


JSON
```
{
  "status": "ACTIVE",
  "invalidFields": [],
  "message": "Account created successfully",
  "id": "daa6ad75-a4c2-486f-a937-1bbf4d19553a"
}

```

2\.2 Add a WIRE destination bank account
----------------------------------------


To add an WIRE destination bank account, you call pass the account details through the [usd\-offramp\-wire](/reference/add-a-ach-destination-bank-account-for-stablecoin-liquidations-copy) endpoint to get an off\-ramp account ID, which serves as the destination of an off\-ramp transaction.


**Request**


cURL
```
curl --request POST \
     --url 'https://production.hifibridge.com/account/usd/offramp/wire?userId=75d7c01f-5f93-4490-8b93-a62fd8020358' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "currency": "usd",
  "accountOwnerType": "individual",
  "bankName": "Demo Bank",
  "bankIdentifierCode": "DEMOUS33",
  "iban": "DE89370400440532013000",
  "accountNumber": "123456789",
  "streetLine1": "123 Demo Street",
  "city": "Demo City",
  "streetLine2": "Apt 456",
  "state": "CA",
  "postalCode": "90210",
  "country": "USA",
  "businessName": "Demo Business",
  "firstName": "John",
  "middleName": "A.",
  "lastName": "Doe",
  "legalFullName": "John A. Doe"
}
'

```

**Response**


JSON
```
{
  "status": "ACTIVE",
  "invalidFields": [],
  "message": "Account created successfully",
  "id": "daa6ad75-a4c2-486f-a937-1bbf4d19553a"
}

```

2\.3 Add a SEPA destination bank account
----------------------------------------


To add an SEPA destination bank account, you call pass the account details through the [euro\-offramp](/reference/add-a-bank-account-for-usd-offramp-copy) endpoint to get an off\-ramp account ID, which serves as the destination of an off\-ramp transaction.


**Request**


cURL
```
curl --request POST \
     --url 'https://production.hifibridge.com/account/euro/offramp?userId=%20' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "accountOwnerType": "individual",
  "currency": "usd",
  "bankName": "Demo Bank",
  "accountOwnerName": "John Doe",
  "ibanAccountNumber": "DE89370400440532013000",
  "ibanCountryCode": "DE",
  "businessIdentifierCode": "DEMOUS33",
  "firstName": "John",
  "lastName": "Doe",
  "businessName": "Demo Business"
}
'

```

**Response**


JSON
```
{
  "status": "ACTIVE",
  "invalidFields": [],
  "message": "Account created successfully",
  "id": "daa6ad75-a4c2-486f-a937-1bbf4d19553a"
}

```

🎉 Congratulations. You have successfully added accounts to a HIFI user.


3\. Ready for the next step?
============================


After adding on\-ramp and off\-ramp bank accounts for the user, you can now do transfers. Learn more about how to do transfers.

Updated 13 days ago 



---

What’s Next* [Transfer](/docs/do-transfer)
