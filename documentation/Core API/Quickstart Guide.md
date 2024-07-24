**This guide will walk you through converting $2 USD fiat into USDC stablecoin, transferring the USDC to another user, and then converting the USDC back into fiat to be sent to a fiat bank account.**

This guide assumes you have generated an API Key here: <https://dashboard.hifibridge.com/apikeys>


Welcome to the HIFI Quickstart Guide! In this guide, we'll walk you through the process of converting a single dollar from fiat to USDC stablecoin, transferring the USDC to another user, and then converting the USDC back into fiat to be sent to a bank account. By following along, you'll gain a clear understanding of how our endpoints work and how to integrate them into your application. Make sure you have your API key from HIFI ready to get started.


First, we need to convert the dollar from fiat to USDC.


### Redirect the user to HIFI's Terms of Service page


To do this, we need to generate a signedAgreementId, which is created after the user accepts HIFI's terms of service and privacy policy on a webpage hosted by HIFI. The signedAgreementId will be used in the POST /user/create call. This will return a URL to which the user should be redirected.


As the developer, you can choose to pass a redirect URL, which will redirect the user to the URL of your choice after they accept the Terms of Service.


Request:


```curl
curl --request POST \
     --url https://production.hifibridge.com/tos-link \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_1234567890' \
     --header 'content-type: application/json' \
     --data '
{
  "idempotencyKey": "b5a913fd-7bfc-4609-ae27-3a0a46298035",
  "redirectUrl": "yeahbuddy.com/path-to-redirect-page-where-you-will-parse-the-signedAgreementId"
}
'
```

Response:

```json
{
  "url": "https://dashboard.hifibridge.com/accept-terms-of-service?sessionToken=3e5f792f-6707-4e6b-807a-7a27b5266860&redirectUrl=yeahbuddy.com%2Fpath-to-redirect-page-where-you-will-parse-the-signedAgreementId&templateId=2fb2da24-472a-4e5b-b160-038d9dc82a40",
}

```

The signedAgreementId will be provided in the redirect URL as a URL parameter, which should be parsed and included in the subsequent POST /user/create call..


<img alt="HIFI's default terms of service template. This page can be whitelabled with your organizations logo and brand colors." src="https://files.readme.io/47eda49-image.png" style="width: 50%;" />

*HIFI's default terms of service template. This page can be whitelabeled with your organization's logo and brand colors.*

<div class="announcement announcement-info">The signedAgreementId is the same as the passed idempotencyKey you provide, so strictly speaking, you do not have to parse the redirect URL. You may simply attempt to call the POST /user/create endpoint with the idempotencyKey value as the signedAgreementId, but if the user did not accept the TOS, the user creation endpoint will return an error.</div>


### Create the user object


The user object represents an individual or business. Every future account and transaction will be tied to a user object.


Now that we have the user's signedAgreementId, we need to create the user by calling POST /user/create. This creates the user object in our system and provides HIFI with all the KYC/KYB data required for HIFI to screen the user.


Request


```curl
curl --request POST \
     --url https://production.hifibridge.com/user/create \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_1234567890' \
     --header 'content-type: application/json' \
     --data '
{
  "userType": "individual",
  "legalFirstName": "Ronnie",
  "legalLastName": "Coleman",
  "complianceEmail": "[[email protected]](/cdn-cgi/l/email-protection)",
  "compliancePhone": "+17025055658",
  "dateOfBirth": "1964-05-13",
  "taxIdentificationNumber": "431931235",
  "addressLine1": "2921 S Cooper St",
  "city": "Arlington",
  "stateProvinceRegion": "TX",
  "postalCode": "76015",
  "country": "USA",
  "signedAgreementId": "b5a913fd-7cfc-4609-ae27-3a0a46298037",
  "ipAddress": "31.222.254.178",
  "govIdFront": "https://pqgnrjvoqbopfaxmlhlv.supabase.co/storage/v1/object/sign/compliance_id/123456/front?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1cmwiOiJjb21wbGlhbmNlX2lkLzEyMzQ1Ni9mcm9udCIsImlhdCI6MTcyMTMxNzc2OSwiZXhwIjo0ODc0OTE3NzY5fQ.EukqQnJuoOdM8EFMlR1bePzX6r8aCGPcN6zLhT7OFPs&t=2024-07-18T15%3A49%3A29.312Z",
  "govIdBack": "https://pqgnrjvoqbopfaxmlhlv.supabase.co/storage/v1/object/sign/compliance_id/123456/front?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1cmwiOiJjb21wbGlhbmNlX2lkLzEyMzQ1Ni9mcm9udCIsImlhdCI6MTcyMTMxNzc2OSwiZXhwIjo0ODc0OTE3NzY5fQ.EukqQnJuoOdM8EFMlR1bePzX6r8aCGPcN6zLhT7OFPs&t=2024-07-18T15%3A49%3A29.312Z",
  "govIdCountry": "TWN"
}
'

```

