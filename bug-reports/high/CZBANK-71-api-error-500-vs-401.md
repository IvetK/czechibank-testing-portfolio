# [CZBANK-71] API Error Handling - Returns 500 Instead of 401

**Priority:** High  
**Severity:** Medium  
**Status:** To Do  
**Reporter:** Iveta Kuklová  
**Date Reported:** August 20, 2025  
**Environment:** Development  

---

## 📋 Summary

When using a deleted or invalid API key, the endpoint `GET /api/v1/transactions` returns `500 Internal Server Error` instead of the proper `401 Unauthorized` response. This affects multiple authenticated endpoints and violates REST API best practices.

---

## 🔍 Description

### Issue Details

While testing API authentication scenarios in Postman, I discovered that when a **deleted** or **incomplete** API key is used to call authenticated endpoints, the system responds with:
- **Actual:** `500 Internal Server Error`
- **Expected:** `401 Unauthorized`

This behavior was confirmed across multiple endpoints including:
- `/api/v1/transactions`
- `/api/v1/transactions/{id}`
- `/api/v1/user/`
- `/api/v1/bank-account/`

### Why This Matters

**500 status codes** indicate server problems (crashes, database errors, unhandled exceptions), while **401 status codes** indicate authentication failures (missing, invalid, or expired credentials).

Using 500 for authentication issues:
- ❌ Misleads developers debugging the API
- ❌ Triggers false alarms in monitoring systems
- ❌ Violates HTTP status code standards
- ❌ Makes it harder to distinguish real server problems from auth failures

**When API key is missing entirely**, the system correctly returns `401 Unauthorized`. The issue only occurs with **deleted** or **invalid** API keys that exist in some form.

---

## 🌐 Environment

**Testing Environment:**
- API Base URL: `[https://[dev-environment]/api/v1/`
- Testing Tool: Postman
- Date: August 20, 2025

**Browser/Tool:** Postman (desktop application)

---

## 📝 Steps to Reproduce

### Prerequisites
1. Have access to Postman
2. Have a valid API key (for baseline testing)
3. Have a deleted API key (create one, then delete it via UI or API)

### Reproduction Steps

**Test 1: Valid API Key (Baseline)**
1. Open Postman
2. Create GET request: `[https://[dev-environment]/api/v1/transactions/`
3. Add header: `x-api-key: [valid-api-key]`
4. Click "Send"
5. **Result:** `200 OK` ✅ (expected)

**Test 2: Missing API Key**
1. Same GET request
2. Remove the `x-api-key` header completely
3. Click "Send"
4. **Result:** `401 Unauthorized` ✅ (expected)

**Test 3: Deleted API Key** ⚠️
1. Same GET request
2. Add header: `x-api-key: [deleted-api-key]`
3. Click "Send"
4. **Result:** `500 Internal Server Error` ❌ (should be 401)

**Test 4: Invalid/Incomplete API Key** ⚠️
1. Same GET request
2. Add header: `x-api-key: invalidapi123` (or any random string)
3. Click "Send"
4. **Result:** `401 Unauthorized` ✅ (expected)

### Key Finding

The error **only occurs with deleted API keys that exist in database but are marked as deleted**, not with completely invalid random strings.

---

## 🎯 Expected Behavior

When authentication fails for any reason (missing, invalid, expired, or deleted API key), the API should return:

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

When a deleted API key is used, the API returns:

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

**Problem:** This response suggests a server problem rather than an authentication failure.

---

## 📊 Affected Endpoints

I tested multiple endpoints and confirmed the same behavior:

| Endpoint | Method | Deleted Key Response | Expected Response |
|----------|--------|---------------------|-------------------|
| `/transactions` | GET | 500 ⚠️ | 401 |
| `/transactions/{id}` | GET | 500 ⚠️ | 401 |
| `/user/` | GET | 500 ⚠️ | 401 |
| `/bank-account/` | GET | 500 ⚠️ | 401 |
| `/apikey` | GET | 500 ⚠️ | 401 |

**Conclusion:** This is a **systematic issue** affecting all authenticated endpoints, not an isolated bug.

---

## 💥 Impact Assessment

### Technical Impact
- **Debugging Difficulty:** Developers waste time investigating "server errors" that are actually auth failures
- **Monitoring Noise:** 500 errors trigger alerts meant for real server problems
- **API Consumer Confusion:** Client applications don't know how to handle the error properly
- **Standard Violation:** Non-compliant with HTTP status code specifications

### Business Impact
- **Low:** Does not affect end users directly
- **Medium:** Affects developer productivity and API usability
- **Development Team:** May waste time investigating false "server errors"

### Frequency
- **Medium:** Occurs every time a deleted API key is used
- More common during testing phases when keys are frequently created/deleted

---

## 🔧 Suggested Fix

### Root Cause Analysis : The likely cause is missing error handling for the "deleted API key" scenario.

### Recommended Solution: Add proper error handling for deleted API keys.

## 📎 Attachments

**Screenshot:** `500-internal-error-instead-of-401-unauthorized-when-deleted-api-key-used.png`
- Shows Postman request with deleted API key
- Displays 500 error response
- Includes timestamp and request headers

*(Note: Screenshot available in portfolio assets folder)*

---

## 💬 Communication History

**Initial Report:**
> "When testing API with Postman, I discovered that using a deleted or incomplete API key causes GET /transactions to return 500 Internal Server Error instead of 401 Unauthorized. This makes debugging harder as 500 suggests server problems rather than authentication issues. I've tested other endpoints and they have the same behavior."

**Developer Response:**
> "This is still valid. I will fix it."

**Follow-up:**
> "I previously saw this discussed in a team recording. Wanted to ensure it's properly documented. Should I close this ticket if it's already being addressed?"

**Developer Clarification:**
> "This is still valid. Please keep the ticket open."

---

## 🔗 Related Issues

- **CZBANK-69:** Session invalidation bug (also related to authentication validation)
- Pattern: Multiple authentication/authorization issues suggest need for comprehensive auth review

---

## ✅ Verification Steps

After the fix is deployed, verify with these tests:

**Test 1: Deleted API Key**
```
GET /api/v1/transactions
Header: x-api-key: [deleted-key]
Expected: 401 Unauthorized ✅
```

**Test 2: Expired API Key**
```
GET /api/v1/transactions
Header: x-api-key: [expired-key]
Expected: 401 Unauthorized ✅
```

**Test 3: Verify Other Endpoints**
- Test at least 3 different endpoints
- All should return 401 for deleted keys
- None should return 500

**Test 4: Monitor Logs**
- Check server logs for proper error messages
- Ensure no unhandled exceptions
- Verify appropriate log levels (auth failures should be INFO/WARN, not ERROR)


---

## 🏷️ Labels

`api` `authentication` `error-handling` `http-status-codes` `postman-testing`

---

*Bug report created: August 20, 2025*  
*Last updated: October 2025*
