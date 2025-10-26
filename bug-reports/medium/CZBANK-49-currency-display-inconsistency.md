# [CZBANK-49] API Accepts Unsupported Currencies Without Validation

**Priority:** Medium  
**Severity:** Medium  
**Status:** Ready to DEV  
**Reporter:** Iveta Kuklová  
**Date Reported:** July 2025  
**Environment:** Development  

---

## 📋 Summary

API accepts transaction requests with currency parameters (CZK, USD) that are not officially supported according to API documentation. The system processes these transactions as CZECHITOKEN without validation, conversion, or error messages, creating inconsistency between API documentation, implementation, and frontend behavior.

---

## 🔍 Description

### Issue Details

While testing the transactions API in Postman, I discovered a discrepancy between:

**API Documentation states:**
- Only supported currency: **CZECHITOKEN**

**API actually accepts:**
- CZECHITOKEN ✅
- CZK ✅ (works but undocumented)
- USD ✅ (works but undocumented)

**Frontend allows:**
- Only CZECHITOKEN

**What happens:**
Regardless of which currency is sent via API (CZK, USD), the transaction is processed and credited to the recipient account as CZECHITOKEN with **no currency conversion** applied. The system accepts the amount at face value (e.g., 100 EUR becomes 100 CZECHITOKEN).

### Why This Matters

**Inconsistency Issues:**
1. API documentation doesn't match implementation
2. Backend accepts currencies that shouldn't be supported
3. No validation or error handling for unsupported currencies
4. Frontend and backend have different behavior

**Data Integrity:**
- Transactions created with wrong currency labels
- No conversion rates applied
- Could cause confusion in transaction history
- Financial amounts may be incorrect

---

## 🌐 Environment

**Testing Environment:**
- Application: CzechiBank (educational banking application)
- Environment: Development
- API Endpoint: `POST /api/v1/transactions/create`
- Testing Tool: Postman
- Date: July 2025

**Documentation Reference:**
- API Documentation: `https://[dev-environment]/api/v1/docs/page`
- Stated supported currency: CZECHITOKEN only

---

## 📝 Steps to Reproduce

### Prerequisites
1. Access to Postman
2. Valid API key
3. Two bank accounts (sender and receiver)
4. Access to API documentation

### Test 1: Verify Documentation

1. Open API documentation
2. Navigate to `POST /transactions/create` endpoint
3. Check supported currencies
4. **Documented:** Only CZECHITOKEN

### Test 2: Test with CZECHITOKEN (Expected)

**Postman Request:**
```
POST https://[dev-environment]/api/v1/transactions/create

Headers:
  x-api-key: [valid-api-key]

Body:
{
  "toBankNumber": "123456789012/5555",
  "amount": 100,
  "currency": "CZECHITOKEN"
}
```

**Result:** 
- Response: `201 Created` ✅
- Transaction processed successfully ✅
- Expected behavior ✅

### Test 3: Test with CZK (Undocumented)

**Postman Request:**
```
POST https://[dev-environment]/api/v1/transactions/create

Headers:
  x-api-key: [valid-api-key]

Body:
{
  "toBankNumber": "123456789012/5555",
  "amount": 100,
  "currency": "CZK"
}
```

**Expected:** `400 Bad Request` - "Unsupported currency: CZK"  
**Actual:** `201 Created` ⚠️ - Transaction successful!

**What happens:**
- Transaction created with "CZK" label
- Recipient receives 100 CZECHITOKEN (not CZK!)
- No conversion rate applied
- No error or warning

### Test 4: Test with USD 

Same result - API accepts USD and processes as CZECHITOKEN.

### Test 5: Frontend Comparison

1. Open web application
2. Navigate to money transfer
3. Attempt to specify currency
4. **Observation:** Frontend only allows CZECHITOKEN
5. No option to select CZK, or USD

**Conclusion:** Frontend and API have different validation rules.

---

## 🎯 Expected Behavior

**Since API documentation states only CZECHITOKEN is supported:**

### Option 1: Strict Validation (Recommended by Developer)

**API should reject unsupported currencies:**
```json
POST /transactions/create
Body: { "currency": "CZK", ... }

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

### Option 2: Update Documentation

If multiple currencies ARE supported:
- Update API documentation to list all supported currencies
- Implement proper currency conversion logic
- Add conversion rates
- Store and display transactions with correct currencies

**Developer decision:** Wanted **Option 1** - single currency only.

---

## 🐛 Actual Behavior

**Current System:**

1. **API accepts any currency** parameter without validation
2. **Treats all currencies as CZECHITOKEN** (1:1, no conversion)
3. **No error messages** for unsupported currencies
4. **Inconsistent with documentation** (says only CZECHITOKEN)

**Example Issue:**
```
User sends via API:
  100 EUR to Account A

System processes:
  100 CZECHITOKEN to Account A
  
Actual value:
  EUR exchange rate ~23 CZK = 2,300 CZK worth
  But user only transferred 100 tokens
  
