## Cancel Position

> Cancel a pending position:

```shell
curl "https://api.whaleclub.co/v1/position/cancel/d7gAxDSeLtdYtZsEd" \
  -H "Authorization: Bearer API_TOKEN" \
  -X PUT
```
```json
{
  "id": "d7gAxDSeLtdYtZsEd",
  "cancelled": true
}
```

Cancel one or multiple pending positions.

### Request

`PUT https://api.whaleclub.co/v1/position/cancel/:id(s)`

`:id(s)` is a list of one or more comma-separated position IDs.

This endpoint allows you to cancel limit or stop orders that haven't yet executed. Once cancelled, your positions will be deleted and will no longer be accessible.

If the request is successful, the API will return a `200` (Ok) status code.