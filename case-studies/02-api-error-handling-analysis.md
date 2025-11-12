# Case Study: API Error Handling - 500 vs 401

**Bug ID:** CZBANK-71 | **Severity:** Medium | **Priority:** High

## Executive Summary

During systematic API authentication testing in Postman, I discovered that deleted API keys cause endpoints to return `500 Internal Server Error` instead of `401 Unauthorized`. This violates REST API best practices and misleads developers about the nature of the problem.

**Impact:** Makes debugging harder, triggers false monitoring alarms, violates HTTP standards  
**Root Cause:** Missing error handling for deleted API key state  
**Scope:** Systematic issue affecting all authenticated endpoints

---

## Discovery Process

**Testing Approach:** Systematic authentication testing using equivalence partitioning - testing different API key states to ensure proper error handling.

**API Key States Tested:**
- ✅ Valid key → 200 OK
- ✅ Missing key → 401 Unauthorized  
- ✅ Invalid/random key → 401 Unauthorized
- ❌ **Deleted key → 500 Internal Server Error** ⚠️

**The Bug:**
```
GET /api/v1/transactions
Headers: x-api-key: [deleted-key]

Expected: 401 Unauthorized
Actual: 500 Internal Server Error ❌
```

---

## Technical Investigation

**Why This Is Wrong:**

HTTP status codes have specific meanings:
- **401** = Authentication failure (client's problem)
- **500** = Server error (server's problem)

When a deleted API key causes a 500 error, it tells developers:
- ❌ "The server crashed" (wrong message)
- ✅ Should say: "Your API key is invalid" (correct message)

**Systematic Testing:**

I tested this scenario across multiple endpoints to determine scope:

| Endpoint | Deleted Key Response | Expected |
|----------|---------------------|----------|
| /transactions | 500 ⚠️ | 401 |
| /transactions/{id} | 500 ⚠️ | 401 |
| /user/ | 500 ⚠️ | 401 |
| /bank-account/ | 500 ⚠️ | 401 |
| /apikey | 500 ⚠️ | 401 |

**Finding:** This isn't an isolated bug - it's a systematic issue affecting all authenticated endpoints, indicating a gap in the authentication middleware.

---

## Real-World Impact

**For API Consumers:**
- False alarms in monitoring (500s trigger "server down" alerts)
- Wasted debugging time (investigating "server problems" instead of "auth issues")
- Confusing error messages (generic "Internal server error" vs clear "Invalid API key")

**For Operations:**
- Every deleted key usage creates a false 500 error in logs
- Real server problems get lost in the noise

---

## Key Insights

- **Systematic testing reveals patterns** - Testing one endpoint would miss that this affects the entire API
- **Equivalence partitioning uncovers edge cases** - Deleted keys are different from invalid keys - they exist in the database but are marked as deleted
- **Understanding HTTP standards matters** - Recognizing that 500 is wrong requires knowing what status codes mean
- **Think about the developer experience** - Good error handling helps API consumers debug faster
- **Pattern recognition** - This, combined with CZBANK-69, suggests broader authentication validation gaps

---
## 🔧 Recommended Fix

Backend Solution: Add proper error handling for deleted/invalid API keys

Improved Error Messages: Instead of generic "Internal server error", return informative auth error i.e."API key has been deleted. Please generate a new API key."

---
**Related:** [Full Bug Report CZBANK-71](../bug-reports/high/CZBANK-71_API_Error_Handling.md)

---

*Last updated: October 2025*
