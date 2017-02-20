# Markets

> Request market information for Gold and Apple:

```shell
curl "https://api.whaleclub.co/v1/markets/XAU-USD,AAPL"
  -H "Authorization: Bearer API_TOKEN"
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
  -H "Authorization: Bearer API_TOKEN"
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
turbo | **object** Information about turbo trading, if it's available for this market. The `payoff` object contains the contract duration (in minutes) and the associated payoff.