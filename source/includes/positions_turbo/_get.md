## Get Turbo Position

> Fetch an existing turbo position:

```shell
curl "https://api.whaleclub.co/v1/position-turbo/uWchea2SocXZHEiHS" \
  -H "Authorization: Bearer API_TOKEN"
```
```json
{
  "id": "uWchea2SocXZHEiHS",
  "direction": "long",
  "market": "EUR-USD",
  "state": "closed",
  "size": 6000000,
  "entry_price": 1.11825,
  "contract_id": "QvgMMkF35kSKowAtf",
  "payoff": 0.55,
  "close_price": 1.118293,
  "profit": 3300000,
  "created_at": 1464873229,
  "closed_at": 1464873270,
  "currency": "BTC"
}
```

Fetch information about an existing turbo position.

### Request

`GET https://api.whaleclub.co/v1/position-turbo/:id` 

If the request is successful, the API will return a `200` (Ok) status code. 

### Response

Returns a **[Turbo Position](#turbo-position-object)** object.