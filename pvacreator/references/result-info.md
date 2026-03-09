# Result Object (Non-Paginated)

Unified response shape for every non-paginated endpoint.

| Field | Type | Description |
|-------|------|-------------|
| `isSuccess` | boolean | `true` if the request succeeded, `false` otherwise. |
| `data` | object/array/null | Payload returned on success. Type depends on the endpoint. Usually `null` or missing on failure. |
| `message` | string/null | Error description when `isSuccess` is `false`. Typically `null` or empty on success. |

**Success example**
```json
{
  "isSuccess": true,
  "data": {
    "Name": "Gmail_Bypass_QR_Code",
    "DisplayName": "Gmail (Bypass QR)",
    "ModelStatus": "Available"
  },
  "message": null
}
```

**Failure example**
```json
{
  "isSuccess": false,
  "data": null,
  "message": "Platform name cannot be empty"
}
```
