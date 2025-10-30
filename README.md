# EX ID API Documentation (v1.0)

Welcome to the official API documentation for EX ID, your partner for identity verification across Latin America.

---

## 1. Getting Started (Authentication & Versioning)

### 1.1 Authentication

All requests to the EX ID API must be authenticated using your unique API Key, provided in your client dashboard.

| Header Name | Value | Required | Description |
| :--- | :--- | :--- | :--- |
| `X-API-KEY` | `[YOUR-SECRET-KEY]` | YES | Your unique key for identification, billing, and access control. |

### 1.2 Versioning

This documentation refers to **API Version 1 (v1)**. All endpoints start with `/api/v1-`. Future updates will be communicated at least 60 days in advance.

---

## 2. Core Endpoints

### 2.1 POST /api/didit-create-session (Start Verification)

Use this endpoint to initiate a new identity verification session. This is the **only endpoint that incurs a cost** when the verification is successful.

#### Key Feature: Project Tracking (`vendor_data`)

The `vendor_data` field should be used to uniquely identify the source of the verification (e.g., specific website, mobile app version, or internal project ID). This allows you to easily track and filter transactions in your dashboard.

#### Requirements

* You must have sufficient **Credit Balance** (in USD Cents).
* The `workflow_id` must match an active plan.

#### Request Body (JSON)

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `workflow_id` | UUID (String) | YES | The specific verification template ID matching your desired plan (see Workflows below). |
| `vendor_data` | String | YES | **Your control string for tracking projects, sites, or campaigns.** |
| `callback` | URL | YES | The URL where our system will send the final webhook result when the verification is complete. |

### 2.2 GET /api/v1-session-details (Get Full Details and Documents)

This endpoint is used by your dashboard to securely retrieve **all verification details in real-time** (including document download links, photos, and analysis data).

### 2.3 GET /api/v1-verifications (View History)

Use this endpoint to retrieve a paginated list of all verification sessions belonging to your company.

### 2.4 GET /api/v1-balance (Consult Balance for UX)

Use this endpoint to quickly fetch the current account balance for display in your client-facing dashboard.

---

## 3. Webhook and Financial Details

### 3.1 Webhook Callback Details (For Your Server)

Upon completion or failure, the final verification result is sent to the `callback` URL you provided.

**Good Practice for Webhook Handling:**
1. **Respond Immediately:** Your server should respond with a `200 OK` as fast as possible to prevent timeouts.
2. **Asynchronous Processing:** Do not process the data inside the webhook receiver. Instead, save the payload immediately and process it in a background job.

### 3.2 Billing Policy

* **Cost Basis:** All transactions are processed in **USD Cents**.
* **When Charged:** The fee is debited **only if** the verification result is deemed **`Completed`**. All other statuses (`Expired`, `Failed`, etc.) incur no charge.

### 3.3 Available Workflows (Pricing Reference)

| Workflow Name | Workflow ID (UUID) | Cost (USD Cents) |
| :--- | :--- | :--- |
| Verification Padrao (Doc + Selfie) | `a5481e89-2e44-4a42-9666-be1e486a52d2` | 150 |
| New Plan Example | `[NEW_UUID_FROM_DASHBOARD]` | 80 |

### 3.4 Error Codes (Handling Failures)

| Code | Status | Description | Action Required |
| :--- | :--- | :--- | :--- |
| `401` | Unauthorized | API Key is invalid or missing. | Check your `X-API-KEY` header. |
| `402` | Payment Required | Insufficient balance to cover the cost of the requested workflow. | Top up your account balance immediately. |
| `429` | Too Many Requests | Rate limit of 20 requests per minute has been exceeded. | Slow down your request frequency. |
| `500` | Internal Server Error | A failure occurred in the EX ID system or the verification provider. | Contact support with the session ID. |
