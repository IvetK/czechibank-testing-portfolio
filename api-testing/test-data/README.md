# 📊 Test Data

## Overview

This folder contains **CSV files with structured test scenarios** for systematic API testing. Each file represents specific test flows organized by endpoint and validation type.

Using CSV files demonstrates:
- **Data-driven testing** - Separating test logic from test data
- **Repeatability** - Consistent test execution
- **Scalability** - Easy to add new scenarios
- **Clear documentation** - Immediate visibility of test coverage

---

## 📁 Files in This Folder

### **transactions_id_scenarios.csv**
Tests GET `/transactions/{id}` endpoint

**Coverage:** 6 scenarios
- ✅ Valid authentication + valid transaction ID
- ⚠️ Authorization failures (missing, expired, deleted, invalid API keys)
- 🔍 Non-existent transaction ID

**Key Finding:** Deleted API key returns 500 instead of 401 → **Bug CZBANK-71**

---

### **transaction_flow_scenarios.csv**
Tests complete transaction flow (POST → GET)

**Coverage:** 11 scenarios
- ✅ Happy path (create and retrieve transaction)
- 🔒 Authorization validation (missing, invalid keys)
- 💰 Amount validation (zero, negative, non-numeric, boundary values)
- 🏦 Account validation (missing, invalid format, non-existent)

**Key Findings:**
- Self-transfer allowed → **Bug CZBANK-61**
- Inconsistent amount validation

---

### **transactions_idbankaccounts_id_scenarios.csv**
Tests authentication consistency across multiple endpoints

**Coverage:** 12 scenarios (6 per endpoint)
- GET `/transactions/{id}` authentication scenarios
- GET `/bank-accounts/{id}` authentication scenarios

**Purpose:** Verify consistent error handling and security implementation across different API endpoints

---

## 🧪 Testing Approach

### Equivalence Partitioning
Grouped inputs into validation classes:
- **API Keys:** Valid, Missing, Invalid, Expired, Deleted
- **Amounts:** Positive, Zero, Negative, Non-numeric, Boundary values
- **Account Numbers:** Valid format, Missing, Invalid format, Non-existent

### Boundary Value Analysis
Tested edge cases:
- Amount: 0, 1, -50, 1000000000
- IDs: Valid vs. non-existent (9999)
- Account format: Empty, malformed, valid

---

## 📊 Test Coverage Summary

| File | Scenarios | Primary Focus | Bugs Found |
|------|-----------|---------------|------------|
| transactions_id_scenarios.csv | 6 | Authorization & ID validation | 1 |
| transaction_flow_scenarios.csv | 11 | E2E flow & input validation | 2 |
| transactions_idbankaccounts_id_scenarios.csv | 12 | Cross-endpoint auth consistency | 2 |
| **TOTAL** | **29** | **Multi-layer API testing** | **5+** |

---

## 💡 What This Demonstrates

- ✅ **Systematic testing** - Organized by feature and validation type
- ✅ **Comprehensive coverage** - Happy paths + negative cases + edge cases
- ✅ **Security focus** - Extensive authentication testing
- ✅ **Bug discovery** - Data-driven approach revealed real issues
- ✅ **Professional structure** - Clear, repeatable, and scalable

---

## 🔗 Related Documentation

- [Postman Collection](../postman/) - API requests using this test data
- [Bug Reports](../../bug-reports/) - Detailed bug documentation
- [Test Cases](../../test-cases/api-testing/) - Formal test case specifications

---

## 📝 Notes

**Security:** API keys in these files are from development environment and are no longer active. Never commit production keys to version control.

**Maintenance:** Update CSV files when API requirements change and add scenarios for newly discovered edge cases.
