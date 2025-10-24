# [CZBANK-47] Delete Bank Account with Non-Zero Balance

**Priority:** Medium  
**Severity:** Medium  
**Status:** Ready to DEV  
**Reporter:** Iveta Kuklová  
**Date Reported:** July 9, 2025  
**Environment:** Development  

---

## 📋 Summary

Bank accounts can be deleted via API even when the account balance is not zero, violating the documented business rule that requires accounts to have zero balance before deletion. This could lead to data integrity issues and potential financial discrepancies.

---

## 🔍 Description

### Issue Details

According to the API documentation, a bank account can only be deleted if its balance is 0. However, when testing the `DELETE /api/v1/bank-account/{id}` endpoint in Postman, I discovered that accounts with non-zero balances can be successfully deleted.

**API Documentation States:**
> "Bank account can only be deleted if balance is 0"

**Actual Behavior:**
The API accepts deletion requests and successfully removes accounts regardless of their balance, including accounts with positive balances.

### Why This Matters

In a banking application (even educational), this violates fundamental business logic:
- Users could lose track of their money
- Account balance is "lost" when account is deleted
- Data integrity issues in financial records
- No warning or prevention mechanism

---

## 🌐 Environment

**Testing Environment:**
- Application: CzechiBank (educational banking application)
- Environment: Development  
- API Endpoint: `DELETE /api/v1/bank-account/{id}`
- Testing Tool: Postman
- Date: July 9, 2025

---

## 📝 Steps to Reproduce

### Prerequisites
1. Valid user account with API key
2. Bank account created with non-zero balance
3. Access to Postman or similar API testing tool

### Detailed Steps

**Setup:**
1. Login to application or use valid API key
2. Create a bank account via UI or API
3. Add funds to the account (ensure balance > 0)
4. Note the account ID and current balance

**Test Deletion:**
1. Open Postman
2. Create DELETE request: `https://[dev-environment]/api/v1/bank-account/{account-id}`
3. In Authorization section, add valid API key:
   - Type: API Key
   - Key: `x-api-key`
   - Value: `[your-valid-api-key]`
4. Click "Send"

**Verify Result:**
5. Check response status: `200 OK` (deletion successful)
6. Try to access the deleted account via GET request
7. Account no longer exists
8. **Balance is gone** - no transfer, no refund, just disappeared

---

## 🎯 Expected Behavior

### According to Documentation

**When attempting to delete an account with non-zero balance:**

**Response Status:** `400 Bad Request` or `422 Unprocessable Entity`

**Response Body:**
```json
{
  "success": false,
  "message": "Cannot delete account with non-zero balance",
  "error": {
    "code": "400",
    "message": "Account balance must be 0 before deletion. Current balance: 150 CZECHITOKEN"
  }
}
```

**Required Actions Before Deletion:**
1. User must transfer all funds out of the account
2. Account balance must be exactly 0
3. Only then should deletion be allowed

---

## 🐛 Actual Behavior

**Current System Response:**

**Status:** `200 OK`

**Response Body:**
```json
{
  "success": true,
  "message": "Bank account deleted successfully"
}
```

**What happens to the money:**
- Balance is not transferred anywhere
- Balance is not refunded to user
- Money simply disappears with the account
- No warning or confirmation about non-zero balance

### Test Example

**Test Account Details:**
- Account ID: `cmcvysmhs001z5u8k11bnhlwp`
- Balance: `150 CZECHITOKEN`
- Currency: CZECHITOKEN

**DELETE Request:**
```
DELETE https://[dev-environment]/api/v1/bank-account/cmcvysmhs001z5u8k11bnhlwp
Headers:
  x-api-key: [valid-api-key]

Response: 200 OK
Result: Account deleted, 150 CZECHITOKEN lost ⚠️
```

---

## 💥 Impact Assessment

### Business Logic Violation

**Severity: MEDIUM-HIGH**

This violates core banking principles:
1. Money should never disappear
2. Users should not lose funds accidentally
3. Accounts with money should be protected from deletion

### Real-World Scenarios

**Scenario 1: Accidental Deletion**
- User has multiple accounts
- Accidentally deletes wrong account
- Loses all money in that account
- No way to recover funds

