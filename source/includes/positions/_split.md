## Split Position

> Split an active 100BTC EUR/USD position into 2 smaller positions of 60BTC and 40BTC:

```shell
curl "https://api.whaleclub.co/v1/position/split/22bCNkWhiwxF7qAMs" \
  -H "Authorization: Bearer API_TOKEN" \
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
    "created_at": 1486327152,
    "currency": "BTC"
  },
  {
    "id": "vM7i73QipKc6F7SFs",
    "parent_id": "22bCNkWhiwxF7qAMs",
    "slug": "fDVhecYF",
    "size": 4000000000,
    "created_at": 1486327152,
    "currency": "BTC"
  }
```

Split an existing pending or active position.

### Request

`POST https://api.whaleclub.co/v1/position/split/:id` 

This endpoint allows you to split an existing position according to a ratio you provide. It can only be called on a pending or active position.

If the request is successful, the API will return a `200` (Ok) status code. 

Param | Description
---------- | -------
ratio | **integer** Required. In percent. Must be between 5 and 95.

### Response

Returns an array of **[Position](#position-object)** objects containing two smaller positions resulting from the split.