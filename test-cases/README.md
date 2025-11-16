# Test Cases

## Overview

This section contains comprehensive API test case documentation for the CzechiBank project. The test cases were developed collaboratively by a QA team of 2 testers to ensure thorough coverage of all API endpoints.

**Quick Stats:**
- **Total Test Cases:** 67
- **API Endpoints Covered:** 12
- **Testing Period:** June - October 2025
- **Team:** Iveta Kuklová (IK) + QA Team Colleague (DD)

---

## 📄 Documents in This Section

### [API-TEST-CASES.md](./API-TEST-CASES.md)
Complete test case documentation for all 12 API endpoints, organized by functionality area:
- User management (Registration, Authentication, API Keys)
- Bank Account operations (Create, Read, Delete)
- Transaction processing (Create, Read, List All)

---

## ⚙️ Test Management

**Detailed Test Cases:**  
Complete test cases with full test steps, preconditions, test data, and execution history are maintained in **Jira Zephyr Test Management System**.

---

🗂️ Test Data
Detailed test scenarios with specific test data are available in CSV files:
Location: /test-data/ folder
Files:

Transaction_Flow_POSTGETDEL.csv - POST /transactions/create validation tests
transactions_idbankaccounts_id_scenarios.csv - Cross-endpoint authentication testing
GET__Apikey_User_Bank_Account_Scenarios.csv - Multi-endpoint authentication (apikey, user, bank-account)

**Purpose:** CSV files enable data-driven testing approach, allowing execution of multiple test scenarios with different input combinations without duplicating test case logic.

---

🧪 Testing Methodology
Approaches Used:

Systematic Coverage: Each endpoint tested for happy path, authentication, validation, and error handling
Data-Driven Testing: CSV files with test scenarios for repeatable execution
Input Validation Testing: Testing data types, required fields, format requirements, and minimum values
Business Logic Validation: Verifying business rules (sufficient balance, valid accounts, authorization)
Security Focus: Comprehensive authentication testing across all endpoints (expired, deleted, missing, invalid API keys)
---

🔗 Related Documentation

Bug Reports - Issues discovered during testing
Case Studies - In-depth analysis of key findings
Test Data - CSV files with test scenarios
Postman Collection - API testing collection

---

**Execution Notes:**
- All test cases designed for the development environment
- Authentication required for all endpoints except GET /about
- Test data includes both valid and invalid scenarios to verify proper error handling

---

## 👥 Team Collaboration

Test cases were created through collaborative effort:
- **Iveta Kuklová (IK):** Users, API Keys, and Transactions endpoints
- **QA Team Colleague (DD):** Bank Accounts endpoints and API validation

This collaborative approach ensured comprehensive coverage while preventing test duplication and enabling knowledge sharing across the team.

---
*Last Updated: November 2025*  
