# Case Study: API Error Handling Analysis

**Bug ID:** CZBANK-71  
**Severity:** Medium  
**Priority:** High  
**Status:** Confirmed by Developer  
**Discovery Date:** August 2025  

---

## 🎯 Executive Summary

Through systematic testing of authentication scenarios, I discovered that when a deleted or invalid API key is used, the endpoint `GET /api/v1/transactions` returns `500 Internal Server Error` instead of the proper `401 Unauthorized` response. This improper error handling affects multiple endpoints and violates REST API best practices.

**Impact:** Makes debugging harder for API consumers, indicates server instability instead of authentication failure  
**Root Cause:** Missing error handling for deleted/invalid API key scenarios  
**Developer Response:** "This is still valid. I will fix it."

---

## 🔍 How I Discovered the Bug

### The Context

I was conducting systematic API testing with Postman, specifically focusing on **authentication and authorization scenarios**. My testing approach was to cover all possible states of API keys:

- ✅ Valid API key
- ❌ Missing API key (empty)
- ❌ Invalid API key (fake/random string)
- ❌ Expired API key
- ❌ **Deleted API key** ⬅️ This is where I found the bug

### The Discovery Process

#### **Phase 1: Testing Valid Scenarios**

First, I established a baseline with valid authentication:
```
GET /api/v1/transactions
Headers:
  x-api-key: [valid-key]

Response: 200 OK ✅
```

#### **Phase 2: Testing Missing API Key**
```
GET /api/v1/transactions
Headers:
  (no x-api-key header)

Response: 401 Unauthorized ✅
Message: "Unauthorized"
```

**Result:** Works as expected.

#### **Phase 3: Testing Invalid API Key**
```
GET /api/v1/transactions
Headers:
  x-api-key: invalidapi123

Response: 401 Unauthorized ✅
```

**Result:** Works as expected.

#### **Phase 4: Testing Deleted API Key** 🚨

This is where I found the problem. I had previously created an API key and then deleted it through the UI. I saved that deleted key to test what happens if someone tries to use it:
```
GET /api/v1/transactions
Headers:
  x-api-key: yaPSbxAGzUSbXXZeMpPqrJYIPhiwEgIrPzCbvVpUhJYvouPHIGhXngsrvEFpNtLY

Response: 500 Internal Server Error ⚠️
```

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

**Expected:** `401 Unauthorized` (authentication failure)  
**Actual:** `500 Internal Server Error` (server problem)

🚨 **This is wrong!** 500 errors should indicate server issues, not authentication problems.

---

## 🛠️ Technical Investigation

### Understanding HTTP Status Codes

To understand why this is a problem, let's review what these status codes mean:

| Status Code | Meaning | When to Use |
|-------------|---------|-------------|
| **401 Unauthorized** | Authentication failed | Missing, invalid, expired, or revoked credentials |
| **500 Internal Server Error** | Server encountered unexpected condition | Database errors, unhandled exceptions, server crashes |

### What's Happening Under the Hood

Based on the error response, I hypothesize this is what's happening in the backend:
```javascript
// Simplified pseudo-code

async function validateApiKey(key) {
  const apiKey = await database.findApiKey(key);
  
  if (!apiKey) {
    // Key doesn't exist in database
    return 401; // ✅ Correctly returns 401
  }
  
  if (apiKey.isDeleted) {
    // Key exists but is marked as deleted
    // ⚠️ Code doesn't handle this scenario!
    // Probably throws an error that isn't caught
    throw new Error("API key is deleted"); // ❌ Becomes 500
  }
  
  return 200; // Key is valid
}
```

**The problem:** When an API key is marked as deleted in the database, the code doesn't handle this case gracefully, causing an unhandled exception that bubbles up as a 500 error.

---

## 📊 Systematic Testing Results

I tested this scenario across **multiple endpoints** to see if it's a widespread issue:

| Endpoint | Method | Deleted API Key Response | Expected | Status |
|----------|--------|--------------------------|----------|--------|
| `/transactions` | GET | 500 ⚠️ | 401 | Bug |
| `/transactions/{id}` | GET | 500 ⚠️ | 401 | Bug |
| `/user/` | GET | 500 ⚠️ | 401 | Bug |
| `/bank-account/` | GET | 500 ⚠️ | 401 | Bug |
| `/apikey` | GET | 500 ⚠️ | 401 | Bug |

**Finding:** This is a **systematic issue** affecting all authenticated endpoints, not just transactions.

---

## 🎯 Why This Matters

### 1. **Developer Experience**

When API consumers (frontend developers, mobile app developers) see a 500 error, they think:
- "Is the server down?"
- "Is there a bug in the backend?"
- "Should I retry the request?"

When they should be thinking:
- "My API key is invalid"
- "I need to get a new API key"
- "This is an authentication issue"

### 2. **Debugging Complexity**

**Current situation (with bug):**
```
Frontend logs: "API Error 500 - Server error on GET /transactions"
Backend logs: "Unhandled exception: API key is deleted"
Developer thinks: "There's a server problem"
```

**Correct behavior (after fix):**
```
Frontend logs: "API Error 401 - Unauthorized on GET /transactions"
Backend logs: "Authentication failed: deleted API key used"
Developer thinks: "This is an auth issue, not a server problem"
```

### 3. **Monitoring & Alerts**

Many systems have monitoring that alerts on 500 errors because they indicate serious problems:
- Every use of a deleted API key triggers a false alarm
- Real 500 errors might be hidden in the noise
- Operations teams waste time investigating non-issues

### 4. **REST API Standards**

This violates HTTP status code conventions. According to REST standards:
- **4xx errors** = Client problems (bad request, unauthorized, forbidden, not found)
- **5xx errors** = Server problems (internal error, not implemented, service unavailable)

