1\. Generate an API Key
=======================


HIFI's APIs require an API key to authenticate client request. The API key must be set in the Authorization header of the request as a Bearer Token. [GET AN API KEY](https://dashboard.hifibridge.com/apikeys)


2\. User Creation Flow
======================


A user represents an individual or a business and serves as the foundational object for HIFI's APIs. 


Generate a Terms of Service Page Link
-------------------------------------


Users must be presented with HIFI's Terms of Service (ToS). Use the [tos\-link](/reference/get-user-copy) endpoint to generate a ToS link.


![](https://files.readme.io/218ab35-image.png)

> 🚧No frontend? Schedule a call with an integration engineer to discuss your use case.
> ------------------------------------------------------------------------------------


The ToS page will redirect the user after the user clicks "Accept". A signedAgreementId will be included as a url parameter. 


* The `idempotencyKey`passed in the ToS request body will be equal to the `signedAgreementId` returned by the ToS page. The `signedAgreementId` becomes valid once the user clicks "Accept".
* After the user clicks "Accept", the user will be redirected to the provided `redirectUrl`


**Request**


cURL
```
curl --request POST \
     --url https://production.hifibridge.com/tos-link \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "idempotencyKey": "e06563d9-2944-4dff-ad52-3b4346912c06",
  "redirectUrl": "https://example.com/auth/tosredirect"
}
'

```

**Response**


JSON
```
{
  "url": "https://dashboard.hifibridge.com/accept-terms-of-service?sessionToken=e12d9c3f-75a8-4bd1-aa3d-97a2cfaf2c40&redirectUrl=...&templateId=2fb2da24-472a-4e5b-b160-038d9dc82a40",
  "sessionToken": "e12d9c3f-75a8-4bd1-aa3d-97a2cfaf2c40"
}

```

**Example redirect call after accepting ToS**


JSON
```
"https://example.com/auth/tosredirect?signedAgreementId=e06563d9-2944-4dff-ad52-3b4346912c06"

```

Calling the Create User Endpoint
--------------------------------


To create a HIFI user (individual/business), you will need to submit the user's personal information through the [create\-user](/reference/create-user) endpoint along with a `signedAgreementId`. 


Depending on the user type, you will need to provide different kinds of information to pass HIFI's KYC (Know Your Customer) or KYB (Know Your Business) program.


In this example, we will use an individual customer.


After submitting all the information via the [POST user/create](/reference/create-user), you will receive either a user object or an error message indicating any missing or invalid information.


**Request**


cURL (Individual)cURL (Business)
```
curl --request POST \
     --url 'https://production.hifibridge.com/user/create' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "userType": "individual",
  "legalFirstName": "John",
  "legalLastName": "Doe",
  "complianceEmail": "[[email protected]](/cdn-cgi/l/email-protection)",
  "compliancePhone": "+1234567890",
  "dateOfBirth": "1990-01-01",
  "taxIdentificationNumber": "123456789",
  "addressLine1": "123 Main St",
  "addressLine2": "Apt 4B",
  "city": "New York",
  "stateProvinceRegion": "NY",
  "postalCode": "10001",
  "country": "US",
  "signedAgreementId": "agr1234567890",
  "ipAddress": "192.168.1.1",
  "govIdFront": "https://example.com/images/govIdFront.jpg",
  "govIdBack": "https://example.com/images/govIdBack.jpg",
  "govIdCountry": "US",
  "proofOfResidency": "https://example.com/images/proofOfResidency.jpg"
}
'

```

```
curl --request POST \
     --url 'https://production.hifibridge.com/user/create#business' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "userType": "business",
  "businessType": "cooperative",
  "transmitsCustomerFunds": true,
  "isDao": true,
  "businessName": "Doe Cooperative Ventures",
  "businessDescription": "A cooperative business focused on sustainable agricultural practices and local food production.",
  "complianceEmail": "[[email protected]](/cdn-cgi/l/email-protection)",
  "sourceOfFunds": "Member contributions and grants",
  "taxIdentificationNumber": "987654321",
  "website": "https://www.doecooperative.com",
  "addressLine1": "456 Enterprise Ave",
  "addressLine2": "Suite 100",
  "city": "San Francisco",
  "stateProvinceRegion": "CA",
  "postalCode": "94105",
  "country": "US",
  "ipAddress": "203.0.113.42",
  "signedAgreementId": "agr9876543210",
  "formationDoc": "https://example.com/documents/formationDoc.pdf",
  "proofOfOwnership": "https://example.com/documents/proofOfOwnership.pdf"
}
'

```

The response object will contain three key parts:


**Wallet Address**: The wallet address for different blockchains.  

**KYC Status**: The user's Know Your Customer (KYC) status.  

**On/Off Ramp Rails**: The on/off ramp rails that the user is permitted to use for depositing and withdrawing funds.


  

### Successful user creation


For this example, in which the user provided all the required and valid information, you will expect the status to be set to `PENDING` 


JSON
```
{
    "wallet": {
        "walletStatus": "ACTIVE",
        "walletMessage": "",
        "actionNeeded": {
            "actions": [],
            "fieldsToResubmit": []
        },
        "walletAddress": {
            "POLYGON_MAINNET": {
                "address": "0x1D6663d083C03AFCa98B2085d456542C26937e11"
            },
            "ETHEREUM_MAINNET": {
                "address": "0x1D6663d083C03AFCa98B2085d456542C26937e11"
            },
            "BASE_MAINNET": {
                "address": "0x1D6663d083C03AFCa98B2085d456542C26937e11"
            },
            "OPTIMISM_MAINNET": {
                "address": "0x1D6663d083C03AFCa98B2085d456542C26937e11"
            }
        }
    },
    "user_kyc": {
        "status": "PENDING",
        "actionNeeded": {
            "actions": [],
            "fieldsToResubmit": []
        },
        "message": "kyc aplication still under review"
    },
    "ramps": {
        "usdAch": {
            "onRamp": {
                "status": "PENDING",
                "actionNeeded": {
                    "actions": [],
                    "fieldsToResubmit": []
                },
                "achPull": {
                    "achPullStatus": "PENDING",
                    "actionNeeded": {
                        "actions": [],
                        "fieldsToResubmit": []
                    }
                }
            },
            "offRamp": {
                "status": "PENDING",
                "actionNeeded": {
                    "actions": [],
                    "fieldsToResubmit": []
                }
            }
        },
        "euroSepa": {
            "onRamp": {
                "status": "INACTIVE",
                "actionNeeded": {
                    "actions": [],
                    "fieldsToResubmit": []
                },
                "message": "SEPA onRamp will be available in near future"
            },
            "offRamp": {
                "status": "PENDING",
                "actionNeeded": {
                    "actions": [
                        "update"
                    ],
                    "fieldsToResubmit": [
                        "proofOfResidency"
                    ]
                },
                "message": ""
            }
        }
    },
    "user": {
        "id": "accba07f-ed45-4235-a9a0-8754dbc58bdc"
    }
}

```

### Successful user creation with additional information


Sometimes, we may request additional information to process the KYC/KYB or to activate other regional services, such as EU SEPA.


Below is a partial example of a user object, which requires `proofOfResidency` in order to activate this specific offramp:



> NOTE: You can either provide it when creating a user or use PUT user endpoint to resubmit proofOfResidency


JSON
```
{...,
  "offRamp": {
      "status": "INACTIVE",
      "actionNeeded": {
          "actions": [
              "update"
          ],
          "fieldsToResubmit": [
              "proofOfResidency"
          ]
      },
      "message": ""
  }
}

```

Below is another partial example of a user object, which requires the update of `govId` in order to resubmit the KYC


NOTE: Use PUT user endpoint to resubmit govIdFront


JSON
```
{
  ...,
  "user_kyc": {
        "status": "INACTIVE",
        "actionNeeded": {
            "actions": ["update"],
            "fieldsToResubmit": ["govId"]
        }
    },
}

```

Get User
--------


To retrieve the up\-to\-date user status, you can use the [GET user](/reference/get-user)endpoint to fetch the user object. Alternatively, you can [register a webhook](/reference/webhooks) to receive status updates automatically.


**Below are the expected status of a source**


* ACTIVE
* INACTIVE
* PENDING


**Below are the actions of a source you can be expected**


* update
* manual\_review
* invalid\_customer\_info



> NOTE: It is rare to see the `manual_review` and `invalid_customer_info`, please contact HIFI team if you see these two actions


Update User
-----------


To resubmit the KYC information, you can use the [PUT user](/reference/create-user-copy) to submit the missing or invalid information based on the `actions` and the `fieldsToResubmit`


JSON
```
{
  ...,
  "user_kyc": {
        "status": "INACTIVE",
        "actionNeeded": {
            "actions": ["update"],
            "fieldsToResubmit": ["govId"]
        }
    },
}

```

cURL
```
curl --request PUT \
     --url https://production.hifibridge.com/user \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "govIdFront": "govIdFront URL",
  "govIdBack": "govIdBack URL"
}
'

```
Updated 13 days ago 



---

What’s Next* [Refresh User Status](/docs/refresh-user-status)
