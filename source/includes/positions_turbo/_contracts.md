## Get Active Contracts

> List active turbo contracts:

```shell
curl "https://api.whaleclub.co/v1/contracts" \
  -H "Authorization: Bearer {API_TOKEN}"
```
```json
[
  {
    "id": "pXuuzf2xZSZePXzvH",
    "type": "1min",
    "created_at": 1486347240,
    "purchase_deadline": 1486347300,
    "expires_at": 1486347330
  },
  {
    "id": "Cbrnx32XEZRyeQGJo",
    "type": "5min",
    "created_at": 1486347000,
    "purchase_deadline": 1486347300,
    "expires_at": 1486347450
  }
]
```

Fetch a list of currently active turbo contracts.

### Request

`GET https://api.whaleclub.co/v1/contracts`

This endpoint will return information about currently active contracts such as the purchase deadline and expiry time. 

When you submit a new turbo position, it'll be on one of the active contracts you get from here.

If the request is successful, the API will return a `200` (Ok) status code. 

### Response

Returns an array of contracts.

Attribute | Description
---------- | -------
id | **string** Unique ID for the contract.
type | **string** Can be `1min` or `5min`.
created_at | **integer** When the contract first became active.
purchase_deadline | **integer** Time before which a turbo position must be submitted to be included in the contract.
expires_at | **integer** When the contract expires and turbo positions settle.