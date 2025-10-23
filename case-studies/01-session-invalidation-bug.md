# Case Study: Session Invalidation Bug

**Bug ID:** CZBANK-69  
**Severity:** High  
**Priority:** Critical  
**Status:** Confirmed by Developer  
**Discovery Date:** August 2025  

---

## 🎯 Executive Summary

Discovered a critical security vulnerability where users could perform financial transactions after logout by using the browser back button. The session cookie was removed from the browser, but the API endpoint `POST /transactions` did not validate the session properly, allowing unauthorized transaction creation.

**Impact:** Enables unauthorized financial operations, violates authentication best practices  
**Root Cause:** Missing server-side session validation on transaction endpoint  
**Developer Response:** "Bug approved. This is critical issue we need to fix."

---

## 🔍 How I Discovered the Bug

### The Context

I was conducting exploratory testing of the logout functionality. My testing charter was:

> *"Explore logout behavior and verify that user session is properly invalidated. Test edge cases around browser navigation and cached pages."*

### The Discovery Moment

After logging out successfully, I noticed I was still on the transactions page. Out of curiosity, I clicked the browser's **back button** to see what would happen.

**Expected behavior:** I should see a login page or an error message stating my session had expired.

**Actual behavior:** The transaction page was still visible with all my account data displayed.

🚨 **Red flag!** This shouldn't be possible after logout.

### Testing the Hypothesis

I decided to test if this was just a visual cache issue or if I could actually perform actions:

1. After logout, I clicked browser back button
2. The transaction page displayed normally
3. I filled in transaction details:
   - Recipient account: Test account
   - Amount: 10 CZECHITOKEN
4. I clicked "Transfer"
5. **The transaction was successful!** ✅ (But it shouldn't be!)

I checked the transaction history and the balance - the money was actually transferred.

---

## 🛠️ Technical Investigation

### Using Browser DevTools

I opened Chrome DevTools to understand what was happening at the network level.

#### **Step 1: Checked Cookies**
- Opened Application tab → Cookies
- After logout: `__Secure-better-auth.session_token` cookie was **deleted** ✅
- This means logout was working correctly on the frontend

#### **Step 2: Analyzed Network Requests**

**Before Logout:**
```
POST /api/v1/transactions
Headers:
  Cookie: __Secure-better-auth.session_token=cmXXXXXXXXXX
Response: 200 OK
```

**After Logout (using back button):**
```
POST /api/v1/transactions
Headers:
  Cookie: (empty)
Response: 200 OK ⚠️
```

**Key Finding:** The API endpoint returned `200 OK` even without the session cookie!

#### **Step 3: Tested with Postman**

To confirm this wasn't browser-specific, I tested in Postman:
```
POST https://czechibank.ostrava.digital/api/v1/transactions
Headers:
  (No cookie, no API key, no authentication)
Body:
{
  "toBankNumber": "287982456568/5555",
  "amount": 10
}

Response: 200 OK
Transaction created successfully ⚠️
```

**This confirmed the bug:** The endpoint has no authentication validation whatsoever.

---

## 💡 Understanding the Problem

### What Should Happen
```
User → Logout → Session Token Deleted → API Validates Token → Returns 401 Unauthorized
```

### What Actually Happens
```
User → Logout → Session Token Deleted → API Does NOT Validate → Returns 200 OK ⚠️
```

### Why This is Critical

1. **Security Risk:** Anyone can create transactions without authentication
2. **Session Management Failure:** Backend doesn't check if user is logged in
3. **Authorization Bypass:** No validation of user identity before financial operations
4. **Data Integrity:** Transactions appear with incorrect sender information

---

## 📋 Reproduction Steps

**Environment:**
- Application: https://czechibank.ostrava.digital/
- Browser: Safari, Chrome (reproduced in both)
- User: Test account with valid credentials

**Steps:**

1. Log in to the application with valid credentials
2. Navigate to the transactions page
3. Click "Sign Out" button
4. Verify logout was successful (redirected to login page)
5. Click browser **back button**
6. Observe that transaction page is still visible
7. Fill in transaction form:
   - Select recipient account
   - Enter amount (e.g., 10 CZECHITOKEN)
8. Click "Transfer"
9. **Actual Result:** Transaction is created successfully despite being logged out
10. **Expected Result:** Should show error message "Session expired" or redirect to login

---

## 🎯 Business Impact

### Severity Assessment

| Factor | Rating | Justification |
|--------|--------|---------------|
| **Security** | Critical | Allows unauthorized financial operations |
| **Frequency** | Medium | Occurs every time user uses back button after logout |
| **Discoverability** | Easy | Standard user behavior (back button) triggers it |
| **Data Impact** | High | Affects financial transactions and user accounts |

### Affected Users

- All users who log out and use browser back button
- Potentially exploitable by malicious actors
- Affects data integrity and audit trails


## 📚 Lessons Learned

### What This Bug Taught Me

1. **Test Beyond Happy Paths:** The bug was discovered by testing edge cases like browser navigation after logout

2. **Browser Behavior Matters:** Understanding how browsers cache pages and handle navigation is crucial for security testing

3. **Never Trust the Client:** Even if frontend removes session cookie, backend must always validate

4. **DevTools Are Essential:** Without Network tab analysis, I might have thought this was just a UI caching issue

5. **Think Like an Attacker:** I asked "What if someone tried to do this?" and found a vulnerability

### Testing Techniques Applied

- 🧭 **Exploratory Testing:** Free-form investigation led to discovery
- 🔒 **Security Testing:** Thinking about authentication bypass scenarios
- 🔬 **Investigation:** Using DevTools to understand root cause
- ✅ **Confirmation Testing:** Reproducing in multiple browsers and via API

---

## 🔗 Related Issues

This bug is related to another finding:

**[CZBANK-50: Missing Authorization on POST /transactions](../bug-reports/high/CZBANK-50-missing-authorization.md)**

Both issues stem from the same root cause: insufficient authentication/authorization validation on the transactions endpoint.

---

## 📊 Testing Metrics

**Time to Discovery:** 15 minutes of exploratory testing  
**Time to Investigation:** 30 minutes (DevTools analysis + Postman testing)  
**Time to Documentation:** 45 minutes  
**Developer Response Time:** < 24 hours  
**Priority Assigned:** High  
**Status:** Approved for Fix  

---

## 💬 Developer Communication

**My Initial Report:**

> "After logout, users can still create transactions using browser back button. Session cookie is removed but API doesn't validate session. Tested in Safari and Chrome, reproduced via Postman."

**Developer Response :**

> "Bug approved. This is critical issue we need to fix. Thanks for detailed investigation!"

---

## 🎓 Key Takeaway

This case study demonstrates:
- ✅ Systematic approach to exploratory testing
- ✅ Technical investigation using DevTools
- ✅ Security mindset (thinking about authentication bypass)
- ✅ Clear documentation of findings with reproduction steps
- ✅ Understanding of business impact and severity assessment

**The most important lesson:** Sometimes the biggest bugs are found not by following test cases, but by asking "What if I try this?" and being curious about edge cases.

---

*Last updated: October 2025*
