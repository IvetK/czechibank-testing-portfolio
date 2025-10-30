# [CZBANK-71] API Error Handling - Returns 500 Instead of 401

**Priority:** High  
**Severity:** Medium  
**Status:** To Do  
**Reporter:** Iveta Kuklová  
**Date Reported:** August 20, 2025

---

## 📋 Summary

When using a deleted API key, the endpoint `GET /api/v1/transactions` returns `500 Internal Server Error` instead of `401 Unauthorized`. This behavior affects multiple authenticated endpoints and violates REST API best practices for HTTP status codes.

---

## 🌐 Environment

- **API Base URL:** `https://[dev-environment]/api/v1/`
- **Testing Tool:** Postman
- **Date:** August 20, 2025

---

## 🔍 Description

While testing API authentication scenarios in Postman, I discovered that when a **deleted API key** is used to call authenticated endpoints, the system responds with `500 Internal Server Error` instead of `401 Unauthorized`.

**Key Finding:** The error only occurs with deleted API keys that exist in the database but are marked as deleted. Completely invalid/random API keys correctly return `401`.

**Why This Is Incorrect:**
- 500 status codes indicate server problems (crashes, unhandled exceptions)
- 401 status codes indicate authentication failures (invalid credentials)
- This misleads developers and triggers false alarms in monitoring systems

---

## 📝 Steps to Reproduce

### Prerequisites
- Access to Postman
- Valid API key for baseline testing
- Deleted API key (create one, then delete it via UI or API)

### Steps

**Test 1: Valid API Key (Baseline)**
1. Create GET request: `GET https://[dev-environment]/api/v1/transactions`
2. Add header: `x-api-key: [valid-api-key]`
3. Send request
4. **Result:** `200 OK` ✅

**Test 2: Missing API Key**
1. Same GET request
2. Remove `x-api-key` header completely
3. Send request
4. **Result:** `401 Unauthorized` ✅

**Test 3: Deleted API Key** ⚠️
1. Same GET request
2. Add header: `x-api-key: [deleted-api-key]`
3. Send request
4. **Result:** `500 Internal Server Error` ❌ (should be 401)

**Test 4: Invalid/Random API Key**
1. Same GET request
2. Add header: `x-api-key: invalidapi123`
3. Send request
4. **Result:** `401 Unauthorized` ✅

---

## 🎯 Expected Behavior

When authentication fails (missing, invalid, expired, or deleted API key), the API should return:

**Status Code:** `401 Unauthorized`

**Response Body:**
```json
{
  "success": false,
  "message": "Unauthorized",
  "error": {
    "code": "401",
    "message": "Invalid or expired API key"
  },
  "meta": {
    "timestamp": "2025-08-20T11:11:53.532Z"
  }
}
```

---

## 🐛 Actual Behavior

When a deleted API key is used:

**Status Code:** `500 Internal Server Error`

**Response Body:**
```json
{
  "success": false,
  "message": "Internal server error",
  "error": {
    "code": "500",
    "message": "Internal server error"
  },
  "meta": {
    "timestamp": "2025-08-20T11:11:53.532Z"
  }
}
```

---

## 📊 Affected Endpoints

Tested multiple endpoints - **systematic issue across all authenticated endpoints:**

| Endpoint | Method | Deleted Key Response | Expected |
|----------|--------|---------------------|----------|
| `/transactions` | GET | 500 ⚠️ | 401 |
| `/transactions/{id}` | GET | 500 ⚠️ | 401 |
| `/user/` | GET | 500 ⚠️ | 401 |
| `/bank-account/` | GET | 500 ⚠️ | 401 |
| `/apikey` | GET | 500 ⚠️ | 401 |

---

## 💥 Impact

- **Developer Experience:** Misleading error codes make debugging harder
- **Monitoring Systems:** False alarms for "server errors" that are actually auth failures
- **API Standards Compliance:** Violates HTTP status code specifications
- **Client Applications:** Cannot properly distinguish between auth failures and server problems

---

## 🔧 Suggested Fix

**Add proper error handling for deleted API keys.**
**Note:** Avoid exposing whether key is "deleted" vs "invalid" in error message (security best practice).

---

## 🔗 Related Issues

**CZBANK-69:** Session not invalidated after logout (also authentication validation issue)

**Pattern:** Multiple authentication/authorization issues suggest need for comprehensive auth review across all endpoints.

---

## 📎 Additional Notes

**Discovery Method:** Systematic API authentication testing in Postman with different key states (valid, missing, deleted, invalid)

**Screenshot:** `500-internal-error-instead-of-401-unauthorized-when-deleted-api-key-used.png` (available in portfolio assets)

---

*Bug reported: August 20, 2025*  
*Last updated: October 2025*
