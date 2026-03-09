# PlatformInfo Object

Describes a registration platform available inside PVACreator.

| Field | Type | Description |
|-------|------|-------------|
| `Name` | string | Platform code (e.g., `Gmail_Bypass_QR_Code`) used in API calls. |
| `DisplayName` | string | Localized label suitable for UI. |
| `Status` | string | Platform status: `Available`, `Unvailable`, `Developing`, `Disable`. |

**Example**
```json
{
  "Name": "Gmail_Bypass_QR_Code",
  "DisplayName": "Gmail (Bypass QR)",
  "Status": "Available"
}
```
