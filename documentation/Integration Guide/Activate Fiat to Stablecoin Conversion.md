To on\-ramp a user for a fiat and stablecoin conversion pair, you need to activate the user from a supported rail to a destination currency and chain through the [activateOnRampRail](/reference/add-a-bank-account-for-usd-onramp-plaid-copy) endpoint. 


**Request**


cURL
```
curl --request POST \
     --url 'https://production.hifibridge.com/account/activateOnRampRail?userId=75d7c01f-5f93-4490-8b93-a62fd8020358' \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "rail": "US_ACH",
  "destinationCurrency": "usdc",
  "destinationChain": "POLYGON_MAINNET"
}
'

```

**Response**


JSON
```
{
  "message": "US_ACH rail created successfully"
}

```

Ready for the next step?
========================


After activating the fiat and stablecoin conversion for the user, you can now add new accounts. Learn more about how to [add new accounts](/docs/add-accounts) for the user.

Updated 13 days ago 



---

What’s Next* [Add Accounts](/docs/add-accounts)
