# [CZBANK-69] Session Not Invalidated After Logout

**Priority:** High  
**Severity:** Critical  
**Status:** Confirmed  
**Reporter:** Iveta Kuklová  
**Date Reported:** August 2025  
**Environment:** Development  

---

## 📋 Summary

After user logout, the session is not properly invalidated on the backend. Users can perform financial transactions by using the browser's back button to return to the application after logging out. The session cookie is removed from the browser, but the API endpoint `POST /transactions` does not validate the session, allowing unauthorized transaction creation.

---

## 🔍 Description

### Issue Details

During exploratory testing of the logout functionality, I discovered that:
1. User logs out successfully (redirected to login page)
2. Session cookie is removed from browser ✅
3. User clicks browser back button
4. Application page is still visible with user data
5. **User can successfully create transactions** ❌

The `POST /transactions` API endpoint accepts requests and processes transactions even after the user has logged out, indicating missing server-side session validation.

### Security Impact

This is a **critical security vulnerability** because:
- Enables unauthorized financial operations
- Violates authentication and authorization best practices
- Session state on client and server are not synchronized
- Could allow malicious actors to perform transactions if they intercept or cache requests

---

## 🌐 Environment

**Testing Environment:**
- Application: CzechiBank (educational banking application)
- Environment: Development
- Base URL: `https://[dev-environment]/`
- Browser: Safari, Chrome (reproduced in both)
- Date: August 2025

---

## 📝 Steps to Reproduce

### Prerequisites
- Valid user account with login credentials
- User has at least one bank account with balance
- Browser with back button functionality (all modern browsers)

### Detailed Steps

1. **Login to Application**
   - Navigate to login page
   - Enter valid credentials
   - Click "Sign In"
   - Verify successful login (user is on main dashboard)

2. **Navigate to Transactions**
   - Click on a bank account
   - Go to "Transfer Money" or transactions section

3. **Logout**
   - Click "Sign Out" button
   - Verify logout successful:
     - Redirected to login page ✅
     - URL changed to `/signin` ✅

4. **Use Browser Back Button** 🚨
   - Click browser back button (←)
   - Observe: Transaction page is still visible
   - User data, account balance, and transaction form are displayed

5. **Attempt Transaction**
   - Fill in transaction form:
     - Select recipient account
     - Enter amount (e.g., 10 CZECHITOKEN)
   - Click "Transfer"

6. **Verify Transaction Created** ❌
   - Transaction processes successfully
   - Confirmation message appears
   - Check transaction history: Transaction is visible
   - Check account balance: Money was transferred

---

## 🎯 Expected Behavior

**After logout:**
1. Session cookie should be deleted from browser ✅ (works)
2. Backend should invalidate session ❌ (doesn't work)
3. Any authenticated API requests should return `401 Unauthorized` ❌
4. User should be redirected to login page if trying to access protected pages ❌
5. **No financial operations should be possible** ❌

**When using back button after logout:**
- User should see login page, or
- User should see "Session expired" error, or
- Application should automatically redirect to login

**What should NOT happen:**
- ❌ Transaction form should not be accessible
- ❌ API should not accept transaction requests
- ❌ Financial operations should not be possible

---

## 🐛 Actual Behavior

**What actually happens:**

1. **Frontend:** Session cookie is correctly removed from browser
2. **Browser Cache:** Page content remains in browser cache (expected browser behavior)
3. **Backend:** API endpoint accepts requests without session validation
4. **Result:** Transactions can be created even after logout

**POST Request Analysis (using Browser DevTools):**

**Before Logout:**
```
POST /api/v1/transactions
Headers:
  Cookie: __Secure-better-auth.session_token=cmXXXXXX
Response: 200 OK
Transaction created successfully ✅
```

**After Logout (using back button):**
```
POST /api/v1/transactions
Headers:
  Cookie: (empty - no session cookie)
Response: 200 OK ⚠️
Transaction created successfully ❌ (should fail!)
```

**Key Finding:** The endpoint processes the request successfully even without the session cookie, indicating **no server-side authentication validation**.

---

## 💥 Impact Assessment

### Security Severity: CRITICAL

**Why this is critical:**

1. **Unauthorized Transactions**
   - Financial operations possible without valid authentication
   - Money can be transferred after user believes they are logged out
   
2. **Session Management Failure**
   - Backend does not validate user session
   - Authentication state is not enforced on server side
   
3. **Authorization Bypass**
   - User identity is not verified before transaction
   - No check if user is currently authenticated

4. **Data Integrity Risk**
   - Transactions recorded with potentially incorrect user attribution
   - Audit trail may be compromised

### Business Impact

- **Trust:** Users expect logout to immediately prevent all account access
- **Security:** Violates banking security best practices
- **Compliance:** May violate financial industry authentication requirements
- **Liability:** Unauthorized transactions could lead to disputes

### Affected Functionality

- POST `/transactions` (create transaction)
- Potentially other authenticated endpoints

---

## 🔧 Suggested Fix

### Root Cause

The `POST /transactions` endpoint likely:
1. Does not require authentication middleware
2. Does not validate session token
3. Processes requests based on cached data or other identifiers


## 📊 Testing Evidence

### Browser DevTools Analysis

**Application Tab (Cookies):**
- Before logout: `__Secure-better-auth.session_token` present ✅
- After logout: Cookie deleted ✅
- After back button: No cookie ✅

**Network Tab:**
- Transaction request sent without session cookie
- Response: 200 OK (should be 401)
- Transaction data in response body

### Database Verification

Checked transaction history in application:
- Transaction appears in sender's history
- Transaction appears in receiver's history
- Account balances updated correctly
- Transaction timestamp shows it was created after logout

**Conclusion:** Transaction was fully processed despite logout.

---

## 🔗 Related Issues

**Similar authentication/authorization issues:**
- CZBANK-71: API returns 500 instead of 401 (also missing auth validation)
- Pattern suggests need for comprehensive authentication review across all endpoints

---

## ✅ Verification Steps

After fix is deployed, verify:

**Test 1: Normal Logout**
1. Login → Navigate to transactions → Logout
2. Try to access any authenticated page directly via URL
3. Expected: Redirect to login or show 401 error ✅

**Test 2: Back Button After Logout**
1. Login → Navigate to transactions → Logout
2. Click browser back button
3. Try to perform transaction
4. Expected: Transaction fails with "Session expired" or 401 error ✅

**Test 3: API Direct Access**
1. Logout
2. Use Postman to call POST /transactions without session
3. Expected: 401 Unauthorized ✅

**Test 4: Expired Session**
1. Login and wait for session to expire
2. Try to perform transaction
3. Expected: Session expired error ✅

---

## 💬 Communication History

**Initial Report:**
> "After logout, users can still create transactions using browser back button. Session cookie is removed but API doesn't validate session. Tested in Safari and Chrome, reproduced via Postman without session cookie."

**Developer Response:**
> "Bug approved. This is critical issue we need to fix. Thanks for detailed investigation!"


## 🎯 Lessons Learned

### Testing Approach

**What led to discovery:**
- Exploratory testing of logout functionality
- Testing edge cases (browser back button)
- Using DevTools to understand underlying behavior
- Not assuming logout works correctly

**Key insight:** Standard user behaviors (like using back button) can expose serious security vulnerabilities. Always test common browser actions after state-changing operations like logout.

---

## 🏷️ Labels

`security` `critical` `authentication` `session-management` `authorization` `exploratory-testing`

---

*Bug report created: August 2025*  
*Last updated: October 2025*
