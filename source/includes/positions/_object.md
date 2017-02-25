# Positions

## Position Object

> Sample Position Object:

```shell
curl "https://api.whaleclub.co/v1/position/s6pGQ4nyS4Z7jHRvJ" \
  -H "Authorization: Bearer API_TOKEN"
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

This section provides a reference for the **Position** object which is returned by position-related endpoints below.

Attribute | Description
---------- | -------
id | **string** Unique ID for the position.
parent_id | **string** ID of parent position. Appears only if this position has been [split](#split-position).
slug | **string** A URL-friendly position identifier. Your position can be shared publicly at `https://whaleclub.co/position/:slug`.
direction | **string** Can be `long` or `short`.
market | **string** Market this position was executed on.
leverage | **number** Position's leverage level.
type | **string** Order type. Can be `market`, `limit`, or `stop`.
state | **string** Can be `pending`, `active`, or `closed`.
size | **integer** Position's size, in satoshis.
margin_size | **integer** Position's margin size, in satoshis.
entry_price | **number** Price at which the position was executed (if at market) or will execute (if limit or stop).
stop_loss | **number** Price at which the position will auto-close in case of loss. Appears only if the position's stop-loss is set.
stop_loss_trailing | **object** Returns `{set: true}` if the stop loss is a trailing stop loss.
take_profit | **number** Price at which the position will auto-close in profit. Appears only if the position's take-profit is set.
close_reason | **string** How the position was closed. Can be `at_market`, `at_stop`, `at_target`, or `liquidation`. Appears only if the position is `closed`.
close_price | **number** Price at which the position was closed. Appears only if the position is `closed`.
profit | **integer** Profit made on the trade, in satoshis. Negative in case of loss. Appears only if the position is `closed`.
created_at | **integer** When the position was created.
entered_at | **integer** When the position was executed. Appears only if the position is `active` or `closed`.
closed_at | **integer** When the position was closed. Appears only if the position is `closed`.
last_updated | **integer** When the position's stop-loss and/or take-profit was last updated. Appears only if the position is manually updated after it's been submitted.
liquidation_price | **number** Price at which the position will auto-close to cover your margin in case of loss.
financing | **integer** Leverage financing charged on the position, in satoshis. Appears only if the position is `active` or `closed`.