**Scenario 2: UI/UX Issues**
- User expects deletion to fail if balance exists
- Clicks delete without checking balance
- Assumes system will warn them
- Money is lost

**Scenario 3: Data Integrity**
- Financial reports won't balance
- Transaction history references deleted accounts
- Audit trail is incomplete

### Potential Issues

| Issue | Impact |
|-------|--------|
| **User Experience** | Confusing, unexpected behavior |
| **Data Integrity** | Lost money, broken references |
| **Business Logic** | Violates documented rules |
| **User Trust** | Users lose confidence in system |
| **Financial Records** | Incomplete audit trail |

---

## 🔧 Suggested Fix

Before deleting account, system should:Check account balance
Return informative error:"Cannot delete account with non-zero balance".


## 📊 API Documentation Reference

**From API Documentation:**

**Endpoint:** `DELETE /api/v1/bank-account/{id}`

**Description:** "Delete a bank account"

**Conditions:**
- ✅ User must be authenticated
- ✅ Account must belong to authenticated user
- ⚠️ **Account balance must be 0** ← NOT ENFORCED

**Current Gap:** Documentation states the rule, but API doesn't enforce it.
---

## ✅ Verification Steps

After fix is deployed:

**Test 1: Delete Account with Zero Balance**
```
Account balance: 0 CZECHITOKEN
DELETE /api/v1/bank-account/{id}
Expected: 200 OK, account deleted ✅
```

**Test 2: Delete Account with Positive Balance**
```
Account balance: 100 CZECHITOKEN
DELETE /api/v1/bank-account/{id}
Expected: 400 Bad Request, account NOT deleted ✅
Error message: "Cannot delete account with non-zero balance"
```

**Test 3: Delete Account with Negative Balance** (edge case)
```
Account balance: -50 CZECHITOKEN
DELETE /api/v1/bank-account/{id}
Expected: 400 Bad Request, account NOT deleted ✅
```

**Test 4: Transfer Funds Then Delete**
```
1. Account balance: 100 CZECHITOKEN
2. Transfer 100 CZECHITOKEN out
3. Account balance: 0 CZECHITOKEN
4. DELETE /api/v1/bank-account/{id}
Expected: 200 OK, account deleted ✅
```

**Test 5: API Documentation**
- Verify documentation matches implementation
- Confirm validation rules are clearly stated

---

## 💬 Communication History

**Initial Report:**
> "According to API documentation, bank accounts should only be deletable when balance is 0. However, I can successfully delete accounts with non-zero balances via API. This violates the documented business rule and could cause data integrity issues."

**Developer Response:**
> "Bug approved. This is bug in application, we should fix it. Thanks for reporting it!"

---

## 📚 Testing Notes

### What Led to Discovery

**Context:** Testing DELETE endpoint systematically

**My Testing Approach:**
1. Test happy path: Create account → Add funds → Delete
2. Expected API to reject deletion due to balance
3. API accepted deletion → discovered bug
4. Checked API documentation → confirmed it's a violation

**Testing Technique:** Boundary value analysis
- Testing with balance = 0 (should work)
- Testing with balance > 0 (should fail, but doesn't)

### Why I Reported This

Even though this is an educational project, this bug demonstrates:
- Understanding of business logic requirements
- Ability to compare documentation vs. implementation
- Recognition of data integrity issues
- Financial domain knowledge

**In a real banking application, this would be CRITICAL severity.**

---

## 🎯 Lessons Learned

### Business Logic Testing

**Key Insights:**
1. Always test against documented requirements
2. Don't assume validation exists just because documentation mentions it
3. Think about edge cases: What happens to the money?
4. Consider real-world impact even in test environments

### Documentation vs. Implementation

**Testing Principle:**
> "Documentation describes what SHOULD happen. Testing verifies what ACTUALLY happens."

This bug is a perfect example where:
- Documentation says one thing
- Implementation does something else
- Testing reveals the gap

---

## 🏷️ Labels

`business-logic` `validation` `data-integrity` `api-testing` `postman` `documentation-gap`

---

*Bug report created: July 9, 2025*  
*Last updated: October 2025* 
