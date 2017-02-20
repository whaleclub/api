# Balance

> Request information about your current balance:

```shell
curl "https://api.whaleclub.co/v1/balance"
  -H "Authorization: Bearer API_TOKEN"
```
```json
{
  "available_amount": 1000000000,
  "total_amount": 1200000000,
  "unconfirmed_balance": 0,
  "deposit_address": "1JZZNHDNrXtdfGduRiSXANvBRZhgoa83zo",
  "active_amount": {
    "BTC-USD": 400000000,
    "AAPL": 900000000,
    "EUR-USD": 4000000000
  },
  "pending_amount": {
    "USD-JPY": 1000000000
  },
  "active_amount_turbo": {
    "EUR-JPY": 10000000,
    "XAU-USD": 5000000
  },
  "last_updated": 1485645703
}
```

Returns information about your balance.

### Request

`GET https://api.whaleclub.co/v1/balance`

Real or demo balance information will be returned based on whether you use your live or demo API token.

### Response

Attribute | Description
---------- | -------
available_amount | **integer** Balance available to trade, in satoshis.
total_amount | **integer** Total balance, in satoshis.
unconfirmed_amount | **integer** Deposit amount that has not yet confirmed, in satoshis.
deposit_address | **string** Your deposit address.
active_amount | **object** Balance used in active positions across markets, in satoshis.
pending_amount | **object** Balance used in pending positions across markets, in satoshis.
active_amount_turbo | **object** Balance used in active turbo positions across markets, in satoshis.
last_updated | **integer** When your balance was last updated.