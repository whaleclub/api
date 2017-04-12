## Close Position

> Close a 100BTC EUR/USD long position at market price:

```shell
curl "https://api.whaleclub.co/v1/position/close/22bCNkWhiwxF7qAMs" \
  -H "Authorization: Bearer API_TOKEN" \
  -X PUT
```
```json
{
  "id": "22bCNkWhiwxF7qAMs",
  "slug": "vCDQah7Hv",
  "direction": "long",
  "market": "EUR-USD",
  "leverage": 100,
  "type": "market",
  "state": "closed",
  "size": 10000000000,
  "margin_size": 100000000,
  "entry_price": 1.07876,
  "stop_loss": 1.07576,
  "take_profit": 1.08726,
  "close_reason": "market",
  "close_price": 1.08126,
  "profit": 23000000,
  "created_at": 1486327152,
  "entered_at": 1486327152,
  "closed_at": 1486328121,
  "last_updated": 1486327452,
  "liquidation_price": 1.07013,
  "currency": "BTC"
}
```

Close one or multiple active positions at market price.

### Request

`PUT https://api.whaleclub.co/v1/position/close/:id(s)`

`:id(s)` is a list of one or more comma-separated position IDs.

Use this endpoint to close existing active positions at the best available market price.

Positions are closed sequentially as they hit our system (not in parallel). If you're closing a large number of positions at once, the market price may move in the time it takes to close them all. If you're looking to close at a specific market price, close your positions individually or use a take-profit or stop-loss.

If the request is successful, the API will return a `200` (Ok) status code.

### Response

Returns a **[Position](#position-object)** object with updated values.

### Errors

In addition to the common API errors, this endpoint can return the following errors.

Name | Description
---------- | -------
Market Closed | `403` This market is currently closed (e.g. on weekends for stocks).
Price Unavailable | `500` Bid and ask prices are currently unavailable for this market.
Price Outdated | `500` Bid and ask prices are currently outdated for this market.
Market Maintenance | `503` The price feed for this market is currently under maintenance.