# PageResult Object (Paginated)

Return format for paginated endpoints. Extends the base `Result` with paging fields.

| Field | Type | Description |
|-------|------|-------------|
| `isSuccess` | boolean | `true` if the request succeeded, `false` otherwise. |
| `data` | array | List of items on the current page. Element type depends on the endpoint. |
| `message` | string/null | Error text when the request fails; usually `null` on success. |
| `page` | integer | Current page index (1-based). |
| `pageCount` | integer | Total number of pages. |

**Success example**
```json
{
  "isSuccess": true,
  "data": [
    {
      "Id": "1634567890123abcde",
      "Username": "testuser",
      "Password": "Pass1234"
    }
  ],
  "message": null,
  "page": 1,
  "pageCount": 5
}
```

**Failure example**
```json
{
  "isSuccess": false,
  "data": null,
  "message": "Campaign does not exist",
  "page": 0,
  "pageCount": 0
}
```
