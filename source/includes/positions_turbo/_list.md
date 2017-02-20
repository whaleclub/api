## List Turbo Positions

> List active turbo positions:

```shell
curl "https://api.whaleclub.co/v1/positions-turbo" \
  -H "Authorization: Bearer API_TOKEN" \
  -G \
  --data-urlencode "limit=10"
```
```json
[
  {
    "id": "uWchea2SocXZHEiHS",
    "contract_id": "QvgMMkF35kSKowAtf",
    "direction": "long",
    "market": "EUR-USD",
    "state": "active",
    "size": 10000000,
    "entry_price": 1.11825,
    "payoff": 0.55,
    "created_at": 1464873229,
    "expires_at": 1464873429
  },
  {
    "id": "26fTLb7byb8fi48Qf",
    "contract_id": "QvgMMkF35kSKowAtf",
    "direction": "long",
    "market": "AUD-USD",
    "state": "active",
    "size": 8000000,
    "entry_price": 0.76661,
    "payoff": 0.65,
    "created_at": 1464873219,
    "expires_at": 1464873409
  },
  ...
]
```

List turbo positions.

### Request

`GET https://api.whaleclub.co/v1/positions-turbo/:state`

Use this endpoint to request a list of turbo positions. `state` can be `active` or `closed`. Defaults to `active`.

Active positions are sorted by `created_at` and closed positions are sorted by `closed_at`.

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
limit | **integer** Optional. Number of results per request. Defaults to 5. Max is 30.

### Response

Returns an array of **[Turbo Position](#turbo-position-object)** objects.


<div class='bottom-padder'>&nbsp;</div>