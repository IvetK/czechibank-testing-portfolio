
# Case Study: Session Invalidation After Logout

**Bug ID:** CZBANK-69 | **Severity:** High | **Status:** ✅ Resolved

## Executive Summary

Discovered a security vulnerability allowing users to perform financial transactions after logout using browser back button. Session cookie was deleted, but backend still processed requests without validation.

**Impact:** Unauthorized financial transactions possible
**Root Cause:** Missing session validation on transactions endpoint

---

## Discovery Process

While testing logout functionality, I tested a common user behavior: clicking the browser back button after logout.

**What happened:**
1. Logged in and navigated to Transactions page
2. Clicked "Sign Out" → Redirected to login page ✅
3. Pressed browser back button
4. Transactions page appeared (from cache)
5. Created test transaction for 10 CZECHITOKEN
6. **Result:** Transaction processed successfully ⚠️

The page wasn't just cached UI - the actual transaction was recorded in the database.

---

## Technical Investigation

**DevTools Analysis:**
- Application tab confirmed session cookie was deleted on logout ✅
- Network tab showed POST /transactions returned 200 OK without session cookie ⚠️

**Comparative Testing:**

Tested same back-button scenario from different pages:
- Profile Page - Generate avatar → ❌ 401 Unauthorized
- Profile Page - Create API key → ❌ 401 Unauthorized  

**Key finding:** Inconsistent security implementation - Profile endpoints properly validate sessions, Transactions endpoint doesn't.

**Verification:**
Checked via Postman that transaction was actually saved in database, confirming it wasn't just a UI glitch.

---

## Root Cause

**Frontend:** Browser cached transactions page, back button showed cached version with functional form

**Backend:** Transactions endpoint missing session validation - accepted and processed requests without valid session cookie

**Comparison:**
- Profile endpoints: ✅ Proper validation (correctly return 401)
- Transactions endpoint: ❌ Missing validation
---

## Key Insights

- **Test realistic user behavior** - Critical bugs can hide behind common actions like browser navigation
- **Compare similar features** - Inconsistency between Profile and Transactions endpoints revealed the specific vulnerability
- **Multiple verification methods** - Used DevTools, Postman, and comparative testing to confirm the issue

---

**Related:** [Full Bug Report CZBANK-69](../bug-reports/high/CZBANK-69_Session_Invalidation.md)


**For full details, see [Bug Report CZBANK-69](../bug-reports/high/CZBANK-69_Session_Invalidation.md)**

---

*Last updated: October 2025*
