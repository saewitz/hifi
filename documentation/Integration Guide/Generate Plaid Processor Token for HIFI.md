Overview
========


HIFI and Plaid have partnered to provide a way to integrate Plaid’s instant account verification with HIFI's digital payment solution. With Plaid Link, users can quickly and securely onboard a bank account to natively issue payments through HIFI's API.


Getting Started
===============


You'll first want to familiarize yourself with [Plaid Link](https://plaid.com/plaid-link/), a drop\-in client\-side integration for the Plaid API that handles input validation, error handling, and multi\-factor authentication. You will also need to have a verified HIFI account to add a bank funding source. Your customers will use Link to authenticate with their financial institution and select the bank account they wish to connect. From there, you'll receive a Plaid access\_token and a HIFI processor\_token, which allows you to quickly and securely verify a bank funding source via [HIFI's API](/reference/supported-regionscountries) without having to store any sensitive banking information. Utilizing Plaid \+ HIFI enables a seamless workflow for connecting external financial accounts to HIFI.


Instructions
============


### Set up your accounts


You'll need accounts at both Plaid and HIFI in order to use the Plaid \+ HIFI integration. You'll also need to enable your Plaid account for the Checkbook integration as HIFI internally uses Checkbook.


First, you will need to use the HIFI API to create a HIFI user, if you do not already have one.


Next, verify that your Plaid account is enabled for the integration. If you do not have a Plaid account, [create one](https://dashboard.plaid.com/signup).


To enable your Plaid account for the integration, go to the Integrations section of the account dashboard. If the integration is off, simply click the 'Enable' button for Checkbook to enable the integration.


Finally, you'll need to complete your Plaid [Application Profile](https://dashboard.plaid.com/signin?redirect=%2Fsettings%2Fcompany%2Fapp-branding) in the Dashboard, which involves filling out basic information about your app, such as your company name and website. This step helps your end\-users learn more how your product uses their bank information and is also required for connecting to some banks.


### Create a link\_token


In order to integrate with Plaid Link, you will first need to create a **link\_token**. A **link\_token** is a short\-lived, one\-time use token that is used to authenticate your app with Link. To create one, make a [/link/token/create](https://plaid.com/docs/api/tokens/#linktokencreate) request with your **client\_id**, **secret**, and a few other required parameters from your app server. For a full list of **link\_token** configurations, see [/link/token/create](https://plaid.com/docs/api/tokens/#linktokencreate).


To see your **client\_id** and **secret**, visit the [Plaid Dashboard](https://dashboard.plaid.com/signin?redirect=%2Fdevelopers%2Fkeys).


cURL
```
curl -X POST https://sandbox.plaid.com/link/token/create \
-H 'Content-Type: application/json' \
-d '{
  "client_id": "${PLAID_CLIENT_ID}",
  "secret": "${PLAID_SECRET}",
  "client_name": "Plaid Test App",
  "user": { "client_user_id": "${UNIQUE_USER_ID}" },
  "products": ["${PRODUCT}"],
  "country_codes": ["US"],
  "language": "en",
  "webhook": "https://webhook.example.com",
  "redirect_uri": "https://domainname.com/oauth-page.html",
}'

```

### Integrate with Plaid Link


Once you have a **link\_token**, all it takes is a few lines of client\-side JavaScript to launch Link. Then, in the **onSuccess** callback, you can call a simple server\-side handler to exchange the Link **public\_token** for a Plaid **access\_token** and a HIFI **processor\_token**.

See the [Link parameter reference](https://plaid.com/docs/link/web/#create) for complete documentation on possible configurations.


**Plaid.create** accepts one argument, a configuration **Object**, and returns an **Object** with three functions, [open](https://plaid.com/docs/link/web/#open), [exit](https://plaid.com/docs/link/web/#exit), and [destroy](https://plaid.com/docs/link/web/#destroy). Calling **open** will display the "Institution Select" view, calling **exit** will close Link, and calling **destroy** will clean up the iframe.


### Write server\-side handler


The Link module handles the entire onboarding flow securely and quickly, but does not actually retrieve account data for a user. Instead, the Link module returns a **public\_token** and an **accounts** array, which is a property on the **metadata** object, via the **onSuccess** callback. Exchange this **public\_token** for a Plaid **access\_token** using the [/item/public\_token/exchange](https://plaid.com/docs/api/items/#itempublic_tokenexchange) API endpoint.


The **accounts** array will contain information about bank accounts associated with the credentials entered by the user, and may contain multiple accounts if the user has more than one bank account at the institution. If you want the user to specify only a single account to link so you know which account to use with HIFI, set [Account Select](https://dashboard.plaid.com/signin?redirect=%2Flink%2Faccount-select) to "enabled for one account" in the Plaid Dashboard. When this setting is selected, the **accounts** array will always contain exactly one account.


Once you have identified the account you will use, you will send the **access\_token** and **account\_id** property of the account to Plaid via the [/processor/token/create](https://plaid.com/docs/api/processors/#processortokencreate) endpoint in order to create a HIFI **processor\_token**. You'll send this token to HIFI and they will use it to securely retrieve account and routing numbers from Plaid.


You can create HIFI **processor\_tokens** in both API environments:


Sandbox (<https://sandbox.plaid.com>): test simulated users  

Production (<https://production.plaid.com>): production environment for when you're ready to go live and have valid HIFI Production credentials


cURL
```
# Exchange token
curl -X POST https://sandbox.plaid.com/item/public_token/exchange \
  -H 'Content-Type: application/json' \
  -d '{
    "client_id": "${PLAID_CLIENT_ID}",
    "secret": "${PLAID_SECRET}",
    "public_token": "${PUBLIC_TOKEN}"
  }'

# Create a processor token for a specific account id. HIFI internally uses Checkbook.
curl -X POST https://sandbox.plaid.com/processor/token/create \
  -H 'Content-Type: application/json' \
  -d '{
    "client_id": "${PLAID_CLIENT_ID}",
    "secret": "${PLAID_SECRET}",
    "access_token": "${ACCESS_TOKEN}",
    "account_id": "${ACCOUNT_ID}",
    "processor": "checkbook"
  }'

```

For a valid request, the API will return a JSON response similar to:


JSON
```
{
  "processor_token": "processor-sandbox-0asd1-a92nc",
  "request_id": "m8MDnv9okwxFNBV"
}

```

For possible error codes, see the full listing of Plaid [error codes](https://plaid.com/docs/errors/).

Updated 13 days ago 



---

