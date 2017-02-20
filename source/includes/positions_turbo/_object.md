# Positions – Turbo

## Turbo Position Object

> Sample Turbo Position Object:

```shell
curl "https://api.whaleclub.co/v1/position-turbo/uWchea2SocXZHEiHS" \
  -H "Authorization: Bearer API_TOKEN"
```
```json
{
  "id": "uWchea2SocXZHEiHS",
  "contract_id": "QvgMMkF35kSKowAtf",
  "direction": "long",
  "market": "EUR-USD",
  "state": "closed",
  "size": 6000000,
  "entry_price": 1.11825,
  "payoff": 0.55,
  "close_price": 1.118293,
  "profit": 3300000,
  "created_at": 1464873229,
  "closed_at": 1464873270
}
```

This section provides a reference for the **Turbo Position** object which is returned by turbo position-related endpoints below.

Attribute | Description
---------- | -------
id | **string** Unique ID for the turbo position.
contract_id | **string** ID of the contract this turbo position belongs to.
direction | **string** Can be `long` or `short`.
market | **string** Market this turbo position was executed on.
state | **string** Can be `active`, or `closed`.
size | **integer** Position's size, in satoshis.
entry_price | **number** Price at which the turbo position was executed.
payoff | **number** Payoff in case of correct prediction. Multiply by size to get payoff in satoshis.
close_price | **number** Price at which the position was closed. Appears only if the position is `closed`.
profit | **number** Profit made on the trade, in satoshis. Is negative in case of loss. Appears only if the position is `closed`.
created_at | **integer** When the position was created.
closed_at | **integer** When the position was closed. Appears only if the position is `closed`.