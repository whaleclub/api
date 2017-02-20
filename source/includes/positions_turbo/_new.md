## New Turbo Position

> Open a 0.1BTC EUR/USD long turbo position:

```shell
curl "https://api.whaleclub.co/v1/position-turbo/new" \
  -H "Authorization: Bearer API_TOKEN" \
  -X POST \
  -d 'direction=long' \
  -d 'market=EUR-USD' \
  -d 'type=1min' \
  -d 'size=10000000'
```
```json
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
}
```

Open a new turbo position.

### Request

`POST https://api.whaleclub.co/v1/position-turbo/new` 

This endpoint allows you to open a new turbo position.

All turbo positions are executed at the market price, so there is no entry price to set.

If the request is successful, the API will return a `201` (Created) status code. 

Param | Description
---------- | -------
direction | **string** Required. Can be `long` or `short`.
market | **string** Required.
type | **string** Required. Contract type. Can be `1min` or `5min`.
size | **integer** Required. Your turbo position's size, in satoshis.

### Response

Returns your newly-submitted turbo position as a **[Turbo Position](#turbo-position-object)** object.

### Errors

In addition to the common API errors, this endpoint can return the following errors.

Name | Description
---------- | -------
Insufficient Balance | `403` You have insufficient available funds to open a position of this size.
Limits Exceeded | `403` Submitting this position would exceed the active volume limit for this market.
Position Max Exceeded | `403` You have too many positions that are active or pending. Please cancel or close some before opening more.
Contract Invalid | `403` This contract is not available for this market. Perhaps try another contract type?
Purchase Deadline Passed | `403` The purchase deadline has passed and this contract is no longer accepting new positions.
Market Disabled | `403` Longs (or shorts) are temporarily disabled for this market.
Market Closed | `403` This market is currently closed (e.g. on weekends for stocks) and not accepting new positions.
Price Unavailable | `500` Price is currently unavailable for this market.
Price Outdated | `500` Bid and ask prices are currently outdated for this market.
Market Maintenance | `503` The price feed for this market is currently under maintenance.