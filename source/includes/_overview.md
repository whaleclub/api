# Overview

Welcome to Whaleclub's API. You can use the API to programmatically submit new trades, check your balance, fetch trading history, and much more. You can both live trade and demo trade.

The API is organized around [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer). All requests should be made over SSL. All request and response bodies, including errors, are encoded in JSON (`application/json` content type).

Code samples are displayed in the right-side column. All epoch dates are in UTC seconds and amounts in satoshis, unless otherwise indicated.

### BASE ENDPOINT URL

**`https://api.whaleclub.co/v1/`**

### BETA NOTICE

The Whaleclub API, while functional, is in BETA until March 1st, 2017. This means it could go down without notice temporarily during this period. Edge case queries may cause it to not work as expected.

It also means we're looking for your feedback! You have full access to our engineers at [dev-support@whaleclub.co](mailto:dev-support@whaleclub.co) to submit bug reports, feature requests, and any other type of API-related feedback. Be sure to include your Whaleclub username and any supporting material, such as code samples, so we can address your request quickly and effectively.

More general support requests should be directed to [support@whaleclub.co](mailto:support@whaleclub.co).

## Authentication

> Authenticate by passing your API token in an Authorization header:

```shell
curl "https://api.whaleclub.co/v1/balance"
  -H "Authorization: Bearer {API_TOKEN}"
```

**All requests to the Whaleclub API must be authenticated.**

Authentication is straightforward. Pass your API token as a bearer token in an `Authorization` header in every request you make. 

`Authorization: Bearer {API_TOKEN}`

You can get your API token from your [API Settings](https://trade.whaleclub.co/settings/api) page. You'll get one token for live trading and another for demo trading.

<aside class="notice">
Replace <code>{API_TOKEN}</code> with your Whaleclub API token.
</aside>

## Referrals

If you're using the Whaleclub API as part of an app or service you offer to other traders, you can earn referral commissions when they trade.

Pass your Whaleclub Partner ID as a `Partner-ID` header in all the requests you make.

`Partner-ID: {YOUR_PARTNER_ID}`

You can get your Partner ID from your [API Settings](https://trade.whaleclub.co/settings/api) page. Only trades with real funds will result in referral commissions.

<aside class="warning">
Use of auto-referrals or multiple live trading accounts will result in permanent account closure without notice.
</aside>

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

The Whaleclub API returns standard HTTP success and error status codes. In case of an error, we'll include extra information about what went wrong in the JSON-encoded response.

If the request is successful, the API will return either a `200` (OK) or a `201` (Created) status code. 

Code | Description
---------- | -------
400 | Validation Error – unable to validate POST/PUT request
401 | Unauthorized – invalid API token
402 | Failed request
403 | Forbidden
404 | Resource Not Found
422 | Unprocessable Entity – there was an issue parsing your request
423 | Blocked – you've been blocked from making API requests
429 | Rate Limit Exceeded – too many requests
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

> Sample error response – Rate Limit Exceeded (429):

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

Additionally, requests are throttled up to a maximum of **3 requests per second**.

You can check the HTTP headers of any response you get from the Whaleclub API to see your current rate limit status.

Header | Description
---------- | -------
<code>X-RateLimit-Limit</code> | The maximum number of requests allowed per minute.
<code>X-RateLimit-Remaining</code> | The number of requests left in the current rate limit window.
<code>X-RateLimit-Reset</code> | The time at which the current rate limit window resets, in UTC epoch seconds.