Response

```json
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
                        "actions": [
                            "update"
                        ],
                        "fieldsToResubmit": []
                    }
                }
            },
            "offRamp": {
                "status": "PENDING",
                "actionNeeded": {
                    "actions": [
                        "update"
                    ],
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

Let's take a moment to understand the response:


* The `wallet` object contains the status of the wallet that was provisioned for the user and includes all of the wallet addresses associated with the user.
* The `ramps` object tells you which ramps (ways to convert fiat to stablecoin and vice versa) are active for the user, each with a status. An "onramp" refers to a method by which you can convert fiat to stablecoin. An "offramp" refers to a method by which you can convert stablecoin to fiat. You'll notice that the status of every on and off ramp is set to "PENDING" in the initial POST /user/create response. This is because the compliance screening process is asynchronous (typically takes a few seconds). You'll want to poll the GET /user endpoint to get the latest status of the user before proceeding. Each on and off ramp may return a `fieldsToResubmit` array, which should be used to identify invalid parameters that need to be resubmitted via the PUT /user endpoint. Typically, this will be related to a user's government ID being blurry.
* The `user` object contains the user's ID, which should be saved for future API calls against this particular user object. For example, adding a bank account or executing a transfer both require the userId to be passed in the query params.


After polling the GET /user endpoint for a few seconds, we see that the user has successfully passed KYC for the `usdAch` `offRamp`, which is all we want for the purposes of this quickstart tutorial. If this user needs to offramp to `euroSepa`, we would submit one additional field (`proofOfAddress`) to the PUT /user endpoint.

```json
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
        "address": "0xe663Ab2F5aA1d37664aE8201c571F6F82D8A759f"
      },
      "ETHEREUM_MAINNET": {
        "address": "0xe663Ab2F5aA1d37664aE8201c571F6F82D8A759f"
      },
      "OPTIMISM_MAINNET": {
        "address": "0xe663Ab2F5aA1d37664aE8201c571F6F82D8A759f"
      }
    }
  },
  "user_kyc": {
    "status": "ACTIVE",
    "actionNeeded": {
      "actions": [],
      "fieldsToResubmit": []
    }
  },
  "ramps": {
    "usdAch": {
      "onRamp": {
        "status": "ACTIVE",
        "actionNeeded": {
          "actions": [],
          "fieldsToResubmit": []
        },
        "message": "",
        "achPull": {
          "achPullStatus": "ACTIVE",
          "actionNeeded": {
            "actions": [],
            "fieldsToResubmit": []
          }
        }
      },
      "offRamp": {
        "status": "ACTIVE",
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
  },
  "user": {
    "id": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b"
  }
}

```

### Funding the user with $2 USDC
### Funding the user with $2 USDC



<div class="announcement announcement-info">HIFI is actively introducing additional onramps for users in non\-US geographies.</div>


To fund the user, you can connect a bank account with HIFI by going through the onramp account set up process. To set up the onramp account with HIFI, you will first need to [create a Plaid processor token](/docs/generate-plaid-processor-token) and then use the processor token to set up an onramp account through HIFI's [USD\-Onramp\_Plaid](/docs/add-accounts#11-add-an-usd-onramp-plaid-bank-account) endpoint. Look [here](/docs/add-accounts#1-on-ramp-account) for details guide on how to create an onramp account.


For the sake of this quickstart tutorial, you can test the wallet functionality by simply sending the POLYGON\_MAINNET wallet address $2 USDC on Polygon mainnet via MetaMask, Coinbase, or any other wallet you use.


<img src="https://files.readme.io/2ea9221-image.png" alt="Send $1 to the POLYGON_MAINNET address returned in the POST /user/create response" style="width: 50%;" />

*Send $1 to the POLYGON\_MAINNET address returned in the POST /user/create response*




**Optional**: Check PolygonScan to make sure that the funds were indeed received in the wallet that was just provisioned for the user we just created.


![](https://files.readme.io/5cd3772-image.png)


You may also notice that HIFI automagically handles gas on Polygon for any users you create. You can also verify this on the blockchain. 


![](https://files.readme.io/f7bd820-image.png)


### Transfer stablecoin to another wallet



<div class="announcement announcement-warning">All /transfer endpoints must be called with production api keys.</div>


Now that the user object has been created and their wallet has been funded, let's send $0\.50 USDC to another user using the POST /transfer/crypto\-to\-crypto endpoint.


Request


```curl
curl --request POST \
     --url https://production.hifibridge.com/transfer/crypto-to-crypto \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_1234567890' \
     --header 'content-type: application/json' \
     --data '
{
  "chain": "POLYGON_MAINNET",
  "currency": "usdc",
  "requestId": "c323e48f-dc47-4cb8-ab79-1aadb427c29e",
  "senderUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
  "recipientAddress": "0x76F8fc6667E239f83a547d4e16225d6a34f6FA22",
  "amount": 1
}
'

```

Response:

```json
{
  "transferType": "CRYPTO_TO_CRYPTO",
  "transferDetails": {
    "id": "6180f2db-2674-434a-8465-1c533666d595",
    "requestId": "c323e48f-dc47-4cb8-ab79-1aadb427c29e",
    "senderUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
    "recipientUserId": null,
    "recipientAddress": "0x76F8fc6667E239f83a547d4e16225d6a34f6FA22",
    "chain": "POLYGON_MAINNET",
    "currency": "usdc",
    "amount": 1,
    "transactionHash": "0x458e7dbec8f255a30ff8a72e37eed004601d2ee8c2653340e20a025954d193f1",
    "createdAt": "2024-07-01T18:36:38.006228+00:00",
    "status": "SUBMITTED",
    "contractAddress": "0x3c499c542cEF5E3811e1192ce70d8cC03d5c3359",
    "failedReason": ""
  }
}

```


<div class="announcement announcement-info">In the above example, we sent funds to a specific wallet address by providing the walletAddress parameter, but you can also specify a userId instead of the recipientAddress to send funds to another user you have created.</div>

Transferring stablecoin on the blockchain is inherently asynchronous. Therefore, we strongly recommend polling the GET /transfer/crypto\-to\-crypto endpoint to confirm the success of the transfer.


Request:


```curl
curl --request GET \
     --url 'https://production.hifibridge.com/transfer/crypto-to-crypto?id=6180f2db-2674-434a-8465-1c533666d595' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_1362a30026904073b4ad6383e3edea5d_789adb44'

```

Response:


```json
{
  "transferType": "CRYPTO_TO_CRYPTO",
  "transferDetails": {
    "id": "6180f2db-2674-434a-8465-1c533666d595",
    "requestId": "c323e48f-dc47-4cb8-ab79-1aadb427c29e",
    "senderUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
    "recipientUserId": null,
    "recipientAddress": "0x76F8fc6667E239f83a547d4e16225d6a34f6FA22",
    "chain": "POLYGON_MAINNET",
    "currency": "usdc",
    "transactionHash": "0x458e7dbec8f255a30ff8a72e37eed004601d2ee8c2653340e20a025954d193f1",
    "createdAt": "2024-07-01T18:36:38.006228+00:00",
    "updatedAt": "2024-07-01T18:37:00.722+00:00",
    "status": "CONFIRMED",
    "contractAddress": "0x3c499c542cEF5E3811e1192ce70d8cC03d5c3359"
  }
}

```

We see that the `status` is now "CONFIRMED", which indicates the transfer was successful on the blockchain.


### Liquidating stablecoin into fiat



<div class="announcement announcement-info">HIFI supports liquidating via ACH, SEPA, or wire. We are actively adding additional payment networks.</div>



Now that the user's (Ronnie Coleman) POLYGON\_MAINNET wallet has been funded with USDC, we can liquidate that as USD to our bank account.


We first need to add a destination bank account for the stablecoin liquidation. For the purposes of this quickstart, we will add a US bank account.


Request:

```curl
curl --request POST \
     --url 'https://production.hifibridge.com/account/usd/offramp?userId=ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_1234567890' \
     --header 'content-type: application/json' \
     --data '
{
  "currency": "usd",
  "accountOwnerType": "individual",
  "bankName": "Chase",
  "accountOwnerName": "Ronnie Coleman",
  "accountNumber": "350922385",
  "routingNumber": "322271427",
  "streetLine1": "360 Hampton Dr",
  "city": "Venice",
  "state": "CA",
  "postalCode": "90291",
  "country": "USA"
}
'

```

Response:


```json
{
  "status": "ACTIVE",
  "invalidFields": [],
  "message": "Account created successfully",
  "id": "f3ce697e-32e4-4d4a-a8ee-d0ffd738f6be"
}

```

  

Now we will liquidate $1 USDC to the Chase bank account we just added via the POST /transfer/crypto\-to\-fiat endpoint.


Request:

```curl
curl --request POST \
     --url https://production.hifibridge.com/transfer/crypto-to-fiat \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_1234567890' \
     --header 'content-type: application/json' \
     --data '
{
  "sourceCurrency": "usdc",
  "destinationCurrency": "usd",
  "chain": "POLYGON_MAINNET",
  "paymentRail": "ach",
  "sourceUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
  "requestId": "8f81b245-577a-4a39-b93c-5717c548e989",
  "destinationUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
  "destinationAccountId": "f3ce697e-32e4-4d4a-a8ee-d0ffd738f6be",
  "amount": 1
}
'

```

Response:


```json
{
  "transferType": "CRYPTO_TO_FIAT",
  "transferDetails": {
    "id": "f77eb6e4-8a10-4925-9c8d-0cf7ac0b563f",
    "requestId": "8f81b245-577a-4a39-b93c-5717c548e989",
    "sourceUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
    "destinationUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
    "chain": "POLYGON_MAINNET",
    "sourceCurrency": "usdc",
    "amount": 1,
    "destinationCurrency": "usd",
    "destinationAccountId": "f3ce697e-32e4-4d4a-a8ee-d0ffd738f6be",
    "createdAt": "2024-07-01T19:16:51.31129+00:00",
    "contractAddress": "0x3c499c542cEF5E3811e1192ce70d8cC03d5c3359",
    "failedReason": "",
    "transactionHash": "0xbc6efc49aa0b014bc8b71511371ebc7257507d056717f9d11c52e3be50aa870c",
    "status": "SUBMITTED_ONCHAIN"
  }
}

```

Once again, we want to poll the GET /transfer/crypto\-to\-fiat endpoint to make sure that the transaction was successful on the blockchain.


Request:


```curl
curl --request GET \
     --url 'https://production.hifibridge.com/transfer/crypto-to-fiat?id=f77eb6e4-8a10-4925-9c8d-0cf7ac0b563f' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_1234567890'

```

Response:


```json
{
  "transferType": "CRYPTO_TO_FIAT",
  "transferDetails": {
    "id": "f77eb6e4-8a10-4925-9c8d-0cf7ac0b563f",
    "requestId": "8f81b245-577a-4a39-b93c-5717c548e989",
    "sourceUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
    "destinationUserId": "ac1083dc-dbd9-40fe-a2aa-eb20e3ef643b",
    "chain": "polygon",
    "sourceCurrency": "usdc",
    "amount": "1",
    "destinationCurrency": "usd",
    "destinationAccountId": "f3ce697e-32e4-4d4a-a8ee-d0ffd738f6be",
    "transactionHash": "0xbc6efc49aa0b014bc8b71511371ebc7257507d056717f9d11c52e3be50aa870c",
    "createdAt": "2024-07-01T19:16:51.31129+00:00",
    "updatedAt": "2024-07-01T19:17:00.828+00:00",
    "status": "COMPLETED_ONCHAIN",
    "contractAddress": "0x3c499c542cEF5E3811e1192ce70d8cC03d5c3359"
  }
}

```

We see that the status is set to COMPLETED\_ONCHAIN, which indicates a successful liquidation. Unless there is a bank\-side error, we can expect the $1 to be deposited into the user's bank account in 3 business days.


  

Congratulations! You've successfully navigated through several key processes of converting fiat to USDC, transferring it to another user, and converting it back to fiat. We hope this guide has provided you with a solid understanding of how to utilize HIFI's API endpoints to manage digital currency transfers seamlessly. If you have any further questions or need additional support, please refer to our documentation or contact our support team. Happy coding!

Updated 5 days ago 
