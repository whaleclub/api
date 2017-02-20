# Transactions

> List deposits you've made to your Whaleclub wallet:

```shell
curl "https://api.whaleclub.co/v1/transactions/deposits" \
  -H "Authorization: Bearer API_TOKEN" \
  -G \
  --data-urlencode "limit=10"
```
```json
[
  {
    "id": "25RDQwc7LpPEFRQPC",
    "amount": 236427766,
    "state": "complete",
    "hash": "a6a5fd411cac34d35f562e8dd65d03173e55d35c58423464400b478b9f408172",
    "created_at": 1465526999
  },
  {
    "id": "25RDQwc7LpPEFRQPC",
    "amount": 100000000,
    "state": "complete",
    "hash": "b92841dc4ef1ec589fe717589b14424d792b93d13b56a78cc898cf2fd90005bf",
    "created_at": 1459560146
  },
  ...
]
```

List transactions that have occurred on your account.

### Request

`GET https://api.whaleclub.co/v1/transactions/:type`

Use this endpoint to request a list of transactions. `type` can be `deposits`, `withdrawals`, `referrals`, or `bonuses`. Defaults to `deposits`.

Transactions returned are sorted by creation date (`created_at`).

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
limit | **integer** Optional. Number of results per request. Defaults to 5. Max is 50.

### Response

Returns an array of transaction objects.

Attribute | Description
---------- | -------
id | **string** Unique ID for the transaction.
amount | **integer** Value of the transaction, in satoshis.
state | **string** Can be `pending` or `complete`. Appears only for deposits and withdrawals.
hash | **string** Bitcoin transaction hash. Appears only for deposits.
address | **string** Destination Bitcoin address. Appears only for withdrawals.
created_at | **integer** When the transaction was made.