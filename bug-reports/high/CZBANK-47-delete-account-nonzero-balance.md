# [CZBANK-47] Delete Bank Account with Non-Zero Balance

**Priority:** Medium  
**Severity:** Medium  
**Status:** Ready to DEV  
**Reporter:** Iveta Kuklová  
**Date Reported:** July 9, 2025

---

## 📋 Summary

Bank accounts can be deleted via API even when the balance is not zero, violating the documented business rule. The API documentation states that accounts can only be deleted if balance is 0, but the endpoint accepts deletion requests regardless of balance.

---

## 🌐 Environment

- **Application:** CzechiBank (Development)
- **Endpoint:** `DELETE /api/v1/bank-account/{id}`
- **Testing Tool:** Postman
- **Date:** July 9, 2025

---

## 🔍 Description

According to the API documentation, a bank account can only be deleted if its balance is 0. However, testing shows that the `DELETE /api/v1/bank-account/{id}` endpoint successfully deletes accounts with non-zero balances.

**API Documentation States:**
> "Bank account can only be deleted if balance is 0"

**Actual Behavior:**
The API accepts deletion requests and removes accounts regardless of balance, including accounts with positive balances. When an account with a non-zero balance is deleted, the balance disappears without transfer or refund.

---

## 📝 Steps to Reproduce

### Prerequisites
- Valid user account with API key
- Bank account with non-zero balance

### Steps

1. Create a bank account via API or UI
2. Add funds to the account (balance > 0)
3. Note the account ID and balance
4. In Postman, create DELETE request: `DELETE https://[dev-environment]/api/v1/bank-account/{account-id}`
5. Add authorization header:
   - Key: `x-api-key`
   - Value: `[valid-api-key]`
6. Send the request

### Result
- Response: `200 OK`
- Account is deleted successfully
- Balance is lost (not transferred anywhere)

---

## 🎯 Expected Behavior

**Response Status:** `400 Bad Request` or `422 Unprocessable Entity`

**Response Body:**
```json
{
  "success": false,
  "message": "Cannot delete account with non-zero balance",
  "error": {
    "code": "INVALID_BALANCE",
    "details": "Account balance must be 0 before deletion. Current balance: 150 CZECHITOKEN"
  }
}
```

**Expected Flow:**
1. API validates account balance before deletion
2. If balance ≠ 0, return error response
3. Account remains intact
4. User must transfer funds out before deletion

---

## 🐛 Actual Behavior

**Response Status:** `200 OK`

**Response Body:**
```json
{
  "success": true,
  "message": "Bank account deleted successfully"
}
```

**What Happens:**
- Account is deleted regardless of balance
- No validation error is returned
- Balance disappears without warning

### Example Test Case

**Test Data:**
- Account ID: `cmcvysmhs001z5u8k11bnhlwp`
- Balance: `150 CZECHITOKEN`

**Request:**
```
DELETE https://[dev-environment]/api/v1/bank-account/cmcvysmhs001z5u8k11bnhlwp
Headers:
  x-api-key: [valid-api-key]
```

**Result:**
- Response: `200 OK`
- Account deleted
- 150 CZECHITOKEN lost

---

## 💥 Impact

- **Business Logic Violation:** Contradicts documented API behavior
- **Data Integrity:** Account balance is lost without proper handling
- **User Experience:** Unexpected behavior, potential accidental fund loss

---

## 🔧 Suggested Fix

Add balance validation in the DELETE endpoint:

1. Check if `account.balance !== 0` before deletion
2. If balance is non-zero, return `400` or `422` error with descriptive message
3. Only allow deletion when `balance === 0`
4. Update API response to include current balance in error message

---

## 📎 Additional Notes

**Discovery Method:** Systematic API endpoint testing  
**Testing Technique:** Boundary value analysis (testing with balance = 0, balance > 0, balance < 0)

---

*Bug reported: July 9, 2025*  
*Last updated: October 2025*
