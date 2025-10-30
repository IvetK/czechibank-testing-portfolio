# [CZBANK-41] Bank Account Creation - Duplicate Default Names

**Priority:** Medium  
**Severity:** Low  
**Status:** ✅ Fixed  
**Reporter:** Iveta Kuklová  
**Date Reported:** July 8, 2025  
**Resolution Date:** August/September 2025

---

## 📋 Summary

When creating multiple bank accounts via API endpoint `POST /bank-account/create`, all accounts received identical default name "New Bank Account" with no differentiation. Users could not distinguish between accounts without checking account numbers or balances.

**Resolution:** Fixed with sequential numbering ("New Bank Account 01, 02, 03...") and UI functionality for custom account names.

---

## 🌐 Environment

- **Application:** CzechiBank (Development)
- **Endpoint:** `POST /api/v1/bank-account/create`
- **Testing Tool:** Postman
- **Date Discovered:** July 8, 2025

**Original Limitation:** Bank accounts could only be created via API; no frontend UI functionality existed.

---

## 🔍 Description

### Original Issue

When testing account creation through Postman:
- API did not accept a "name" parameter
- System assigned default name: "New Bank Account" to **all accounts**
- No sequential numbering or differentiation
- Multiple accounts had identical names

**User Impact:**
```
Account 1: "New Bank Account"
Account 2: "New Bank Account"  ← Identical
Account 3: "New Bank Account"  ← Identical
```

Users had to check account numbers or balances to identify the correct account, increasing risk of selecting wrong account for transfers.

---

## 📝 Steps to Reproduce (Original Bug)

### Prerequisites
- Valid user account with API key
- Access to Postman

### Steps

1. **Create First Account**
   ```
   POST https://[dev-environment]/api/v1/bank-account/create
   Headers: x-api-key: [valid-api-key]
   Body: { "currency": "CZECHITOKEN" }
   Response: { "name": "New Bank Account" }
   ```

2. **Create Second Account**
   ```
   POST /bank-account/create
   Body: { "currency": "CZECHITOKEN" }
   Response: { "name": "New Bank Account" }  ← Same name!
   ```

3. **View All Accounts**
   ```
   GET /bank-account
   Response: All accounts show identical "New Bank Account" name
   ```

**Result:** User cannot distinguish accounts by name.

---

## 🎯 Expected Behavior

Accounts should be distinguishable through one of:

**Option 1: Accept Custom Names**
```json
POST /bank-account/create
Body: {
  "currency": "CZECHITOKEN",
  "name": "Personal Savings"
}
```

**Option 2: Sequential Default Naming**
```
"New Bank Account 01"
"New Bank Account 02"
"New Bank Account 03"
```

**Option 3: UI Functionality**
- Frontend interface for account creation
- Ability to specify custom names

---

## 🐛 Actual Behavior (Original)

1. API did not accept "name" parameter (ignored if provided)
2. System always assigned: "New Bank Account" to every account
3. No sequential numbering
4. No UI alternative available
5. Users forced to identify accounts by number or balance

---

## ✅ Resolution & Fix

### What Was Implemented

**1. UI Functionality Added** ✅
- Frontend interface for account creation
- Custom naming available during creation

**2. API Enhanced** ✅
- API now accepts "name" parameter
- Validation added for name field

**3. Sequential Default Naming** ✅
- Default names: "New Bank Account 01", "02", "03", etc.
- Each account receives unique identifier

**After Fix:**
```
Without names: "New Bank Account 01", "02", "03"
With custom names: "Personal Savings", "Business Account"
```

---

## 🧪 Verification Testing

Retested after fix was deployed:

**Test 1: Default Sequential Naming**
```
Create 3 accounts without names via API
Result: ✅ "New Bank Account 01, 02, 03" - unique names
Status: PASS
```

**Test 2: Custom Names via API**
```
POST /bank-account/create
Body: { "name": "My Savings" }
Result: ✅ Account created with custom name
Status: PASS
```

**Test 3: UI Account Creation**
```
Create account through frontend
Result: ✅ Custom name accepted and saved
Status: PASS
```

**Test 4: Mixed Creation Methods**
```
Create via both UI and API
Result: ✅ All accounts distinguishable
Status: PASS
```

**Verification Result:** ✅ **BUG RESOLVED** - All test cases pass

---

## 🔗 Related Improvements

Features added as part of fix:
- UI for bank account creation
- Custom account naming (UI and API)
- Sequential default naming system
- Improved account management

---

## 📎 Additional Notes

**Discovery Method:** API testing with multiple account creation scenarios  
**Suggested Solutions:** Provided in original report (sequential numbering or custom names)  
**Implementation:** Development team implemented comprehensive fix addressing all suggestions

---

*Bug reported: July 8, 2025*  
*Bug fixed: August/September 2025*  
*Verification completed: September 2025*  
*Status: ✅ Resolved*
