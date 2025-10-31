
# Case Study: Session Invalidation After Logout

**Bug ID:** CZBANK-69 | **Severity:** High | **Status:** ✅ Resolved

## 🎯 Executive Summary

Discovered a critical security vulnerability where users could perform financial transactions after logout by using the browser back button. The Session cookie was deleted on logout, but transactions endpoint still processed requests, enabling unauthorized operations.

**Impact:** Unauthorized financial transactions  
**Root Cause:** Missing session validation + frontend cache  
**Resolution:** Session validation implemented, cache properly cleared

---

## 🔍 The Discovery

### Testing Context

Exploratory testing of logout functionality, focusing on session invalidation and user access control.

### The "Aha" Moment

After logout, I wondered: *"What if I click the back button?"* (common user behavior)

**Steps:**
1. Logged in and created test transaction
2. Clicked "Sign Out" → Redirected to login ✅
3. Clicked browser **back button**
4. **Transactions page appeared** - looked like I was still logged in! ⚠️
5. Filled transaction form (10 CZECHITOKEN)
6. Clicked "Transfer"
7. **Success!** Transaction processed and recorded ⚠️

**This wasn't just cached UI - real financial operations were executed.**

---

## 🛠️ Technical Investigation

### DevTools Analysis

**Application Tab → Cookies:**
- Before logout: `__Secure-better-auth.session_token` present
- After logout: Cookie **deleted** ✅
- Frontend logout worked correctly

**Network Tab → POST /transactions:**

After logout (via back button):
```
POST /api/v1/transactions
Headers:
  Cookie: (no session cookie!)
→ Response: 200 OK ⚠️ (Should be 401!)
```

**Key finding:** Transaction accepted **without valid session cookie**.

### Comparison Testing: Profile vs Transactions

Tested same back button behavior from different pages:

**Profile page after logout:**
- Generate avatar → ❌ "Unauthorized or invalid session"
- Create API key → ❌ "Unauthorized or invalid session"

**Transactions page after logout:**
- Create transaction → ✅ Success (should fail!)

**Discovery:** Inconsistent security implementation across endpoints.

### Verification in Postman

```
GET /api/v1/transactions
```
**Result:** Transaction created after logout was in database.

Confirmed it's not just UI issue - backend actually processed unauthorized transaction.

---

## 💡 Root Cause

### Two-Layer Problem:

**Frontend:**
- Browser caches transactions page
- Back button shows cached version
- Form remains functional

**Backend:**
- Transactions endpoint has insufficient session validation
- Accepts requests without valid session cookie
- Processes operations (returns 200 OK instead of 401)

**Comparison:**
- Profile endpoints: ✅ Proper validation (correctly return 401)
- Transactions endpoint: ❌ Missing validation

---

## 🧪 Additional Tests

**Test 1:** Logout → Refresh → Back button  
**Result:** ✅ Correctly blocked (refresh clears cache)

**Test 2:** Anonymous window  
**Result:** ⚠️ Bug reproduces (not related to stored cookies)

**Test 3:** Different logout pages  
**Result:** Only transactions page vulnerable

---

## 📚 Key Lessons

1. **Test realistic user behavior** - Found by simulating common action (back button)
2. **Browser back is a security test** - Always test navigation after logout
3. **Compare similar endpoints** - Inconsistency revealed the specific problem
4. **DevTools reveal truth** - Showed frontend did job (deleted cookie), backend didn't (accepted request)
5. **Verify multiple ways** - Frontend + Network tab + Postman = confirmed real vulnerability
6. **Document with evidence** - Screenshots and DevTools data made bug report compelling

---

## 🎓 What This Demonstrates

✅ **Security testing** - Authentication boundary testing  
✅ **Exploratory skills** - Realistic user scenario testing  
✅ **Technical investigation** - DevTools diagnostic analysis  
✅ **Comparative analysis** - Pattern identification across endpoints  
✅ **Systematic verification** - Multi-method confirmation  
✅ **Professional communication** - Evidence-based bug documentation  

**Key insight:** Critical vulnerabilities can hide behind common user actions. Testing realistic behaviors (like "back" button) is as important as testing designed flows.

---

**For full details, see [Bug Report CZBANK-69](../bug-reports/high/CZBANK-69_Session_Invalidation.md)**

---

*Last updated: October 2025*