Result: Massive discrepancy if currencies were real!
```

---

## 💥 Impact Assessment

### Severity: MEDIUM

**Data Integrity Impact:**
- Transactions stored with incorrect currency labels
- Could cause confusion in financial reports
- Transaction history shows wrong currency

**Business Logic Impact:**
- No validation of business rules (single currency)
- Backend allows behavior that frontend prevents
- Inconsistent user experience (API vs UI)

**Documentation Gap:**
- API behavior doesn't match documentation
- Developers using API might make incorrect assumptions
- Could lead to integration issues

### Affected Users

**Low Impact** (because educational project):
- In educational context, doesn't affect real money
- But demonstrates important validation gap

**Would be HIGH Impact** in production:
- Real banking app: Could cause financial losses
- Currency conversion errors
- Regulatory compliance issues

---

## 🔧 Suggested Fix

### Recommended Solution (Per Developer Preference)

**Implement strict currency validation:**

1. **Backend Validation:**
```
   If currency parameter provided:
     If currency != "CZECHITOKEN":
       Return 400 Bad Request
       Error: "Only CZECHITOKEN is supported"
   
   If currency parameter missing:
     Default to CZECHITOKEN
```

2. **Remove Currency Field:**
   - Remove currency parameter from API endpoint
   - Always use CZECHITOKEN implicitly
   - Update API schema/documentation

3. **Consistent Behavior:**
   - API and frontend enforce same rules
   - Clear error messages for unsupported values
   - Documentation matches implementation

---

## 📊 Testing Evidence

### Test Matrix

| Currency Sent | Expected Response | Actual Response | Issue |
|---------------|-------------------|-----------------|-------|
| CZECHITOKEN | 201 Created ✅ | 201 Created ✅ | Works correctly |
| CZK | 400 Bad Request | 201 Created ⚠️ | Accepts undocumented currency |
| USD | 400 Bad Request | 201 Created ⚠️ | Accepts undocumented currency |

### Postman Test Results

**Test Collection Results:**
- CZECHITOKEN: ✅ Pass
- CZK: ❌ Fail (should reject, but accepts)
- USD: ❌ Fail (should reject, but accepts)

**Frontend Comparison:**
- Frontend: Only CZECHITOKEN selectable ✅
- API: Accepts multiple currencies ❌
---

## 💬 Communication History

**Initial Report:**
> "API documentation states only CZECHITOKEN is supported, but API actually accepts CZK and USD without validation. These transactions are processed as CZECHITOKEN with no conversion rate. Frontend only allows CZECHITOKEN. This creates inconsistency between documentation, backend, and frontend."

**Developer Response:**
> "We want to support only one currency (CZECHITOKEN). API should validate and reject other currencies."

**Recommended Action:**
> "Add validation to reject non-CZECHITOKEN currencies with clear error message. Update API schema to reflect single currency support."

---

## 🔗 Related Issues

**Documentation Gaps:**
- CZBANK-40: API Documentation inconsistencies
- CZBANK-41: Bank account creation allows currency selection (related issue)

---

## ✅ Verification Steps

After fix is implemented:

**Test 1: CZECHITOKEN (should work)**
```
POST /transactions/create
Body: { "currency": "CZECHITOKEN", "amount": 100, ... }
Expected: 201 Created ✅
```

**Test 2: CZK (should fail)**
```
POST /transactions/create
Body: { "currency": "CZK", "amount": 100, ... }
Expected: 400 Bad Request ✅
Error message: "Only CZECHITOKEN is supported"
```

**Test 3: Missing currency (should default)**
```
POST /transactions/create
Body: { "amount": 100, ... }  // no currency field
Expected: 201 Created, defaults to CZECHITOKEN ✅
```

**Test 4: Invalid currency (should fail)**
```
POST /transactions/create
Body: { "currency": "INVALID", "amount": 100, ... }
Expected: 400 Bad Request ✅
```

---

## 🎓 Lessons Learned

### What This Bug Taught Me

**1. Always Compare Documentation vs. Implementation**
- Documentation said one thing (CZECHITOKEN only)
- Implementation did another (accepted multiple currencies)
- Testing revealed the gap

**2. Test Beyond Happy Path**
- Happy path: CZECHITOKEN works ✅
- Edge cases: What about other currencies? ⚠️
- Found the validation gap

**3. Frontend vs. Backend Testing**
- Frontend enforces one rule (CZECHITOKEN only)
- Backend enforces different rule
- API testing revealed backend weakness

**4. Equivalence Partitioning**
- Valid class: CZECHITOKEN
- Invalid classes: CZK, USD, random strings
- Testing invalid classes found the bug

**5. Clear Communication with Developers**
- Presented findings objectively
- Asked for clarification on intended behavior
- Developer confirmed: single currency intended
- Clear fix direction established

### Testing Technique Applied

**Negative Testing:**
- Didn't just test valid inputs
- Tested unsupported values
- Found that validation was missing

**API vs UI Testing:**
- Compared frontend and backend behavior
- Identified inconsistency
- This is why API testing complements UI testing

---

## 🏷️ Labels

`api` `validation` `currency` `data-integrity` `documentation-gap` `backend` `postman-testing`

---

*Bug report created: July 2025*  
*Last updated: October 2025*
