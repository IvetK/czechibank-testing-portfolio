# 🔌 API Testing

## Overview

This section demonstrates my systematic approach to **REST API testing** for the CzechiBank application. I focused on validating API endpoints, testing authorization mechanisms, and ensuring proper error handling across all endpoints.

---

## 🎯 Testing Objectives

My API testing strategy focused on:

1. **Functional Validation** - Verifying that each endpoint performs its intended operation correctly
2. **Security Testing** - Ensuring proper authentication and authorization using API keys
3. **Error Handling** - Validating appropriate HTTP status codes for various scenarios
4. **Data Integrity** - Confirming accurate data creation, retrieval, and deletion
5. **Edge Cases** - Testing boundary values and invalid inputs

---

## 📊 API Endpoints Tested

### **Users**
- `POST /api/v1/users` - User registration
- `GET /api/v1/users` - Get user details
- `GET /api/v1/users/apikey` - Retrieve user's API key

### **Bank Accounts**
- `POST /api/v1/bank-accounts/create` - Create new bank account
- `GET /api/v1/bank-accounts` - Get all user's bank accounts
- `GET /api/v1/bank-accounts/{id}` - Get specific bank account details
- `DELETE /api/v1/bank-accounts/{id}` - Delete bank account
- `PATCH /api/v1/bank-accounts/{id}` - Rename bank account

### **Transactions**
- `POST /api/v1/transactions` - Create new transaction
- `GET /api/v1/transactions/{id}` - Get transaction by ID
- `GET /api/v1/transactions` - Get all user transactions (with pagination)

---

## 🧪 Testing Approach

### **1. Scenario-Based Testing**
I created comprehensive test scenarios covering:
- ✅ **Happy Path** - Valid data with proper authentication
- ⚠️ **Negative Cases** - Invalid inputs, missing required fields
- 🔒 **Authorization Failures** - Missing, invalid, expired, and deleted API keys
- 🔍 **Edge Cases** - Boundary values, special characters, data type mismatches

### **2. Test Data Organization**
All test scenarios are documented in CSV files with:
- **Scenario name** - Clear description of what's being tested
- **Input data** - API keys, IDs, amounts, account numbers
- **Expected results** - HTTP status codes for validation

### **3. Tools Used**
- **Postman** - API request execution and collection organization
- **CSV Files** - Structured test data for repeatable testing
- **Browser DevTools** - Network inspection and response analysis

---

## 📁 Structure

```
api-testing/
├── postman/
│   ├── CzechiBank_collection.json    # Complete Postman collection
│   └── README.md                      # How to use the collection
│
└── test-data/
    ├── transactions_scenarios.csv     # Transaction testing scenarios
    ├── bank_accounts_scenarios.csv    # Bank account scenarios
    ├── user_auth_scenarios.csv        # Authentication scenarios
    └── README.md                      # Test data documentation
```

---

## 🐛 Key Findings

Through systematic API testing, I discovered several critical issues:

### **Critical Findings**
- **API Key Security Issue** - API key contains partial user ID (CZBANK-57)
- **Error Code Inconsistency** - Deleted API key returns 500 instead of 401 (CZBANK-71)
- **Business Logic Flaw** - Bank account can be deleted with non-zero balance (CZBANK-47)

### **Authorization Gaps**
- Missing session validation on transaction creation
- Inconsistent error responses for unauthorized requests

### **Data Validation Issues**
- Insufficient input validation on transaction amounts
- Missing validation for duplicate account numbers

---

## 🚀 How to Use This Section

1. **Review the Postman collection** - See all tested endpoints and example requests
2. **Examine test data files** - Understand the scenarios I tested
3. **Check bug reports** - See how API issues were discovered and documented

---

## 📈 Test Coverage

| Area | Endpoints Tested | Scenarios Covered | Bugs Found |
|------|------------------|-------------------|------------|
| Authentication | 3 | 8 | 2 |
| Bank Accounts | 5 | 12 | 3 |
| Transactions | 3 | 15 | 4 |
| **Total** | **11** | **35+** | **9** |

---

## 💡 What This Demonstrates

This API testing section showcases:

- ✅ **Systematic approach** to REST API testing
- ✅ **Understanding of HTTP methods** and status codes
- ✅ **Security awareness** - Authentication and authorization testing
- ✅ **Attention to detail** - Comprehensive scenario coverage
- ✅ **Clear documentation** - Organized test data and findings
- ✅ **Tool proficiency** - Postman, CSV data management, API inspection

---

## 🔗 Related Sections

- [Bug Reports](../bug-reports/) - Detailed reports of API issues found
- [Test Cases](../test-cases/) - Formal test cases for API endpoints
- [Documentation](../documentation/) - Overall testing strategy

