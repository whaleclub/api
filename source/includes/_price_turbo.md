# Price – Turbo

> Request the current turbo price for USD/JPY and WTI Crude Oil:

```shell
curl "https://api.whaleclub.co/v1/price-turbo/USD-JPY,WTICO-USD"
  -H "Authorization: Bearer {API_TOKEN}"
```
```json
{
  "USD-JPY": {
    "mid": 112.593,
    "state": "open",
    "last_updated": 1486159183
  },
  "WTICO-USD": {
    "mid": 44.0912,
    "state": "open",
    "last_updated": 1474676038
  }
}
```

Returns the current turbo price for one or more markets.

### Request

`GET https://api.whaleclub.co/v1/price-turbo/:symbol(s)`

`:symbol(s)` is a list of one or more comma-separated market symbols. You can request turbo price for up to 5 markets at once.

You can fetch available symbols using the [Markets](#markets) endpoint. 

Param | Description
---------- | -------
symbol(s) | **string** Required. One or more comma-separated market symbols.

### Response

Attribute | Description
---------- | -------
mid | **number** The current turbo price.
state | **string** Can be `open` or `closed`.
last_updated | **integer** When turbo price for this market was last updated.