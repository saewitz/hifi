HIFI users can do transfer between users and wallet addresses on or between fiats and stable coins.


1\. Crypto\-To\-Crypto
======================


The [crypto\-to\-crypto endpoint](/reference/add-a-bank-account-for-usd-offramp-copy-1) allows users to transfer stable coins between users or wallet addresses.


**Request**


cURL
```
curl --request POST \
     --url https://production.hifibridge.com/transfer/crypto-to-crypto \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "chain": "POLYGON_MAINNET",
  "currency": "usdc",
  "requestId": "5d0a55aa-9a2a-486a-a57e-d5d9e576cf6d",
  "senderUserId": "75d7c01f-5f93-4490-8b93-a62fd8020358",
  "recipientUserId": "0c91a2a3-b0da-4779-bd15-2b29b8854de4",
  "recipientAddress": "0x89dfD8792c7E7041c24F01223929F1d8Dd642F0c",
  "amount": 0.01
}
'

```

**Response**


JSON
```
{
  "transferType": "CRYPTO_TO_CRYPTO",
  "transferDetails": {
    "id": "0b9a8c80-3ce3-4aee-9d94-8a8ac43a2543",
    "requestId": "5d0a55aa-9a2a-486a-a57e-d5d9e576cf6d",
    "senderUserId": "75d7c01f-5f93-4490-8b93-a62fd8020358",
    "recipientUserId": "0c91a2a3-b0da-4779-bd15-2b29b8854de4",
    "recipientAddress": "0x89dfD8792c7E7041c24F01223929F1d8Dd642F0c",
    "chain": "POLYGON_MAINNET",
    "currency": "usdc",
    "amount": 0.01,
    "transactionHash": "0x23188ac9f798d97d481f86fe1904a74cd885ed8bcff9d52d8bb7c3a2f54a3a59",
    "createdAt": "2024-06-25T20:41:57.671322+00:00",
    "status": "SUBMITTED",
    "contractAddress": "0x3c499c542cEF5E3811e1192ce70d8cC03d5c3359",
    "failedReason": ""
  }
}

```

2\. Crypto\-To\-Fiat
====================


The [crypto\-to\-fiat endpoint](/reference/transfer-crypto-between-user-copy) allows users to liquidate stablecoin into fiat currency and send to a destination bank account.


**Request**


cURL
```
curl --request POST \
     --url https://production.hifibridge.com/transfer/crypto-to-fiat \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "sourceCurrency": "usdc",
  "destinationCurrency": "usd",
  "chain": "POLYGON_MAINNET",
  "paymentRail": "ach",
  "requestId": "233fb9a1-cf0d-425a-9d71-2f4b84040160",
  "sourceUserId": "75d7c01f-5f93-4490-8b93-a62fd8020358 ",
  "destinationUserId": "b7eef868-c72c-4215-a2b9-38272e362b5a ",
  "destinationAccountId": "daa6ad75-a4c2-486f-a937-1bbf4d19553c ",
  "amount": 0.01,
}
'

```

**Response**


JSON
```
{
  "transferType": "CRYPTO_TO_FIAT",
  "transferDetails": {
    "id": "cb586edd-5f9c-475e-8909-4c65c2f03243",
    "requestId": "233fb9a1-cf0d-425a-9d71-2f4b84040160",
    "userId": "75d7c01f-5f93-4490-8b93-a62fd8020358",
    "chain": "POLYGON_MAINNET",
    "sourceCurrency": "usdc",
    "amount": 0.01,
    "destinationCurrency": "usd",
    "destinationAccountId": "daa6ad75-a4c2-486f-a937-1bbf4d19553c",
    "createdAt": "2024-06-25T20:58:32.152865+00:00",
    "status": "SUBMITTED_ONCHAIN",
    "contractAddress": "0x3c499c542cEF5E3811e1192ce70d8cC03d5c3359"
  }
}

```

3\. Fiat\-To\-Crypto
====================


The [fiat\-to\-crypto endpoint](/reference/transfer-crypto-to-fiat-bank-account-copy) allows users to move fiat funds in a bank account into stable coin on the blockchain.


**Request**


cURL
```
curl --request POST \
     --url https://production.hifibridge.com/transfer/fiat-to-crypto \
     --header 'accept: application/json' \
     --header 'authorization: Bearer zpka_123456789' \
     --header 'content-type: application/json' \
     --data '
{
  "sourceCurrency": "usd",
  "destinationCurrency": "usdc",
  "chain": "POLYGON_MAINNET",
  "isInstant": false,
  "requestId": "984da3b6-b930-4641-87ab-93fc131ba0f8",
  "sourceUserId": "75d7c01f-5f93-4490-8b93-a62fd8020358",
  "sourceAccountId": "0de2ae79-737d-4266-8c7d-ec82df476d3a",
  "destinationUserId": "75d7c01f-5f93-4490-8b93-a62fd8020358",
  "amount": 0
}
'

```

**Response**


JSON
```
{
  "transferType": "FIAT_TO_CRYPTO",
  "transferDetails": {
    "id": "312410a2-9c1b-4337-afeb-71dad9da3428",
    "requestId": "984da3b6-b930-4641-87ab-93fc131ba0f8",
    "sourceUserId": "75d7c01f-5f93-4490-8b93-a62fd8020358",
    "destinationUserId": "75d7c01f-5f93-4490-8b93-a62fd8020358",
    "chain": "POLYGON_MAINNET",
    "sourceCurrency": "usd",
    "amount": 1,
    "destinationCurrency": "usdc",
    "sourceAccountId": "0de2ae79-737d-4266-8c7d-ec82df476d3a",
    "createdAt": "2024-06-26T18:50:16.653318+00:00",
    "status": "FIAT_SUBMITTED",
    "isInstant": false
  }
}

```

🎉 Congratulations. You have successfully do a transfer between HIFI users or wallets.

Updated 13 days ago 



---