Using 500 for authentication failures is technically incorrect.

---

## 📋 Reproduction Steps

**Environment:**
- API Base URL: `https://develop.czechibank.ostrava.digital/api/v1/`
- Tool: Postman
- Endpoint: `/transactions`

**Prerequisites:**
1. Create a user account and generate an API key
2. Delete that API key through the UI or DELETE endpoint
3. Save the deleted API key value for testing

**Steps:**

1. Open Postman
2. Create a new GET request
3. URL: `https://develop.czechibank.ostrava.digital/api/v1/transactions/`
4. In Headers tab, add:
   - Key: `x-api-key`
   - Value: `[your-deleted-api-key]`
5. Click "Send"
6. **Actual Result:** Response status `500 Internal Server Error`
7. **Expected Result:** Response status `401 Unauthorized`

**Note:** Same behavior occurs with:
- Partially deleted API key (incomplete string)
- API key that was deleted and has been removed from database

---

## 🔧 Recommended Fix

### Backend Solution

**Add proper error handling for deleted/invalid API keys:**
```javascript
// Current code (causing the bug)
async function validateApiKey(key) {
  const apiKey = await database.findApiKey(key);
  
  if (!apiKey || apiKey.isDeleted) {
    // ❌ Throws unhandled exception
    throw new Error("Invalid API key");
  }
  
  return apiKey;
}

// Fixed code
async function validateApiKey(key) {
  try {
    const apiKey = await database.findApiKey(key);
    
    if (!apiKey) {
      // Key doesn't exist
      return { valid: false, reason: "API key not found" };
    }
    
    if (apiKey.isDeleted) {
      // Key was deleted
      return { valid: false, reason: "API key has been deleted" };
    }
    
    if (apiKey.expiresAt && apiKey.expiresAt < Date.now()) {
      // Key has expired
      return { valid: false, reason: "API key has expired" };
    }
    
    return { valid: true, apiKey };
    
  } catch (error) {
    // Log the actual error for debugging
    console.error("Error validating API key:", error);
    // But still return auth failure to client
    return { valid: false, reason: "Authentication failed" };
  }
}

// In the route handler
app.get('/transactions', async (req, res) => {
  const result = await validateApiKey(req.headers['x-api-key']);
  
  if (!result.valid) {
    return res.status(401).json({
      success: false,
      message: "Unauthorized",
      error: {
        code: "401",
        message: result.reason
      }
    });
  }
  
  // Continue with the request...
});
```

### Improved Error Messages

Instead of generic "Internal server error", return informative auth errors:
```json
{
  "success": false,
  "message": "Unauthorized",
  "error": {
    "code": "401",
    "message": "API key has been deleted. Please generate a new API key."
  },
  "meta": {
    "timestamp": "2025-08-20T11:11:53.532Z"
  }
}
```

---

## 📚 Lessons Learned

### What This Bug Taught Me

1. **Test All Authentication States:** Don't just test valid/invalid - test expired, deleted, revoked, and edge cases

2. **HTTP Status Codes Matter:** Using the wrong status code affects debugging, monitoring, and developer experience

3. **Systematic Testing Reveals Patterns:** By testing multiple endpoints, I discovered this was a widespread issue, not an isolated bug

4. **Error Handling is Critical:** Proper error handling should catch edge cases and return appropriate status codes

5. **Think About Developer Experience:** API consumers need clear, accurate error messages to debug issues

### Testing Techniques Applied

- ✅ **Equivalence Partitioning:** Grouped API key states into classes (valid, missing, invalid, expired, deleted)
- ✅ **Systematic Testing:** Tested the same scenario across multiple endpoints
- ✅ **Negative Testing:** Focused on error scenarios, not just happy paths
- ✅ **API Best Practices Knowledge:** Recognized violation of REST standards

---

## 🔗 Related Issues

This bug is similar to other authentication/authorization issues found:

- **[Session Invalidation Bug](01-session-invalidation-bug.md)** - Also related to missing validation
- **[CZBANK-57: API Key Security Pattern](03-security-apikey-pattern.md)** - Another API key-related finding

All three issues point to the need for a comprehensive **authentication/authorization review** across the application.

---

## 📊 Testing Metrics

**Time to Discovery:** 20 minutes of systematic auth testing  
**Time to Investigation:** 30 minutes (testing multiple endpoints, analyzing patterns)  
**Time to Documentation:** 30 minutes  
**Developer Response Time:** < 48 hours  
**Priority Assigned:** High  
**Status:** Approved for Fix  

---

## 💬 Developer Communication

**My Initial Report:**

> "When testing API with Postman, I discovered that using a deleted or incomplete API key causes `GET /transactions` to return 500 Internal Server Error instead of 401 Unauthorized. This makes debugging harder as 500 suggests server problems rather than authentication issues. I've tested other endpoints and they have the same behavior."

**Developer Response:**

> "This is still valid. I will fix it."

**My Follow-up:**

> "I previously sent a recording from a team call where this issue was discussed. Wanted to make sure it's documented properly. Should I close this ticket if it's already being addressed?"

**Developer Clarification:**

> "This is still valid. Please keep the ticket open."

---

## 🎓 Key Takeaway

This case study demonstrates:
- ✅ Systematic approach to API testing
- ✅ Understanding of HTTP status codes and REST principles
- ✅ Ability to identify patterns across multiple endpoints
- ✅ Clear communication of technical issues
- ✅ Consideration of developer experience and debugging workflow

**The most important lesson:** Good API testing isn't just about making requests - it's about understanding what the responses should be, recognizing when standards are violated, and thinking about how errors affect the people who use the API.

---

*Last updated: October 2025*
