## List Positions

> List active positions:

```shell
curl "https://api.whaleclub.co/v1/positions/active" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -G \
  --data-urlencode "limit=10"
```
```json
[
  {
    "id": "22Eov6G9gXb7cC7n7",
    "slug": "47n2728b3",
    "direction": "long",
    "market": "NFLX",
    "leverage": 10,
    "type": "limit",
    "state": "active",
    "size": 2000000000,
    "margin_size": 200000000,
    "entry_price": 99,
    "stop_loss": 96.5,
    "take_profit": 126,
    "created_at": 1465795498,
    "entered_at": 1465795498,
    "liquidation_price": 91.08
  },
  {
    "id": "22bCNkWhiwxF7qAMs",
    "slug": "vCDQah7Hv",
    "direction": "long",
    "market": "EUR-USD",
    "leverage": 100,
    "type": "market",
    "state": "active",
    "size": 10000000000,
    "margin_size": 10000000000,
    "entry_price": 1.07876,
    "stop_loss": 1.07676,
    "take_profit": 1.08316,
    "created_at": 1486327152,
    "entered_at": 1486327152,
    "liquidation_price": 1.07013
  },
  ...
]
```

List positions.

### Request

`GET https://api.whaleclub.co/v1/positions/:state`

Use this endpoint to request a list of positions. `state` can be `pending`, `active`, or `closed`. Defaults to `active`.

It's strongly recommended that you maintain your own list of positions and use the [Price](#price) endpoint to keep it updated instead of polling this endpoint to track the state of your positions.

Pending positions are sorted by `created_at`, active positions are sorted by `entered_at`, and closed positions are sorted by `closed_at`.

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
limit | **integer** Optional. Number of results per request. Defaults to 5. Max is 30.

### Response

Returns an array of **[Position](#position-object)** objects.