## New Position

> Open a 100BTC EUR/USD long position at market price, with a stop-loss and take-profit:

```shell
curl "https://api.whaleclub.co/v1/position/new" \
  -H "Authorization: Bearer API_TOKEN" \
  -X POST \
  -d 'direction=long' \
  -d 'market=EUR-USD' \
  -d 'leverage=100' \
  -d 'size=10000000000' \
  -d 'stop_loss=1.07676' \
  -d 'take_profit=1.08316'
```
```json
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
}
```

> Submit a 50BTC Gold sell stop position at 1050:

```shell
curl "https://api.whaleclub.co/v1/position/new" \
  -H "Authorization: Bearer API_TOKEN" \
  -X POST \
  -d 'direction=short' \
  -d 'market=XAU-USD' \
  -d 'leverage=10' \
  -d 'size=5000000000' \
  -d 'entry_price=1050'
```
```json
{
  "id": "d7gAxDSeLtdYtZsEd",
  "slug": "GaK75ndGm",
  "direction": "short",
  "market": "XAU-USD",
  "leverage": 10,
  "type": "stop",
  "state": "pending",
  "size": 5000000000,
  "margin_size": 500000000,
  "entry_price": 1050,
  "created_at": 1486307187,
  "liquidation_price": 1150
}
```

> Submit a 20BTC Netflix limit long position at 99:

```shell
curl "https://api.whaleclub.co/v1/position/new" \
  -H "Authorization: Bearer API_TOKEN" \
  -X POST \
  -d 'direction=long' \
  -d 'market=NFLX' \
  -d 'leverage=10' \
  -d 'size=2000000000' \
  -d 'entry_price=99' \
  -d 'stop_loss=96.5' \
  -d 'take_profit=126'
```
```json
{
  "id": "22Eov6G9gXb7cC7n7",
  "slug": "47n2728b3",
  "direction": "long",
  "market": "NFLX",
  "leverage": 10,
  "type": "limit",
  "state": "pending",
  "size": 2000000000,
  "margin_size": 200000000,
  "entry_price": 99,
  "stop_loss": 96.5,
  "take_profit": 126,
  "created_at": 1465795498,
  "liquidation_price": 91.08
}
```

Submit a new position.

### Request

`POST https://api.whaleclub.co/v1/position/new` 

This endpoint allows you to submit a new position.

To submit a **limit or stop order**, set the `entry_price` parameter in your request. We'll automatically detect whether it's a limit order or a stop order based on the current market price.

To submit a **market order**, simply omit the `entry_price` parameter from your request. Your order will execute at the best available price.

If the request is successful, the API will return a `201` (Created) status code. 

Param | Description
---------- | -------
direction | **string** Required. Can be `long` or `short`.
market | **string** Required.
leverage | **number** Required.
size | **integer** Required. Your position's size, in satoshis. This is the total size including leverage, not the margin size.
entry_price | **number** Optional. Set this to submit a limit/stop order. If omitted, your position will execute at the best available market price.
stop_loss | **number** Optional. Price at which your position will auto-close in case of loss.
take_profit | **number** Optional. Price at which your position will auto-close in profit.

### Response

Returns your newly-submitted position as a **[Position](#position-object)** object.

In particular, an `id` is generated for the position that you can use in later calls such as to update or close it.

### Errors

In addition to the common API errors, this endpoint can return the following errors.

Name | Description
---------- | -------
Insufficient Balance | `403` You have insufficient available funds to open this position.
Limits Exceeded | `403` Submitting this position would exceed the active volume limit for this market.
Position Max Exceeded | `403` You have too many positions that are active or pending. Please cancel or close some before opening additional positions.
Limit Orders Only | `403` Only limit orders are allowed in pre-market and after-hours trading.
Entry Too Far | `403` Your entry price is too far from the current market price.
Market Disabled | `403` Longs (or Shorts) are temporarily disabled for this market.
Market Closed | `403` This market is currently closed (e.g. on weekends for stocks).
Price Unavailable | `500` Bid and ask prices are currently unavailable for this market. Please try again later.
Price Outdated | `500` Bid and ask prices are currently outdated for this market.  Please try again later.
Market Maintenance | `503` The price feed for this market is currently under maintenance. Please try again later.