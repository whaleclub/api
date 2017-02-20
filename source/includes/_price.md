# Price

> Request the current price for BTC/USD and EUR/USD:

```shell
curl "https://api.whaleclub.co/v1/price/BTC-USD,EUR-USD"
  -H "Authorization: Bearer API_TOKEN"
```
```json
{
  "BTC-USD": {
    "bid": 1034.52,
    "ask": 1035.42,
    "state": "open",
    "last_updated": 1486283460
  },
  "EUR-USD": {
    "bid": 1.07846,
    "ask": 1.07856,
    "state": "open",
    "last_updated": 1486283454
  }
}
```

Returns the current bid and ask prices for one or more markets.

### Request

`GET https://api.whaleclub.co/v1/price/:symbol(s)`

`:symbol(s)` is a list of one or more comma-separated market symbols. You can request price for up to 5 markets at once.

You can get a list of available symbols using the [Markets](#markets) endpoint. 

Param | Description
---------- | -------
symbol(s) | **string** Required. One or more comma-separated market symbols.

### Response

Attribute | Description
---------- | -------
bid | **number** The current bid price.
ask | **number** The current ask price.
state | **string** Can be `open`, `closed`, `pre` (pre-market trading – stocks only), or `after` (after-market trading – stocks only)
last_updated | **integer** When prices for this market were last updated.