---
title: Whaleclub API Documentation

language_tabs:
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>

search: true
---

# Overview

Welcome to Whaleclub's API! You can use our API to programmatically submit new trades, check your balance, fetch trading history, and much more. You can use the API to both trade with real funds and demo trade.

The API is organized around [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer). All requests should be made over SSL. All request and response bodies, including errors, are encoded in JSON.

You can view code samples in the right-side column. Epoch dates are in UTC seconds.

### BASE ENDPOINT URL
`https://api.whaleclub.co/v1/`

# Authentication

> Authenticate by passing a bearer token in an Authorization header:

```shell
curl "https://api.whaleclub.co/v1/balance"
  -H "Authorization: Bearer {API_TOKEN}"
```

Authentication is done via an API token that you can get from your [API Settings](https://trade.whaleclub.co/settings/api) page. You'll get one token for your live trading account and another for demo trading.

Requests are authenticated by passing your API token as a bearer token in an `Authorization` header. All Whaleclub API requests must be authenticated.

`Authorization: Bearer {API_TOKEN}`

<aside class="notice">
You must replace <code>{API_TOKEN}</code> with your API token.
</aside>

# Errors

> Validation failed (400)

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

# Rate limiting

> Check the headers of any response to see rate limit status

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

> Rate limit exceeded (429)

```json
{
  "error": {
    "name": "Rate Limit Exceeded",
    "message": "Slow down! You've exceeded the allocated rate limits."
  }
}
```

The Whaleclub API is rate limited to prevent abuse that would degrade our ability to maintain consistent API performance for all traders. By default, each API token is rate limited at 60 requests per minute. If your requests are being rate limited, you'll receive a Rate Limit Exceeded error with status code `429`.

You can check the HTTP headers of any response you get from the Whaleclub API to see your current rate limit status.

Header | Description
---------- | -------
<code>X-RateLimit-Limit</code> | The maximum number of requests allowed per minute
<code>X-RateLimit-Remaining</code> | The number of requests remaining in the current rate limit window
<code>X-RateLimit-Reset</code> | The time at which the current rate limit window resets, in UTC epoch seconds

# API Reference

# Markets

> Request market information for Gold and Apple

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

> List all available markets

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
hours | **string** Opening hours.
financing_rate | **number** Daily rate levied as a fraction of the active position size, if leverage is used.
category | **string** Asset class.
turbo | **object** Information about turbo trading, if it's available for this market. The **payoff** object contains the contract duration (in minutes) and the associated payoff.


# Price

> Request the current price for BTC/USD and EUR/USD

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

You can fetch available symbols using the `/markets` endpoint. 

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


# Price Turbo

> Request the current turbo price for USD/JPY and Crude Oil (WTI)

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

You can fetch available symbols using the `/markets` endpoint. 

Param | Description
---------- | -------
symbol(s) | **string** Required. One or more comma-separated market symbols.

### Response

Attribute | Description
---------- | -------
mid | **number** The current turbo price.
state | **string** Can be `open` or `closed`
last_updated | **integer** When turbo price for this market was last updated.


# Balance

> Request information about your balance

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

# Position

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
  "financing": 120000,
}
```

This section provides a reference for the **Position** object, which represents a position, typically returned by most position-related endpoints below.

Attribute | Description
---------- | -------
id | **string** Unique ID for the position.
slug | **string** A URL-friendly position identifier. Your position can be shared publicly at `https://whaleclub.co/position/:slug`.
direction | **string** Can be `long` or `short`.
market | **string** Market this position was executed on.
leverage | **number** The position's leverage level.
type | **string** Order type. Can be `market`, `limit`, or `stop`.
state | **string** Can be `pending`, `active`, or `closed`.
size | **integer** The position's size, in satoshis.
margin_size | **integer** The position's margin size, in satoshis.
entry_price | **number** Price at which the position was executed (if at market) or will execute (if limit or stop).
stop_loss | **number** Price at which the position will auto-close in case of loss. Appears only if the position's stop-loss is set.
take_profit | **number** Price at which the position will auto-close in profit. Appears only if the position's take-profit is set.
close_reason | **string** How the position was closed. Can be `market`, `stop-loss`, `take-profit`, or `liquidation`. Appears only if the position is `closed`.
close_price | **number** Price at which the position was closed. Appears only if the position is `closed`.
profit | **number** Profit made on the trade, in satoshis. Is negative in case of loss. Appears only if the position is `closed`.
created_at | **integer** When the position was created.
entered_at | **integer** When the position was executed. Appears only if the position is `active` or `closed`.
closed_at | **integer** When the position was closed. Appears only if the position is `closed`.
last_updated | **integer** When the position's stop-loss and/or take-profit was last updated. Appears only if the position is manually updated after it's been submitted.
liquidation_price | **number** Price at which the position will auto-close to cover your margin in case of loss.
financing | **integer** Leverage financing charged on the position, in satoshis. Appears only if the position is `active` or `closed`.


## New Position

> Open a 100BTC EUR/USD long position at market price, with stop-loss and take-profit

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

> Submit a 50BTC Gold sell stop position at 1050

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

> Submit a 20BTC Netflix limit long position at 99

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

To submit a limit or stop order, set the `entry_price` parameter in your request.

To submit a position that will execute immediately at market price, simply omit the `entry_price` parameter from your request.

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

Returns a **[Position](#position-object)** containing the values of your newly submitted position.

### Errors

In addition to the common API errors, this endpoint can return the following errors under the `403` status code.

Name | Description
---------- | -------
Insufficient Balance | You have insufficient available funds to open a position of this size.
Limits Exceeded | Submitting this position would exceed the active volume limit for this market.
Position Max Exceeded | You have too many positions that are active or pending. Please cancel or close some before opening more.
Limit Orders Only | Only limit orders are allowed in pre-market and after-hours trading.
Market Disabled | Longs (or shorts) are temporarily disabled for this market.
Market Closed | This market is currently closed (e.g. on weekends for stocks) and not accepting new positions.
Price Unavailable | Bid and ask prices are currently unavailable for this market.
Price Outdated | Bid and ask prices are currently outdated for this market.
Market Maintenance | The price feed for this market is currently under maintenance.

## Update Position

> Set stop-loss and take-profit for an existing position 

```shell
curl "https://api.whaleclub.co/v1/position/update" \
  -H "Authorization: Bearer {API_TOKEN}" \
  -X POST \
  -d 'id=22bCNkWhiwxF7qAMs' \
  -d 'stop_loss=1.07576' \
  -d 'take_profit=1.08726'
```
```json
{
  "id": "22bCNkWhiwxF7qAMs",
  "entry_price": 1.07876,
  "created_at": 1486327152,
  "entered_at": 1486327152,
  "state": "active",
  "slug": "vCDQah7Hv",
  "type": "market",
  "size": 10000000000,
  "margin_size": 100000000,
  "liquidation_price": 1.07013,
  "stop_loss": 1.07576,
  "take_profit": 1.08726,
  "leverage": 100,
  "market": "EUR-USD",
  "direction": "long"
}
```

## Close Position

## Cancel Position

## Split Position

# Positions

## List Pending Positions

## List Active Positions

## List Closed Positions

# Positions Turbo

## New Turbo Position

## List Active Turbo Positions

## List Closed Turbo Positions

# Get Turbo Contracts

# Transactions

## List Deposits

## List Withdrawals

## List Referral Payments

## List Bonus Payments

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

