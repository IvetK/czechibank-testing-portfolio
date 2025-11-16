# CzechiBank API Test Cases

> **Team Collaboration Project**  
> These test cases were created collaboratively by the QA team to ensure comprehensive API coverage:
> - **Iveta Kuklová (IK)** - Users, API Keys, and Transactions endpoints  
> - **QA Team Colleague (DD)** - Bank Accounts endpoints and general API validation
> 
> **Note on Test Management:**  
> Detailed test cases with full test steps, preconditions, and execution history are maintained in **Jira Zephyr Test Management**. This document provides a comprehensive overview of test coverage for portfolio demonstration and quick reference purposes.

---

## 📊 Test Coverage Overview

| Category | Endpoint | Test Cases | Tester |
|----------|----------|------------|--------|
| **About** | GET /about | 1 | DD |
| **Users** | POST /user/create | 9 | IK |
| | GET /user | 5 | IK |
| **API Keys** | GET /apikey | 7 | IK |
| **Bank Accounts** | GET /bank-account/{id} | 4 | DD |
| | DELETE /bank-account/{id} | 3 | DD |
| | POST /bank-account/create | 5 | DD |
| | GET /bank-account/get-all | 5 | DD |
| | GET /bank-account | 5 | DD |
| **Transactions** | GET /transactions/{id} | 6 | IK |
| | POST /transactions/create | 17 | IK |
| | GET /transactions | 16 | IK |
| **TOTAL** | **12 endpoints** | **84** | **IK: 43, DD: 41** |

**Test Data:** Detailed test data sets available in `/test-data/` folder  
**Testing Period:** June - October 2025

---

## 1. About Endpoint

### GET /about
**Tester:** DD | **Purpose:** API health check and version information

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-001 | Get About Information | Verify API returns basic information without authentication | No authentication required | Medium | 200 OK, API version and info |

---

## 2. Users - Registration

### POST /user/create
**Tester:** IK | **Purpose:** User registration with validation

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-002 | User Registration - Happy Path | Register new user with all valid data | Valid email, password (6+ chars), name, gender | High | 201 Created, user object with ID and API key |
| TC-API-003 | Registration with Existing Email | Attempt to register with duplicate email | Email already in database | High | 400 Bad Request, error message |
| TC-API-004 | Registration with Invalid Email Format | Submit invalid email formats (no @, no domain) | Email without @ or domain | High | 400 Bad Request, validation error |
| TC-API-005 | Registration with Short Password | Test minimum password length validation | Password < 6 characters | High | 400 Bad Request, password length error |
| TC-API-006 | Registration without Full Name | Submit registration without required name field | Empty name field | Medium | 400 Bad Request, missing field error |
| TC-API-007 | Registration without Gender | Submit registration without required gender field | Empty gender field | Medium | 400 Bad Request, missing field error |
| TC-API-008 | Registration - Missing Email Field | Verify validation when email field is missing | Request body without email | Medium | 400 Bad Request, missing field error |
| TC-API-009 | Registration - Missing Password Field | Verify validation when password field is missing | Request body without password | Medium | 400 Bad Request, missing field error |

---

## 3. Users - Get User Data

### GET /user
**Tester:** IK | **Purpose:** Retrieve authenticated user information

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-010 | Get User - Authorized | Retrieve user data with valid authentication | Valid active API key | High | 200 OK, complete user object |
| TC-API-011 | Get User - Expired API Key | Verify expired API key is rejected | Expired API key in header | High | 401 Unauthorized, error message |
| TC-API-012 | Get User - Deleted API Key | Verify deleted API key cannot access resources | Previously deleted API key | High | 401 Unauthorized, error message |
| TC-API-013 | Get User - Invalid API Key Format | Test with truncated API key string | Incomplete or malformed API key | Medium | 401 Unauthorized, error message |
| TC-API-014 | Get User - Missing API Key | Request without authentication header | No API key header present | High | 401 Unauthorized, error message |

---

## 4. API Keys Management

### GET /apikey
**Tester:** IK | **Purpose:** List all API keys for authenticated user

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-015 | Get API Keys - Authorized | Retrieve list of all keys for current user | Valid active API key | High | 200 OK, array of user's API keys |
| TC-API-016 | Get API Keys - Expired API Key | Verify expired key cannot list keys | Expired API key in header | High | 401 Unauthorized, error message |
| TC-API-017 | Get API Keys - Deleted API Key | Verify deleted key cannot access endpoint | Previously deleted API key | High | 401 Unauthorized, error message |
| TC-API-018 | Get API Keys - Invalid Format | Test with invalid API key format | Truncated or malformed key | Medium | 401 Unauthorized, error message |
| TC-API-019 | Get API Keys - Missing API Key | Request without API key header | No authentication header | High | 401 Unauthorized, error message |

---

## 5. Bank Accounts - Get by ID

