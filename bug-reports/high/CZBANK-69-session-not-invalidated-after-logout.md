# [CZBANK-69] Session Not Invalidated After Logout

**Priority:** High  
**Severity:** Critical  
**Status:** Confirmed  
**Reporter:** Iveta Kuklová  
**Date Reported:** August 2025

---

## 📋 Summary

After user logout, the session is not properly invalidated on the backend. Users can perform financial transactions by using the browser's back button to return to the application after logging out. The `POST /transactions` endpoint does not validate session state, allowing unauthorized transaction creation.

---

## 🌐 Environment

- **Application:** CzechiBank (Development)
- **Affected Endpoint:** `POST /api/v1/transactions`
- **Browser:** Safari, Chrome (reproduced in both)
- **Date:** August 2025

---

## 🔍 Description

During exploratory testing of the logout functionality, I discovered that:
1. User logs out successfully (redirected to login page)
2. Session cookie is correctly removed from browser
3. User clicks browser back button
4. Application page is still visible with user data (expected browser cache behavior)
5. **User can successfully create transactions** (critical issue)

The `POST /transactions` API endpoint accepts requests and processes transactions even after the user has logged out, indicating missing server-side session validation.

**Security Impact:** This is a critical security vulnerability that enables unauthorized financial operations after logout. Session state on client and server are not synchronized.

---

## 📝 Steps to Reproduce

### Prerequisites
- Valid user account with login credentials
- User has at least one bank account with balance

### Steps

1. **Login to Application**
   - Navigate to login page and enter valid credentials
   - Click "Sign In"
   - Verify successful login (user is on main dashboard)

2. **Navigate to Transactions**
   - Click on a bank account
   - Go to "Transfer Money" section

3. **Logout**
   - Click "Sign Out" button
   - Verify logout successful (redirected to `/signin` page)

4. **Use Browser Back Button**
   - Click browser back button (←)
   - Transaction page is still visible (browser cache)

5. **Attempt Transaction**
   - Fill in transaction form:
     - Select recipient account
     - Enter amount (e.g., 10 CZECHITOKEN)
   - Click "Transfer"

6. **Observe Result**
   - Transaction processes successfully
   - Confirmation message appears
   - Transaction is visible in history
   - Account balance is updated

---

## 🎯 Expected Behavior

**After logout:**
1. Session cookie should be deleted from browser ✅
2. Backend should invalidate the session ❌
3. Any authenticated API requests should return `401 Unauthorized` ❌
4. User attempting to access protected endpoints should be rejected ❌

**When using back button after logout:**
- API should reject transaction requests with `401 Unauthorized`
- User should see "Session expired" error
- No financial operations should be possible

---

## 🐛 Actual Behavior

**What happens:**
1. Frontend correctly removes session cookie from browser
2. Page content remains in browser cache (expected browser behavior)
3. Backend accepts API requests without session validation
4. Transactions can be created after logout

### POST Request Analysis (Browser DevTools)

**Before Logout:**
```
POST /api/v1/transactions
Headers:
  Cookie: __Secure-better-auth.session_token=cmXXXXXX
Response: 200 OK
Transaction created ✅
```

**After Logout (using back button):**
```
POST /api/v1/transactions
Headers:
  Cookie: (empty - no session cookie sent)
Response: 200 OK ⚠️
Transaction created ❌ (should fail with 401!)
```

**Key Finding:** The endpoint processes requests successfully even without a session cookie, indicating **no server-side authentication validation**.

---

## 🔗 Related Issues

**CZBANK-71:** API returns 500 instead of 401 with deleted API key (also missing auth validation)

**Pattern:** Multiple endpoints appear to lack proper authentication validation. Recommend comprehensive authentication review across all protected endpoints.

---

## 📎 Additional Notes

**Discovery Method:** Exploratory testing of logout functionality, testing browser back button behavior  
**Reproducibility:** 100% - reproduced in Safari, Chrome, and via Postman without session cookie

---

*Bug reported: August 2025*  
*Last updated: October 2025*
