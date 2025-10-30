# 🪪 EX ID API Documentation

Welcome to the official API documentation for **EX ID**, your partner for identity verification across Latin America.  
This guide provides comprehensive instructions for integrating our identity verification service into your applications.

---

## 1. Getting Started (Authentication)

### 1.1 Authentication

All requests to the EX ID API must be authenticated using your unique API Key, which you can find in your client dashboard.

| Header Name | Value | Required | Description |
| :--- | :--- | :--- | :--- |
| `X-API-KEY` | `[YOUR-SECRET-KEY]` | ✅ | Your unique key for identification, billing, and access control. |

#### Example (cURL)
```bash
curl --location --request GET 'https://api.exid.online/api/v1-verifications' \
--header 'X-API-KEY: YOUR_API_KEY_HERE'
```

### 1.2 Versioning

This documentation refers to **API Version 1 (v1)**.  
Future updates will be communicated via the Changelog.

---

## 2. Core Endpoints

### 2.1 POST /api/create-session  — Start Verification

Initiates a new identity verification session.  
This is the **only endpoint that incurs a cost** when the verification is successfully created.

#### 🔹 Key Feature: Project Tracking (`vendor_data`)

Use the `vendor_data` field to uniquely identify the source of the verification  
(e.g., a specific website, app version, or internal project ID).  
This allows tracking and filtering of transactions in your dashboard.

#### Requirements
- You must have sufficient **Credit Balance** (in USD cents).  
- The `workflow_id` must match an active service plan.

#### Request Body (JSON)

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `workflow_id` | UUID (String) | ✅ | The ID of the specific verification service template you wish to execute. |
| `vendor_data` | String | ✅ | Your control string for tracking projects, sites, or campaigns. |
| `callback` | URL | ✅ | The URL where the EX ID system will send the final result. |

#### Example Request
```json
{
  "workflow_id": "a5481e89-2e44-4a42-9666-be1e486a52d2",
  "vendor_data": "user_1001_onboarding_session",
  "callback": "https://your-client-system.com/exid-results"
}
```

#### Successful Response (201 Created)
Returns the session details, including the redirect URL where the user completes verification.

```json
{
  "session_id": "bae75ba0-81cd-4710-9d96-dc4a24794ee2",
  "url": "https://id.exid.online/session/gi1Os3dTawz9",
  "verification_provider_data": { /* JSON object containing provider data */ }
}
```

---

### 2.2 GET /api/v1-session-details  — Get Full Details and Documents

Used by your dashboard to securely retrieve all verification details in real time  
(including document download links, photos, and analysis data) from the provider.

---

### 2.3 GET /api/v1-verifications  — View History

Retrieves a paginated list of all verification sessions belonging to your company.

---

### 2.4 GET /api/v1-balance  — Consult Balance (for UX)

Quickly fetches the current account balance for display in your client-facing dashboard.

---

## 3. Webhook and Financial Details

### 3.1 Webhook Callback Details (For Your Server)

Upon completion or failure, the final verification result is sent to the callback URL you provided.

#### When Charged
- The fee is **debited only if** the verification result is deemed `Completed`.  
- All other statuses (`Expired`, `Failed`, etc.) incur **no charge**.

#### Webhook Body
The body sent to your callback will be the **complete JSON payload** received from the EX ID system.

---

### 3.2 Available Services (Pricing Reference)

Consult your client dashboard for the official list of active `workflow_id`s and their costs.  
All costs are processed in **USD Cents**.

| Service Name | Workflow ID (UUID) | Cost (USD Cents) |
| :--- | :--- | :--- |
| Verification Padrão (Doc + Selfie) | `a5481e89-2e44-4a42-9666-be1e486a52d2` | 150 |
| New Plan Example | `[NEW_UUID_FROM_DASHBOARD]` | 80 |

---

### 3.3 Error Codes (Handling Failures)

| Code | Status | Description | Action Required |
| :--- | :--- | :--- | :--- |
| **401** | Unauthorized | API Key is invalid or missing. | Check your `X-API-KEY` header. |
| **402** | Payment Required | Insufficient balance to cover the cost of the requested service. | Top up your account balance immediately. |
| **429** | Too Many Requests | Rate limit of 20 requests per minute has been exceeded. | Slow down your request frequency. |
| **500** | Internal Server Error | A failure occurred in the EX ID system. | Contact support with the `session_id`. |

---

## 4. Notes

- **API Base URL:** `https://api.exid.online`  
- **Web Verification URL:** `https://id.exid.online/session/{id}`  
- **Rate Limit:** 20 requests per minute  
- **Response Format:** JSON  

---

## 5. Support

If you experience issues or need assistance:  
**Email:** `support@exid.online`  
Include the **session_id** and **timestamp** of the request.

---

© 2025 **EX ID** — All rights reserved.