### GET /bank-account/{id}
**Tester:** DD | **Purpose:** Retrieve specific bank account details

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-020 | Get Bank Account by ID - Authorized | Retrieve bank account with valid credentials | Valid API key, existing account ID | High | 200 OK, bank account object |
| TC-API-021 | Get Bank Account - Unauthorized | Attempt to access without authentication | No API key header | High | 401 Unauthorized, error message |
| TC-API-022 | Get Bank Account - Invalid API Key | Test with invalid API key format | Truncated or malformed key | Medium | 401 Unauthorized, error message |
| TC-API-023 | Get Bank Account - Not Found | Request account that doesn't exist | Valid API key, non-existent ID | Medium | 404 Not Found, error message |

---

## 6. Bank Accounts - Delete Account

### DELETE /bank-account/{id}
**Tester:** DD | **Purpose:** Delete bank account with balance validation

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-024 | Delete Bank Account - Not Found | Attempt to delete non-existent account | Valid API key, non-existent ID | Medium | 404 Not Found, error message |
| TC-API-025 | Delete Bank Account - Unauthorized | Attempt deletion without authentication | No API key header | High | 401 Unauthorized, error message |
| TC-API-026 | Delete Bank Account - Invalid Key | Test with invalid API key format | Truncated or malformed key | Medium | 401 Unauthorized, error message |

---

## 7. Bank Accounts - Create New Account

### POST /bank-account/create
**Tester:** DD | **Purpose:** Create new bank account for user

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-027 | Create Bank Account - Happy Path | Create account with valid data | Valid API key, valid currency | High | 201 Created, new account object |
| TC-API-028 | Create Bank Account - Invalid Input | Submit invalid JSON structure | Malformed request body | Medium | 400 Bad Request, validation error |
| TC-API-029 | Create Bank Account - Empty Currency | Test currency field validation | Currency field empty string | Medium | 400 Bad Request, validation error |
| TC-API-030 | Create Bank Account - Missing Currency | Verify required field validation | No currency field in request | Medium | 400 Bad Request, missing field error |
| TC-API-031 | Create Bank Account - Unauthorized | Attempt creation without authentication | No API key header | High | 401 Unauthorized, error message |

---

## 8. Bank Accounts - Get All Accounts

### GET /bank-account/get-all
**Tester:** DD | **Purpose:** Retrieve all bank accounts with pagination

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-032 | Get All Bank Accounts | Retrieve complete list of accounts | Valid API key | High | 200 OK, array of all accounts |
| TC-API-033 | Get All Accounts - Pagination | Test pagination with limit and offset | Valid API key, page parameters | Medium | 200 OK, paginated results |
| TC-API-034 | Get All Accounts - Invalid Pagination | Submit negative or non-numeric pagination | Invalid page/limit values | Low | 400 Bad Request, validation error |
| TC-API-035 | Get All Accounts - Unauthorized | Request without authentication | No API key header | High | 401 Unauthorized, error message |
| TC-API-036 | Get All Accounts - Invalid Key | Test with invalid API key format | Truncated or malformed key | Medium | 401 Unauthorized, error message |

---

## 9. Bank Accounts - Get User's Accounts

### GET /bank-account
**Tester:** DD | **Purpose:** Retrieve authenticated user's bank accounts

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-037 | Get User Bank Accounts | Display all accounts for current user | Valid API key | High | 200 OK, array of user's accounts |
| TC-API-038 | Get User Accounts - Pagination | Test pagination functionality | Valid API key, page parameters | Medium | 200 OK, paginated results |
| TC-API-039 | Get User Accounts - Invalid Pagination | Submit invalid pagination parameters | Invalid page/limit values | Low | 400 Bad Request, validation error |
| TC-API-040 | Get User Accounts - Unauthorized | Request without authentication | No API key header | High | 401 Unauthorized, error message |
| TC-API-041 | Get User Accounts - Invalid Key | Test with invalid API key format | Truncated or malformed key | Medium | 401 Unauthorized, error message |

---

## 10. Transactions - Get by ID

### GET /transactions/{id}
**Tester:** IK | **Purpose:** Retrieve specific transaction details  
**Test Data:** See `/test-data/transactions_id_scenarios.csv`

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-042 | Get Transaction by ID - Happy Path | Retrieve transaction with valid authentication | Valid API key, existing transaction ID | High | 200 OK, transaction object |
| TC-API-043 | Get Transaction - Missing API Key | Attempt to access without API key | No authentication header | High | 401 Unauthorized, error message |
| TC-API-044 | Get Transaction - Expired API Key | Verify expired key is rejected | Expired API key in header | High | 401 Unauthorized, error message |
| TC-API-045 | Get Transaction - Deleted API Key | Verify deleted key handling | Previously deleted API key | High | 401 Unauthorized or 500 error |
| TC-API-046 | Get Transaction - Invalid Key Format | Test with incomplete API key | Truncated or malformed key | Medium | 401 Unauthorized, error message |
| TC-API-047 | Get Transaction - Not Found | Request transaction that doesn't exist | Valid API key, non-existent ID | Medium | 404 Not Found, error message |

---

## 11. Transactions - Create Transaction

