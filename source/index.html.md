---
title: Whaleclub API Documentation

toc_footers:
  - <a href='https://trade.whaleclub.co/signup' target='_blank'>Sign Up for Whaleclub</a>

search: true
---

# Overview

Welcome to Whaleclub's API. You can use the API to programmatically submit new trades, check your balance, fetch trading history, and much more. You can both live trade and demo trade.

The API is organized around [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer). All requests should be made over SSL. All request and response bodies, including errors, are encoded in JSON (`application/json` content type).

Code samples are displayed in the right-side column. All epoch dates are in UTC seconds.

### BASE ENDPOINT URL

**`https://api.whaleclub.co/v1/`**

## Authentication

> Authenticate by passing your API token in an Authorization header:

```shell
curl "https://api.whaleclub.co/v1/balance"
  -H "Authorization: Bearer {API_TOKEN}"
```

**All requests to the Whaleclub API must be authenticated.**

Authentication is straightforward. Requests are authenticated by passing your API token as a bearer token in an `Authorization` header. 

`Authorization: Bearer {API_TOKEN}`

Get your API token from your [API Settings](https://trade.whaleclub.co/settings/api) page. You'll get one token for live trading and another for demo trading.

<aside class="notice">
You must replace <code>{API_TOKEN}</code> with your Whaleclub API token.
</aside>

## Referrals

If you're using the API in an app that allows your users to access Whaleclub, you can earn referral commissions when they trade.

Pass your Whaleclub referral code as a `Partner-ID` header in all the requests you make.

`Partner-ID: {YOUR_PARTNER_CODE}`

Get your partner code from your [API Settings](https://trade.whaleclub.co/wallet/referrals) page. Only trades with real funds will result in referral commissions.

Please keep in mind that the use of auto-referrals or multiple live trading accounts will result in permanent account closure without notice.

## Errors

> Sample error response – Validation error (400):

```json
{
  "error": {
    "name": "Validation Error",
    "message": "Please enter a valid amount."
  }
}
```

The Whaleclub API returns standard HTTP success or error status codes. In case of an error, we'll include extra information about what went wrong in the JSON-encoded response.

If the request is successful, the API will return either a `200` (OK) or a `201` (Created) status code. 

Code | Description
---------- | -------
400 | Validation error – unable to validate POST/PUT request
401 | Unauthorized – invalid API token
402 | Failed request
403 | Forbidden
404 | Resource not found
422 | Unprocessable entity – there was an issue parsing your request
423 | You've been blocked from making API requests
429 | Rate limit exceeded – too many requests
50x | Internal Server Error – Whaleclub server error

## Rate limits

> Check the headers of any response to see your rate limit status:

```shell
curl -i "https://api.whaleclub.co/v1/balance"
  -H "Authorization: Bearer {API_TOKEN}"

HTTP/1.1 200 OK
Date: Mon, 27 Feb 2016 21:20:00 GMT
Status: 200 OK
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 54
X-RateLimit-Reset: 1488230460
```

> Sample error response – Rate limit exceeded (429):

```json
{
  "error": {
    "name": "Rate Limit Exceeded",
    "message": "Slow down! You've exceeded the allocated rate limits."
  }
}
```

The Whaleclub API is rate limited to prevent abuse that would degrade our ability to maintain consistent API performance for all traders. 

By default, each API token is rate limited at **60 requests per minute**. If your requests are being rate limited, you'll receive a Rate Limit Exceeded error with status code `429`.

Requests are also throttled up to a maximum of 1 request per second.

You can check the HTTP headers of any response you get from the Whaleclub API to see your current rate limit status.

Header | Description
---------- | -------
<code>X-RateLimit-Limit</code> | The maximum number of requests allowed per minute.
<code>X-RateLimit-Remaining</code> | The number of requests left in the current rate limit window.
<code>X-RateLimit-Reset</code> | The time at which the current rate limit window resets, in UTC epoch seconds.

# Markets

> Request market information for Gold and Apple:

```shell
curl "https://api.whaleclub.co/v1/markets/XAU-USD,AAPL"
  -H "Authorization: Bearer {API_TOKEN}"
```
```json
{
  "XAU-USD": {
    "display_name": "Gold",
    "leverages": [2, 3.3, 5, 10],
    "limit": 5000000000,
    "hours": "Sun 5PM - Fri 5PM",
    "financing_rate": 0.0005,
    "category": "commodities",
    "turbo": {
      "payoff": {
        "5": 0.5
      },
      "limit": 10000000
    }
  },
  "AAPL": {
    "display_name": "Apple",
    "leverages": [3.3, 5, 10, 15],
    "limit": 3000000000,
    "hours": "Mon-Fri 8AM-5PM",
    "financing_rate": 0.001,
    "category": "stocks",
    "turbo": {
      "payoff": {
        "5": 0.5
      },
      "limit": 10000000
    }
  }
}
```

> Request a list of all available markets:

```shell
curl "https://api.whaleclub.co/v1/markets"
  -H "Authorization: Bearer {API_TOKEN}"
```
```json
{
  "BTC-USD": {
    "display_name": "BTC/USD",
    "category": "crypto"
  },
  "EUR-USD": {
    "display_name": "EUR/USD",
    "category": "forex"
  },
  "USD-JPY": {
    "display_name": "USD/JPY",
    "category": "forex"
  },
  "XAU-USD": {
    "display_name": "Gold",
    "category": "commodities"
  },
  "AAPL": {
    "display_name": "Apple",
    "category": "stocks"
  },
  ...
}
```

Returns market information for one or more markets.

### Request

`GET https://api.whaleclub.co/v1/markets/:symbol(s)`

`:symbol(s)` is a list of one or more comma-separated market symbols. You can request market information for up to 5 markets at once.

Omit `:symbol(s)` to get a list of of available markets with basic information such as display name and category.

Param | Description
---------- | -------
symbol(s) | **string** Optional. One or more comma-separated market symbols.

### Response

Attribute | Description
---------- | -------
display_name | **string** The market's conventional name.
leverages | **array** Leverage levels available.
limit | **integer** Maximum active position size, in satoshis
hours | **string** Market operating hours. Market is closed at all other times.
financing_rate | **number** Daily financing rate. Multiply by 100 to get the amount in percent.
category | **string** Asset class.
turbo | **object** Information about turbo trading, if it's available for this market. The **payoff** object contains the contract duration (in minutes) and the associated payoff.


# Price

> Request the current price for BTC/USD and EUR/USD:

```shell
curl "https://api.whaleclub.co/v1/price/BTC-USD,EUR-USD"
  -H "Authorization: Bearer {API_TOKEN}"
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


# Balance

> Request information about your current balance:

```shell
curl "https://api.whaleclub.co/v1/balance"
  -H "Authorization: Bearer {API_TOKEN}"
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

# Transactions

> List deposits:

```shell
curl "https://api.whaleclub.co/v1/transactions/deposits" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -G \
  --data-urlencode "limit=10"
```
```json
[
  {
    "id": "25RDQwc7LpPEFRQPC",
    "amount": 236427766,
    "state": "complete",
    "hash": "a6a5fd411cac34d35f562e8dd65d03173e55d35c58423464400b478b9f408172",
    "created_at": 1465526999
  },
  {
    "id": "25RDQwc7LpPEFRQPC",
    "amount": 100000000,
    "state": "complete",
    "hash": "b92841dc4ef1ec589fe717589b14424d792b93d13b56a78cc898cf2fd90005bf",
    "created_at": 1459560146
  },
  ...
]
```

List transactions.

### Request

`GET https://api.whaleclub.co/v1/transactions/:type`

Use this endpoint to request a list of transactions. `type` can be `deposits`, `withdrawals`, `referrals`, or `bonuses`. Defaults to `deposits`.

Transactions returned are sorted by `created_at`.

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
limit | **integer** Optional. Number of results per request. Defaults to 5. Max is 50.

### Response

Returns an array of transaction objects.

Attribute | Description
---------- | -------
id | **string** Unique ID for the transaction.
amount | **integer** Value of the transaction, in satoshis.
state | **string** Can be `pending` or `complete`. Appears only for deposits and withdrawals.
hash | **string** Bitcoin transaction hash. Appears only for deposits.
address | **string** Destination Bitcoin address. Appears only for withdrawals.
created_at | **integer** When the transaction was made.

# Positions

## Position Object

```shell
curl "https://api.whaleclub.co/v1/position/s6pGQ4nyS4Z7jHRvJ" \
  -H "Authorization: Bearer {API_TOKEN}"
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

This section provides a reference for the **Position** object, which represents a position, returned by position-related endpoints below.

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

## New Position

> Open a 100BTC EUR/USD long position at market price, with a stop-loss and take-profit:

```shell
curl "https://api.whaleclub.co/v1/position/new" \
  -H "Authorization: Bearer {API_TOKEN}" \
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
  -H "Authorization: Bearer {API_TOKEN}" \
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
  -H "Authorization: Bearer {API_TOKEN}" \
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
leverage | **integer** Required.
size | **integer** Required. Your position's size, in satoshis. This is the total size including leverage, not the margin size.
entry_price | **number** Optional. Set this to submit a limit/stop order. If omitted, your position will execute at the best available market price.
stop_loss | **number** Optional. Price at which your position will auto-close in case of loss.
take_profit | **number** Optional. Price at which your position will auto-close in profit.

### Response

Returns your newly-submitted position as a **[Position](#position-object)** object.

In particular, an `id` is generated for the position that you can use in later calls such as updating or closing.

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

## Get Position

> Fetch an existing position by passing its id to the request: 

```shell
curl "https://api.whaleclub.co/v1/position/s6pGQ4nyS4Z7jHRvJ" \
  -H "Authorization: Bearer {API_TOKEN}"
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

Fetch information about an existing position.

### Request

`GET https://api.whaleclub.co/v1/position/:id`

If the request is successful, the API will return a `200` (Ok) status code. 

### Response

Returns a **[Position](#position-object)** object.


## Update Position

> Set stop-loss and take-profit on an existing position:

```shell
curl "https://api.whaleclub.co/v1/position/update/22bCNkWhiwxF7qAMs" \
  -H "Authorization: Bearer {API_TOKEN}" \
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
  "liquidation_price": 1.07013
}
```

Update an existing position.

### Request

`PUT https://api.whaleclub.co/v1/position/update/:id` 

This endpoint allows you to update the stop-loss and/or take-profit of an existing pending or active position.

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
stop_loss | **number** Optional. Price at which the position will auto-close in case of loss. Must be set if `take_profit` is not. Set to `0` to remove.
take_profit | **number** Optional. Price at which the position will auto-close in profit. Must be set if `stop_loss` is not. Set to `0` to remove.

### Response

Returns a **[Position](#position-object)** object with updated values.

## Close Position

> Close a 100BTC EUR/USD long position at market price:

```shell
curl "https://api.whaleclub.co/v1/position/close/22bCNkWhiwxF7qAMs" \
  -H "Authorization: Bearer {API_TOKEN}" \
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
  "liquidation_price": 1.07013
}
```

Close an existing position at market price.

### Request

`PUT https://api.whaleclub.co/v1/position/close/:id`

Use this endpoint to close an existing active position at the best available market price.

If the request is successful, the API will return a `200` (Ok) status code.

### Response

Returns a **[Position](#position-object)** object with updated values.

### Errors

In addition to the common API errors, this endpoint can return the following errors.

Name | Description
---------- | -------
Market Closed | `403` This market is currently closed (e.g. on weekends for stocks) and not accepting new positions.
Price Unavailable | `500` Bid and ask prices are currently unavailable for this market.
Price Outdated | `500` Bid and ask prices are currently outdated for this market.
Market Maintenance | `503` The price feed for this market is currently under maintenance.

## Cancel Position

> Cancel a pending position:

```shell
curl "https://api.whaleclub.co/v1/position/cancel/d7gAxDSeLtdYtZsEd" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -X PUT
```
```json
{
  "id": "d7gAxDSeLtdYtZsEd",
  "cancelled": true
}
```

Cancel an existing pending position.

### Request

`PUT https://api.whaleclub.co/v1/position/cancel/:id`

This endpoint allows you to cancel a pending position that hasn't yet executed. Once cancelled, a position will be deleted and no longer have an id associated with it.

If the request is successful, the API will return a `200` (Ok) status code.

## Split Position

> Split an active 100BTC EUR/USD position into 2 smaller positions of 60BTC and 40BTC:

```shell
curl "https://api.whaleclub.co/v1/position/split/22bCNkWhiwxF7qAMs" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -X POST \
  -d 'ratio=60'
```
```json
[
  {
    "id": "RTcdY8cHxeptxZx9Q",
    "parent_id": "22bCNkWhiwxF7qAMs"
    "slug": "jYFs6Lpno",
    "size": 6000000000,
    "created_at": 1486327152
  },
  {
    "id": "vM7i73QipKc6F7SFs",
    "parent_id": "22bCNkWhiwxF7qAMs",
    "slug": "fDVhecYF",
    "size": 4000000000,
    "created_at": 1486327152
  }
```

Split an existing active position.

### Request

`POST https://api.whaleclub.co/v1/position/split/:id` 

This endpoint allows you to split an existing position. It can only be called on an active position.

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
ratio | **integer** Required. In percent. Must be between 5 and 95.

### Response

Returns an array of **[Position](#position-object)** objects containing two smaller positions resulting from the split.

## List Positions

> List active positions:

```shell
curl "https://api.whaleclub.co/v1/positions/active" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -G \
  --data-urlencode "limit=10"
```
```json
[
  {
    "id": "22Eov6G9gXb7cC7n7",
    "slug": "47n2728b3",
    "direction": "long",
    "market": "NFLX",
    "leverage": 10,
    "type": "limit",
    "state": "active",
    "size": 2000000000,
    "margin_size": 200000000,
    "entry_price": 99,
    "stop_loss": 96.5,
    "take_profit": 126,
    "created_at": 1465795498,
    "entered_at": 1465795498,
    "liquidation_price": 91.08
  },
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
  },
  ...
]
```

List positions.

### Request

`GET https://api.whaleclub.co/v1/positions/:state`

Use this endpoint to request a list of positions. `state` can be `pending`, `active`, or `closed`. Defaults to `active`.

It's strongly recommended that you maintain your own list of positions and use the [Price](#price) endpoint to keep it updated instead of polling this endpoint to track the state of your positions.

Pending positions are sorted by `created_at`, active positions are sorted by `entered_at`, and closed positions are sorted by `closed_at`.

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
limit | **integer** Optional. Number of results per request. Defaults to 5. Max is 30.

### Response

Returns an array of **[Position](#position-object)** objects.

# Positions – Turbo

## Turbo Position Object

```shell
curl "https://api.whaleclub.co/v1/position-turbo/uWchea2SocXZHEiHS" \
  -H "Authorization: Bearer {API_TOKEN}"
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

This section provides a reference for the **Turbo Position** object, which represents a turbo position, typically returned by most turbo position-related endpoints below.

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

## Get Active Contracts

> List active turbo contracts:

```shell
curl "https://api.whaleclub.co/v1/contracts" \
  -H "Authorization: Bearer {API_TOKEN}"
```
```json
[
  {
    "id": "pXuuzf2xZSZePXzvH",
    "type": "1min",
    "created_at": 1486347240,
    "purchase_deadline": 1486347300,
    "expires_at": 1486347330
  },
  {
    "id": "Cbrnx32XEZRyeQGJo",
    "type": "5min",
    "created_at": 1486347000,
    "purchase_deadline": 1486347300,
    "expires_at": 1486347450
  }
]
```

Fetch a list of currently active turbo contracts.

### Request

`GET https://api.whaleclub.co/v1/contracts`

This endpoint will return information about currently active contracts such as the purchase deadline and expiry time. Contracts apply across all markets.

If the request is successful, the API will return a `200` (Ok) status code. 

### Response

Returns an array of contracts.

Attribute | Description
---------- | -------
id | **string** Unique ID for the contract.
type | **string** Can be `1min` or `5min`.
created_at | **integer** When the contract first became active.
purchase_deadline | **integer** Time before which a turbo position must be submitted to be included in the contract.
expires_at | **integer** When the contract expires and turbo positions settle.

## New Turbo Position

> Open a 0.1BTC EUR/USD long turbo position:

```shell
curl "https://api.whaleclub.co/v1/position-turbo/new" \
  -H "Authorization: Bearer {API_TOKEN}" \
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

## Get Turbo Position

> Fetch an existing turbo position:

```shell
curl "https://api.whaleclub.co/v1/position-turbo/uWchea2SocXZHEiHS" \
  -H "Authorization: Bearer {API_TOKEN}"
```
```json
{
  "id": "uWchea2SocXZHEiHS",
  "direction": "long",
  "market": "EUR-USD",
  "state": "closed",
  "size": 6000000,
  "entry_price": 1.11825,
  "contract_id": "QvgMMkF35kSKowAtf",
  "payoff": 0.55,
  "close_price": 1.118293,
  "profit": 3300000,
  "created_at": 1464873229,
  "closed_at": 1464873270
}
```

Fetch information about an existing turbo position.

### Request

`GET https://api.whaleclub.co/v1/position-turbo/:id` 

If the request is successful, the API will return a `200` (Ok) status code. 

### Response

Returns a **[Turbo Position](#turbo-position-object)** object.

## List Turbo Positions

> List active turbo positions:

```shell
curl "https://api.whaleclub.co/v1/positions-turbo" \
  -H "Authorization: Bearer {API_TOKEN}" \
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
