## Update Position

> Set stop-loss and take-profit on an existing position:

```shell
curl "https://api.whaleclub.co/v1/position/update/22bCNkWhiwxF7qAMs" \
  -H "Authorization: Bearer API_TOKEN" \
  -X PUT \
  -d 'stop_loss=1.07576' \
  -d 'take_profit=1.08726'
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
  "margin_size": 100000000,
  "entry_price": 1.07876,
  "stop_loss": 1.07576,
  "take_profit": 1.08726,
  "created_at": 1486327152,
  "entered_at": 1486327152,
  "last_updated": 1486367152,
  "liquidation_price": 1.07013,
  "currency": "BTC"
}
```

Update an existing position.

### Request

`PUT https://api.whaleclub.co/v1/position/update/:id` 

This endpoint allows you to update the stop-loss and/or take-profit of an existing pending or active position.

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
stop_loss | **number** Optional. Price at which the position will auto-close in case of loss. Must be set if `take_profit` is not. Set to `0` to remove an existing stop-loss.
stop_loss_trailing | **boolean or number** Optional. Set to `true` to enable the stop loss to trail. Works only if `stop_loss` is set. Set to `0` to disable trailing.
take_profit | **number** Optional. Price at which the position will auto-close in profit. Must be set if `stop_loss` is not. Set to `0` to remove an existing take-profit.

### Response

Returns a **[Position](#position-object)** object with updated values.