### POST /transactions/create
**Tester:** IK | **Purpose:** Create money transfer between accounts  
**Test Data:** See `/test-data/Transaction_Flow_POSTGETDEL.csv`

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-048 | Create Transaction - Happy Path | Successful money transfer | Valid API key, valid account, amount 100 | High | 201 Created, transaction object |
| TC-API-049 | Create Transaction - Missing API Key | Request without authentication | No authentication header | High | 401 Unauthorized, error message |
| TC-API-050 | Create Transaction - Invalid API Key | Test with invalid key format | Truncated or malformed key | Medium | 401 Unauthorized, error message |
| TC-API-051 | Create Transaction - Negative Amount | Reject negative transaction amounts | amount: -50 | High | 422 Unprocessable Entity, validation error |
| TC-API-052 | Create Transaction - Zero Amount | Reject zero-value transactions | amount: 0 | High | 422 Unprocessable Entity, validation error |
| TC-API-053 | Create Transaction - Non-numeric Amount | Validate amount data type | amount: "abc" | Medium | 422 Unprocessable Entity, validation error |
| TC-API-054 | Create Transaction - Missing Amount Value | Test required field validation | amount: "" (empty) | Medium | 422 Unprocessable Entity, missing value error |
| TC-API-055 | Create Transaction - Missing Amount Field | Verify field presence validation | Request without amount field | Medium | 422 Unprocessable Entity, missing field error |
| TC-API-056 | Create Transaction - Missing Account Value | Test required account number | toBankNumber: "" (empty) | High | 422 Unprocessable Entity, missing value error |
| TC-API-057 | Create Transaction - Missing Account Field | Verify account field requirement | Request without toBankNumber | High | 422 Unprocessable Entity, missing field error |
| TC-API-058 | Create Transaction - Short Account Number | Test account number format validation | toBankNumber: "123" | Medium | 422 Unprocessable Entity, validation error |
| TC-API-059 | Create Transaction - Non-existent Account | Verify target account existence check | toBankNumber: "999999999" | High | 404 Not Found, account not found error |
| TC-API-060 | Create Transaction - Invalid Account Format | Test account number format requirements | toBankNumber: "ACC-XYZ" | Medium | 422 Unprocessable Entity, format error |
---

## 12. Transactions - Get All Transactions

### GET /transactions
**Tester:** IK | **Purpose:** Retrieve user's transaction history  
**Test Data:** See `/test-data/GETuser_transactions_bank_accounts.csv`

| TC ID | Test Case Name | Test Scenario | Test Data | Priority | Expected Result |
|-------|----------------|---------------|-----------|----------|-----------------|
| TC-API-061 | Get Transactions - Happy Path | Retrieve transaction history | Valid API key | High | 200 OK, array of transactions |
| TC-API-062 | Get Transactions - Missing API Key | Request without authentication | No authentication header | High | 401 Unauthorized, error message |
| TC-API-063 | Get Transactions - Deleted API Key | Deleted key handling | Previously deleted API key | High | 401 Unauthorized or 500 error |
| TC-API-064 | Get Transactions - Expired API Key | Verify expired key rejection | Expired API key in header | High | 401 Unauthorized, error message |
| TC-API-065 | Get Transactions - Invalid Key Format | Test with incomplete API key | Truncated or malformed key | Medium | 401 Unauthorized, error message |
| TC-API-066 | Get Transactions - Invalid Parameters | Test parameter validation | Invalid query parameters | Low | 400 Bad Request, validation error |
| TC-API-067 | Get Transactions - Pagination | Verify pagination functionality | Valid API key, page/limit params | Medium | 200 OK, paginated results |

---

## 📈 Testing Methodology

### Test Approach
- **Systematic Coverage:** Each endpoint tested for happy path, authentication, validation, and error handling
- **Data-Driven Testing:** CSV files with test scenarios for comprehensive coverage
- **Boundary Value Analysis:** Testing edge cases (min/max amounts, invalid formats)
- **Equivalence Partitioning:** Grouping similar test scenarios for efficient coverage
- **Security Focus:** Extensive authentication and authorization testing

### Test Data Organization
Detailed test scenarios with specific test data are maintained in CSV files:
- `transactions_id_scenarios.csv` - GET /transactions/{id} test data
- `Transaction_Flow_POSTGETDEL.csv` - POST /transactions/create test flows
- `GETuser_transactions_bank_accounts.csv` - GET /transactions test data
- `transactions_idbankaccounts_id_scenarios.csv` - Cross-endpoint authentication tests

---

## 🔗 Related Documentation

- **Bug Reports:** `/bug-reports/` - Detailed bug documentation
- **Case Studies:** `/case-studies/` - In-depth analysis of key findings
- **Test Data:** `/test-data/` - CSV files with test scenarios
- **Postman Collection:** `CzechiBank_postman_collection.json` - API testing collection

---

## 📝 Notes

- **Test Management:** Full test cases with detailed steps stored in Jira Zephyr
- **Execution Period:** June - October 2025
- **Test Environment:** Development environment
- **Base URL:** `https://[dev-environment]/api/v1/`
- **Authentication:** API key in request header
- **Team Size:** 2 QA testers
- **Coverage:** 67 test cases across 12 API endpoints

---

*Last Updated: November 2025*  
*Project: CzechiBank Testing Portfolio*
