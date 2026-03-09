# CampaignInfo Object

Represents a registration task bundle (campaign).

| Field | Type | Description |
|-------|------|-------------|
| `Name` | string | Unique campaign name. |
| `DisplayName` | string | Display label. Derived campaigns append their source, e.g., `MyCampaign(Gmail_Campaign_001)`. |
| `Platform` | string | Platform code such as `Gmail_Bypass_QR_Code`. |
| `PlatformDisplayName` | string | Localized platform label. |
| `Status` | string | Campaign state: `Stop`, `Pause`, or `Run`. |

**Example**
```json
{
  "Name": "Gmail_Campaign_001",
  "DisplayName": "Gmail_Campaign_001",
  "Platform": "Gmail_Bypass_QR_Code",
  "PlatformDisplayName": "Gmail (Bypass QR)",
  "Status": "Stop"
}
```
