## Get Position

> Fetch an existing position by passing its id to the request: 

```shell
curl "https://api.whaleclub.co/v1/position/s6pGQ4nyS4Z7jHRvJ" \
  -H "Authorization: Bearer {API_TOKEN}"
```
```json
{
  "id": "s6pwQ4nyS4Z7jHRvJ",
  "slug": "47n2728b3",
  "direction": "long",
  "market": "NFLX",
  "leverage": 10,
  "type": "limit",
  "state": "closed",
  "size": 2000000000,
  "margin_size": 200000000,
  "entry_price": 99,
  "stop_loss": 96.5,
  "take_profit": 126,
  "close_reason": "market",
  "close_price": 122.81,
  "profit": 481000000,
  "created_at": 1465795498,
  "entered_at": 1465795598,
  "closed_at": 1465799498,
  "last_updated": 1465797498,
  "liquidation_price": 91.08,
  "financing": 120000
}
```

Fetch information about an existing position.

### Request

`GET https://api.whaleclub.co/v1/position/:id`

If the request is successful, the API will return a `200` (Ok) status code. 

### Response

Returns a **[Position](#position-object)** object.