PVACreator Skill for OpenClaw AI
An OpenClaw-compatible AI skill that enables autonomous Gmail account registration and campaign management via the PVACreator local API.
Overview
This skill allows an AI agent to interface with a locally running instance of PVACreator (Windows). It automates the entire lifecycle of account creation, from configuring proxies and browser fingerprints to handling SMS-based multi-step verification.
Key Capabilities:
Platform: Support Gmail, Facebook, Instagram, TikTok Outlook, Twitter, etc.）
Campaign Management: Programmatically create, start, stop, and delete registration campaigns.
Account Automation: Add Gmail accounts to specific campaigns with granular control over profile data (Name, DOB, Gender).
Verification Integration: Supports multiple SMS providers (smspva, 5sim, smsactivate, etc.) via API keys.
Status Monitoring: Paginated tracking of registration progress and success/failure logs.

Prerequisites
PVACreator Windows App: Must be installed and running on the host machine.
API Access: Ensure the Windows application allows external HTTP requests (check firewall settings).
Port: The service defaults to port 52636. 

Connection Settings
Base URL:

Direct Windows: http://localhost:52636/api

Docker/Container: http://host.docker.internal:52636/api

Remote: Set the REGISTER_SERVICE_URL environment variable.


 API Data StructuresAll endpoints return a unified JSON response format.
 
 Non-Paginated Result
 Used for single actions like creating a campaign.
 JSON
 {
  "isSuccess": true,
  "data": { "Name": "Gmail_Campaign_001", "Status": "Stop" },
  "message": null
}

Paginated PageResult
Used for listing accounts within a campaign.
JSON
{
  "isSuccess": true,
  "data": [ ... ],
  "page": 1,
  "pageCount": 5
}
Gmail Account Configuration
When adding accounts, the following fields are required or highly recommended:
## 📧 Gmail Account Configuration
When adding accounts, the following fields are required or highly recommended:

| Field | Description |
| :--- | :--- |
| **Username** | Desired Gmail address. |
| **PhoneService** | SMS provider code: `1` (smspva), `2` (5sim), `3` (smsactivate), etc. |
| **PhoneApiKey** | API key for the chosen SMS provider. |
| **Proxy** | Proxy string format: `host:port:username:password`. |
| **BirthYear** | Birth year (e.g., 1990). |
| **Gender** | 1 = male, 2 = female. |

> **Note:** For a full list of `PhoneCountry` codes for each provider, refer to the `gmail-info.md` file in the references folder.

Registration Workflow
To use this skill effectively, the AI follows these steps:

Check Platform: Confirm the platform is Available.

Collect Info: Prompt the user for required credentials (SMS keys, proxies, etc.).

Create Campaign: Spin up a fresh campaign for the task.

Add & Start: Add the account data and trigger the campaign.

Monitor: Poll the account list every 30 seconds to track the AccountStatus (e.g., Registing, Success, or Fail).

Troubleshooting
If the skill cannot reach the API, verify:

Is PVACreator running?

Are you using the correct Base URL for your environment?

If using Docker, can the container resolve host.docker.internal?
