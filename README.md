
# EX ID API Documentation

Welcome to the official API documentation for EX ID, your partner for identity verification across Latin America. This system manages authentication, billing, and integration with verification providers to give you a single, secure gateway.

---## 1. Getting Started (Authentication)

All requests to the EX ID API must be authenticated using your unique API Key, which you can find in your client dashboard.### How to Authenticate

Include your API Key in the request header `X-API-KEY`. Requests without this header or with an invalid key will return a `401 Unauthorized` error.

| Header Name | Value | Required | Description |
| :--- | :--- | :--- | :--- |
| `X-API-KEY` | `[YOUR-SECRET-KEY]` | YES | Your unique key for identification, billing, and access control. |### Example (cURL)

A simple request to test your authentication:```bash
curl --location --request GET '[https://api.exid.online/api/v1-verifications](https://api.exid.online/api/v1-verifications)' \
--header 'X-API-KEY: YOUR_API_KEY_HERE'
2. Core Endpoints
2.1 POST /api/didit-create-session (Start Verification)
Use this endpoint to initiate a new identity verification session. This is the only endpoint that incurs a cost when the verification is successful.
Requirements
You must have sufficient Credit Balance (in USD Cents).
The workflow_id must match an active plan.
Request Body (JSON)
ParameterTypeRequiredDescriptionworkflow_idUUID (String)YESThe specific verification template ID matching your desired plan (see Workflows below).vendor_dataStringYESYour internal reference ID for this user (e.g., your user ID in your database).callbackURLYESThe URL where our system will send the final webhook result when the verification is complete.
Exportar a Hojas de cálculo
Example Request
JSON

{
  "workflow_id": "a5481e89-2e44-4a42-9666-be1e486a52d2",
  "vendor_data": "user_1001_onboarding_session",
  "callback": "[https://your-client-system.com/exid-results](https://your-client-system.com/exid-results)"
}
Successful Response (201 Created)
Returns the session details, including the URL where the user must be redirected to complete the verification process.
JSON

{
  "session_id": "bae75ba0-81cd-4710-9d96-dc4a24794ee2",
  "url": "[https://id.exid.online/session/gi1Os3dTawz9](https://id.exid.online/session/gi1Os3dTawz9)",
  // ... verification provider's data
}
2.2 GET /api/v1-session-details (Get Full Details and Documents)
This endpoint is used by your dashboard to securely retrieve all verification details in real-time (including document download links, photos, and analysis data).
Query Parameters
ParameterTypeRequiredDescriptionsession_idUUID (String)YESThe ID of the session you want to retrieve.
Exportar a Hojas de cálculo
Successful Response (200 OK)
Returns the complete JSON payload from the verification provider, which you can use to display detailed results and offer document downloads in your own dashboard.
2.3 GET /api/v1-verifications (View History)
Use this endpoint to retrieve a paginated list of all verification sessions belonging to your company.
3. Reference and Error Codes
3.1 Available Workflows (Pricing)
Consult your client dashboard for the official list of currently active workflow_ids and their costs.
Workflow NameWorkflow ID (UUID)Cost (USD Cents)Verification Padrao (Doc + Selfie)a5481e89-2e44-4a42-9666-be1e486a52d2150New Plan Example[NEW_UUID_FROM_DASHBOARD]80
Exportar a Hojas de cálculo
3.2 Error Codes (Handling Failures)
CodeStatusDescriptionAction Required401UnauthorizedAPI Key is invalid or missing.Check your X-API-KEY header.402Payment RequiredInsufficient balance to cover the cost of the requested workflow.Top up your account balance immediately.429Too Many RequestsRate limit of 20 requests per minute has been exceeded.Slow down your request frequency.500Internal Server ErrorA failure occurred in the EX ID system or the verification provider.Contact support with the session ID.
Exportar a Hojas de cálculo
