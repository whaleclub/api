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

This endpoint allows you to cancel a limit or stop order that hasn't yet executed. Once cancelled, your position will be deleted and no longer accessible.

If the request is successful, the API will return a `200` (Ok) status code.