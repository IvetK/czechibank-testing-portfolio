# [CZBANK-49] API Accepts Unsupported Currencies Without Validation

**Priority:** Medium  
**Severity:** Medium  
**Status:** ✅ Resolved  
**Reporter:** Iveta Kuklová  
**Date Reported:** July 2025  
**Resolution Date:** August/September 2025

---

## 📋 Summary

API accepted transaction requests with currency parameters (CZK, USD) that were not officially supported according to API documentation. The system processed these transactions as CZECHITOKEN without validation, conversion, or error messages, creating inconsistency between documentation, implementation, and frontend behavior.

**Resolution:** Fixed - API now validates currency and only accepts CZECHITOKEN. Unsupported currencies are rejected with proper error messages.

---

## 🌐 Environment

- **Application:** CzechiBank (Development)
- **Endpoint:** `POST /api/v1/transactions/create`
- **Testing Tool:** Postman
- **Date Discovered:** July 2025
- **API Documentation:** `https://[dev-environment]/api/v1/docs/page`

---

## 🔍 Description

### Original Issue

While testing the transactions API in Postman, I discovered inconsistency between documentation, implementation, and frontend:

**API Documentation:** Only CZECHITOKEN supported  
**API Actually Accepted:** CZECHITOKEN, CZK, USD 
**Frontend Allowed:** Only CZECHITOKEN  

**What Was Happening:**
Regardless of which currency was sent via API (CZK, USD), the transaction was processed and credited as CZECHITOKEN with **no currency conversion**. The system accepted the amount at face value (e.g., 100 USD became 100 CZECHITOKEN).

**Issues:**
- API documentation didn't match implementation
- No validation for unsupported currencies
- Frontend and backend had different behavior
- Transactions created with incorrect currency labels

---

## 📝 Steps to Reproduce (Original Bug)

### Prerequisites
- Access to Postman
- Valid API key
- Two bank accounts (sender and receiver)

### Steps

**Test 1: Verify Documentation**
1. Open API documentation
2. Check `POST /transactions/create` endpoint
3. **Documented:** Only CZECHITOKEN supported

**Test 2: Test with CZECHITOKEN (Expected Behavior)**
```
POST https://[dev-environment]/api/v1/transactions/create
Headers: x-api-key: [valid-api-key]
Body: {
  "toBankNumber": "123456789012/5555",
  "amount": 100,
  "currency": "CZECHITOKEN"
}
Result: 201 Created ✅ (expected)
```

**Test 3: Test with CZK (Should Have Rejected)**
```
POST /transactions/create
Body: {
  "toBankNumber": "123456789012/5555",
  "amount": 100,
  "currency": "CZK"
}
Expected: 400 Bad Request - "Unsupported currency"
Actual (Original Bug): 201 Created ⚠️ - Transaction successful!
```

**What Happened:**
- Transaction created with "CZK" label
- Recipient received 100 CZECHITOKEN (not CZK)
- No conversion rate applied
- No error or warning

**Test 4: Test with USD**
Same result - API accepted USD and processed as CZECHITOKEN.

**Test 5: Frontend Comparison**
- Frontend only allowed CZECHITOKEN selection
- **Conclusion:** Frontend and API had different validation rules

---

## 🎯 Expected Behavior

Since API documentation stated only CZECHITOKEN is supported:

**API should reject unsupported currencies:**

```json
POST /transactions/create
Body: { "currency": "CZK", "amount": 100, ... }

Response: 400 Bad Request
{
  "success": false,
  "message": "Unsupported currency",
  "error": {
    "code": "400",
    "message": "Only CZECHITOKEN currency is supported. Provided: CZK"
  }
}
```

**Validation rules:**
- Accept: CZECHITOKEN ✅
- Reject: CZK, USD, or any other currency ❌
- Return clear error message

---

## 🐛 Actual Behavior (Original)

**Before fix:**
1. API accepted any currency parameter without validation
2. Treated all currencies as CZECHITOKEN (1:1, no conversion)
3. No error messages for unsupported currencies
4. Inconsistent with documentation

**Example Issue:**
```
User sent via API: 100 USD
System processed: 100 CZECHITOKEN
```

### Test Results Matrix (Original Bug)

| Currency Sent | Expected | Actual (Before Fix) |
|---------------|----------|---------------------|
| CZECHITOKEN | 201 ✅ | 201 ✅ |
| CZK | 400 ❌ | 201 ⚠️ (Accepted) |
| USD | 400 ❌ | 201 ⚠️ (Accepted) |

---

## ✅ Resolution & Fix

### What Was Implemented

The development team implemented strict currency validation:

**1. Currency Validation Added** ✅
- API now validates currency parameter
- Only CZECHITOKEN is accepted
- Unsupported currencies are rejected with 400 Bad Request

**2. Error Messages Improved** ✅
- Clear error message when unsupported currency provided
- Specifies which currency was provided and what is expected

**3. Consistent Behavior** ✅
- API and frontend now enforce same validation rules
- Documentation matches implementation
- No more inconsistency

**After Fix:**
```
Request with CZK:
  → 400 Bad Request
  → Error: "Only CZECHITOKEN is supported"

Request with CZECHITOKEN:
  → 201 Created
  → Transaction successful
```

---

## 🧪 Verification Testing

Retested after fix was deployed:

**Test 1: CZECHITOKEN (Should Work)**
```
POST /transactions/create
Body: { "currency": "CZECHITOKEN", "amount": 100, ... }
Result: ✅ 201 Created - Transaction successful
Status: PASS
```

**Test 2: CZK (Should Reject)**
```
POST /transactions/create
Body: { "currency": "CZK", "amount": 100, ... }
Result: ✅ 400 Bad Request - "Unsupported currency"
Status: PASS
```

**Test 3: USD (Should Reject)**
```
POST /transactions/create
Body: { "currency": "USD", "amount": 100, ... }
Result: ✅ 400 Bad Request - "Unsupported currency"
Status: PASS
```

**Test 4: Missing Currency (Should Default)**
```
POST /transactions/create
Body: { "amount": 100, ... }  // no currency field
Result: ✅ 201 Created - Defaults to CZECHITOKEN
Status: PASS
```

**Test 5: Invalid Currency (Should Reject)**
```
POST /transactions/create
Body: { "currency": "INVALID", "amount": 100, ... }
Result: ✅ 400 Bad Request
Status: PASS
```

**Verification Result:** ✅ **BUG RESOLVED** - All test cases pass

---

## 🔗 Related Issues

**CZBANK-40:** API Documentation inconsistencies  
**CZBANK-41:** Bank account creation - duplicate names (also resolved)

---

## 📎 Additional Notes

**Discovery Method:** API testing with equivalence partitioning (valid vs invalid currency values)  
**Frontend vs Backend Testing:** Revealed inconsistency between UI and API validation  
**Developer Decision:** Single currency (CZECHITOKEN) only, strict validation implemented

---

*Bug reported: July 2025*  
*Bug fixed: August/September 2025*  
*Verification completed: September 2025*  
*Status: ✅ Resolved*
