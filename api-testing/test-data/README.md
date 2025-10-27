# 📊 Test Data

## Overview

This folder contains **CSV files with structured test scenarios** I used for systematic API testing. Each file represents different test flows and validation scenarios, organized by endpoint or feature area.

Using CSV files for test data demonstrates:
- **Repeatable testing** - Same scenarios can be run consistently
- **Data-driven approach** - Separating test logic from test data
- **Clear documentation** - Easy to see what was tested at a glance
- **Scalability** - Easy to add new scenarios without changing code

---

## 📁 Files Overview

### **1. transactions_scenarios.csv**
**Purpose:** Test scenarios for transaction operations (GET /transactions/{id})

**What it tests:**
- ✅ Happy path with valid authentication
- ⚠️ Authorization failures (missing, invalid, expired, deleted API keys)
- 🔍 Edge case: Non-existent transaction ID

**Structure:**
```csv
scenario, api_key, tx_id, expected_status
```

**Example scenarios:**
- `happy_path` - Valid key + valid ID → expects 200
- `unauthorized_missing_key` - No key → expects 401
- `unauthorized_deleted_key` - Deleted key → expects 401 (but returns 500! Bug CZBANK-71)
- `not_found` - Invalid ID → expects 404

---

### **2. transaction_flow_scenarios.csv**
**Purpose:** End-to-end transaction flow testing (POST + GET)

**What it tests:**
- ✅ Complete transaction lifecycle (create → retrieve)
- 💰 Amount validation (zero, negative, too large, non-numeric)
- 🏦 Account number validation (missing, invalid format, non-existent)
- 🔒 Authorization checks on both operations

**Structure:**
```csv
scenario, api_key, toBankNumber, amount, expected_post, expected_get
```

**Key scenarios:**
- `happy_path` - Valid transaction creation and retrieval
- `amount_zero` - Should reject (422) but needs validation
- `amount_negative` - Business rule: cannot transfer negative amount
- `amount_too_large` - Testing upper boundary
- `invalid_account_format` - Format validation (expects account/bank code)
- `account_not_found` - Non-existent recipient (404)

**Discoveries:**
- Found inconsistent validation on amount field
- Discovered missing checks for self-transfer (CZBANK-61)

---

### **3. bank_account_flow_scenarios.csv**
**Purpose:** End-to-end bank account lifecycle testing

**What it tests:**
- ✅ Full CRUD flow: Create → Get → Delete
- 🔒 Authorization on all operations
- ⚠️ Business logic validation

**Structure:**
```csv
scenario, api_key, expected_post, expected_get, expected_delete
```

**Key findings:**
- `happy_path` - All operations succeed with valid auth
- `unauthorized_missing_key` - Properly blocked (401) on all operations
- **Critical Bug (CZBANK-47):** DELETE succeeds even with non-zero balance

---

### **4. user_auth_scenarios.csv**
**Purpose:** API key authentication testing across endpoints

**What it tests:**
- 🔑 Valid API key behavior
- ⚠️ Missing API key
- 🚫 Invalid/malformed API key
- ⏰ Expired API key
- 🗑️ Deleted API key

**Structure:**
```csv
target, api_key, tx_id, account_id, expected_status
```

**Testing approach:**
Used the same authentication scenarios across different endpoints to verify:
- Consistent error handling
- Proper security implementation
- Correct HTTP status codes

**Findings:**
- Most endpoints return 401 for auth failures ✅
- Exception: Deleted key on GET /transactions returns 500 ❌ (CZBANK-71)
- API key structure issue (CZBANK-57): Key shares prefix with user ID

---

## 🧪 Testing Methodology

### **Equivalence Partitioning**
I grouped inputs into classes:

**API Key States:**
- Valid & active
- Missing
- Invalid format
- Expired
- Deleted

**Amount Values:**
- Positive valid amounts (1-999999)
- Zero
- Negative
- Non-numeric
- Extremely large

**Account Numbers:**
- Valid format (number/bank-code)
- Missing
- Invalid format
- Non-existent

### **Boundary Value Analysis**
Tested edge values:
- **Amount:** 0, 1, -1, 999999999
- **String lengths:** Empty, very long
- **IDs:** Valid, 9999 (non-existent), empty

---

## 📋 How to Use These Files

### **For Manual Testing:**
1. Open CSV in spreadsheet software
2. Pick a scenario row
3. Use the provided data in Postman/API client
4. Verify actual status matches expected

---

## 📊 Test Coverage Summary

| File | Scenarios | Areas Covered | Bugs Found |
|------|-----------|---------------|------------|
| transactions_scenarios.csv | 6 | Authorization, ID validation | 1 |
| transaction_flow_scenarios.csv | 11 | E2E flow, amount validation | 2 |
| bank_account_flow_scenarios.csv | 2 | CRUD lifecycle | 1 |
| user_auth_scenarios.csv | 12 | Cross-endpoint auth | 2 |
| **Total** | **31+** | **Multi-layer testing** | **6+** |

---

## 🐛 Bugs Discovered Through These Scenarios

### **High Priority**

**CZBANK-71: Deleted API Key Returns 500**
- **Scenario:** `unauthorized_deleted_key`
- **Expected:** 401 Unauthorized
- **Actual:** 500 Internal Server Error
- **Impact:** Poor error handling, confusing for API consumers

**CZBANK-47: Account Deletion Ignores Balance**
- **Scenario:** Testing DELETE on `bank_account_flow_scenarios.csv`
- **Expected:** 422/403 when balance ≠ 0
- **Actual:** 200 (successful deletion)
- **Impact:** Potential data loss, business logic violation

### **Medium Priority**

**CZBANK-61: Self-Transfer Allowed**
- **Scenario:** Testing with same account as sender/recipient
- **Expected:** 422 (cannot transfer to self)
- **Actual:** 200 (transfer succeeds)
- **Impact:** Pointless transactions, confusing transaction history

**CZBANK-57: API Key Security Issue**
- **Discovered during:** API key scenario testing
- **Issue:** API key shares prefix with user ID
- **Impact:** Security concern - reduces randomness of key

---

## 💡 What This Demonstrates

This test data structure showcases:

- ✅ **Systematic approach** - Organized scenarios by feature and flow
- ✅ **Comprehensive coverage** - Happy paths, negative cases, edge cases
- ✅ **Security focus** - Extensive authentication testing
- ✅ **Real bug discovery** - Data-driven testing revealed actual issues
- ✅ **Professional documentation** - Clear structure for repeatability
- ✅ **Scalable methodology** - Easy to extend with new scenarios

---

## 🔗 Related Resources

- [Postman Collection](../postman/) - API requests using this data
- [Bug Reports](../../bug-reports/) - Detailed reports of issues found
- [Test Cases](../../test-cases/) - Formal test case documentation

---

## 📝 Notes

### **API Key Values**
For security, actual API keys in these files are from a development environment and are no longer active. In a real testing scenario, you would:
1. Generate fresh API keys for your test users
2. Replace placeholder keys in CSV files
3. Never commit real production keys to version control

### **Test Data Maintenance**
- Update CSV files when API requirements change
- Add scenarios for new edge cases discovered
- Keep expected values synchronized with API documentation

### **Future Enhancements**
- Add more boundary value scenarios
- Include performance testing data (large datasets)
- Create negative scenarios for all new endpoints

