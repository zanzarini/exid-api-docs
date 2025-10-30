# EX ID API Documentation

Welcome to the official API documentation for EX ID, your partner for identity verification across Latin America. This system manages authentication, billing, and integration with verification providers to give you a single, secure gateway.

---

## 1. Getting Started (Authentication)

All requests to the EX ID API must be authenticated using your unique API Key, which you can find in your client dashboard.

### How to Authenticate

Include your API Key in the request header `X-API-KEY`. Requests without this header or with an invalid key will return a `401 Unauthorized` error.

| Header Name | Value | Required | Description |
| :--- | :--- | :--- | :--- |
| `X-API-KEY` | `[YOUR-SECRET-KEY]` | YES | Your unique key for identification, billing, and access control. |

### Example (cURL)

A simple request to test your authentication:

```bash
curl --location --request GET '[https://api.exid.online/api/v1-verifications](https://api.exid.online/api/v1-verifications)' \
--header 'X-API-KEY: YOUR_API_KEY_HERE'
