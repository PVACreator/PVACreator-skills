---
name: pvacreator-en
description: Automate Gmail account registrations with PVACreator's local API, including proxy/browser fingerprints and multi-step verification (SMS, phone, email, etc.).
homepage: https://pvacreator.com/
---

# PVACreator Skill (English)

## 1. Overview
This skill lets the agent talk to a locally running PVACreator API (Windows executable). With it you can:
- List currently supported platforms and their statuses.
- Create, inspect, start, stop, and delete registration campaigns.
- Add Gmail accounts to a campaign, list them with pagination, or delete them.

The service exposes an HTTP API. If the Windows app is not running, nothing will work.

Before registering any account, read Section 6 “Registration Workflow Guide” and walk the user through each required field.

## 2. Dependency Check & Service Startup
PVACreator lives in `PVACreator.exe`. **Always confirm the program is already running locally before calling any API.**

### How to check whether the service is up?
- Hit `GET /platform/list` on the base URL.
- If the request times out or is refused, tell the user:
  > “Cannot reach the registration service. Please confirm:
  > 1. PVACreator (Windows) is installed and running.
  > 2. The program allows external access (firewall rules, etc.).
  > 3. If this AI runs inside Docker, the container can reach the host port and should use `host.docker.internal`.”

### How to start the service?
- Double-click the EXE or launch it via CLI. Once running, it listens on port `52636`.

## 3. Base URL Selection
The API base URL depends on where the agent runs:

| Environment | Recommended Base URL |
|-------------|---------------------|
| AI running **directly on Windows** | `http://localhost:52636/api` |
| AI running in **Docker**, service on the host | `http://host.docker.internal:52636/api` |
| Remote deployment | Ask the user for the host/IP |

**URL resolution flow:**
1. If env var `REGISTER_SERVICE_URL` exists, use it.
2. Else try `http://localhost:52636/api` (OPTIONS or `/platform/list`).
3. If that fails, try `http://host.docker.internal:52636/api`.
4. If everything fails, surface the dependency warning above.

## 4. Response Formats
All endpoints return JSON.

### 4.1 Result (non-paginated)
```json
{
  "isSuccess": true,
  "data": { ... },
  "message": null
}
```
See [result-info.md](references/result-info.md) for details.

### 4.2 PageResult (paginated)
```json
{
  "isSuccess": true,
  "data": [ ... ],
  "message": null,
  "page": 1,
  "pageCount": 5
}
```
See [pageresult-info.md](references/pageresult-info.md).

## 5. API Operations
All endpoints live under the chosen base URL and return `application/json`.

### 5.1 Platform APIs
#### List platforms
- **Endpoint**: `GET /platform/list`
- **Query params**: `onlyIncludeAvailable` (optional bool)
- **Response**: `Result<PlatformInfo[]>` (see [platform-info.md](references/platform-info.md)).

### 5.2 Campaign APIs
- `GET /campaign/list` → `Result<CampaignInfo[]>`
- `POST /campaign/create` → body (`application/x-www-form-urlencoded`): `platform`, `name`
- `GET /campaign/details?name={campaignName}`
- `POST /campaign/start` → body: `name={campaignName}`
- `POST /campaign/stop` → body: `name={campaignName}`
- `DELETE /campaign/remove` → body: `name={campaignName}`
(Details in [campaign-info.md](references/campaign-info.md)).

### 5.3 Account APIs (Gmail only)
#### Paginated list
- `GET /account/list?campaignName=...&page=...`
- Returns `PageResult<GmailInfo[]>`.

#### Add account
- `POST /account/add`
- Body (`application/x-www-form-urlencoded`):
  - `campaignName`
  - `account` = JSON payload conforming to [gmail-info.md](references/gmail-info.md).
- Returns `Result<GmailInfo>`.

Example JSON:
```json
{
  "Username": "testuser",
  "Password": "Pass1234",
  "FirstName": "John",
  "LastName": "Doe",
  "BirthMonth": "1",
  "BirthDay": "1",
  "BirthYear": "1990",
  "Gender": "male"
}
```

#### Remove account
- `DELETE /account/remove`
- Body: `campaignName`, `accountId`

## 6. Registration Workflow Guide
1. Confirm the platform is available.
2. Read the platform-specific account info doc (different flows may require SMS, email, proxy, etc.).
3. Collect every required field from the user. For each field the user must provide, explain what it means and guide them through filling it out.
4. Create a new campaign (always spin up a fresh one unless the user insists otherwise).
5. Add the account and note the returned account ID.
6. Start the campaign.
7. Poll the account list (PageResult) and monitor by account ID until it finishes.
8. Report the outcome (record success, analyze failures).

If the user wants continuous monitoring, poll roughly every 30 seconds. Otherwise remind them they can request a status check at any time.

**Important:** Whenever the workflow needs user input, show the field description and walk them through the setting.

## 7. Notes
- **Platform support:** Only `Gmail_Bypass_QR_Code`, `Outlook`, and `Twitter` are defined, but account APIs currently work for Gmail only.
- **Pagination:** Page size is server-controlled; rely on `pageCount` to know when you’re done.
- **Campaign state:** `start` kicks off all accounts inside the campaign; `stop` pauses but does not revert finished accounts.
- **Account state:** Registration runs in the background; keep checking via pagination.
- **Errors:** Every API uses the unified Result format—bubble up the message to the user.
- **Third-party services:** SMS/phone/email providers need valid credentials and API keys. Expect failures like bad OTPs or phone issues—handle them via the returned messages.

## 8. References
- [Result data structure](references/result-info.md)
- [PageResult data structure](references/pageresult-info.md)
- [Platform data structure](references/platform-info.md)
- [Campaign data structure](references/campaign-info.md)
- [Gmail account data structure](references/gmail-info.md)

## Platform Reference Matrix

| Platform | Doc |
|----------|-----|
| Gmail_Bypass_QR_Code | [gmail-info.md](references/gmail-info.md) |
