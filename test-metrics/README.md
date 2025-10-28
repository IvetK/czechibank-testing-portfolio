# 📊 Test Metrics

## Overview

Quantitative analysis of CzechiBank testing project (July - September 2025).  
**All metrics based on verified data from actual testing activities.**

---

## Summary Dashboard

| Metric | Value |
|--------|-------|
| **Total Bugs Documented** | 14+ |
| **Bugs on GitHub** | 6 (3 High, 3 Medium) |
| **Test Cases Documented** | 50+ |
| **CSV Test Scenarios** | 30 |
| **API Endpoints Tested** | 11 |
| **Postman Requests** | 15 |
| **Testing Duration** | 12 weeks |
| **Browsers Tested** | Chrome, Safari |

**Note:** Test cases were made in collaboration with other QA tester in the team.

---

## Bugs by Severity

| Severity | Count | Status |
|----------|-------|--------|
| **High** | 3 | All confirmed by developers |
| **Medium** | 3 | All confirmed by developers |
| **Low** | 8+ | Documented |
| **Total** | **14+** | **All approved** |

**Note:** All bugs documented in this portfolio were confirmed and approved by the development team.

---

## Test Coverage

### API Testing

| Category | Endpoints | Scenarios (CSV) |
|----------|-----------|-----------------|
| **Users** | 3 | 6 |
| **Bank Accounts** | 5 | — |
| **Transactions** | 3 | 24 |
| **Total** | **11** | **30** |

**Coverage:** 100% of available API endpoints

---

### Functional Testing

| Area | Test Cases |
|------|------------|
| User Registration & Authentication | ✓ |
| Bank Account Management (CRUD) | ✓ |
| Money Transfers & Transactions | ✓ |
| API Key Lifecycle | ✓ |
| Profile Management | ✓ |
| UI/Responsive Design | ✓ |
| **Total Documented** | **50+** |

---

## Test Data & Tools

### CSV Test Scenarios

| File | Scenarios | Purpose |
|------|-----------|---------|
| `transactions_scenarios.csv` | 6 | Transaction validation + auth states |
| `transaction_flow_scenarios.csv` | 12 | E2E transaction testing |
| `user_auth_scenarios.csv` | 12 | Authentication state testing |
| **Total** | **30** | **Data-driven API testing** |

**Testing approach:**
- Happy path scenarios
- Authorization states (valid, invalid, missing, expired, deleted)
- Boundary values (0, negative, minimum, maximum)
- Invalid inputs and error handling

---

### Postman Collection

- **15 API requests** organized into 4 folders
- **Folders:** Users, Bank Accounts, Transactions, About
- **Format:** Importable JSON collection
- **Purpose:** API testing and documentation

---

## Test Environment

### Browsers

| Browser | Platform | Usage |
|---------|----------|-------|
| **Chrome** | macOS | Primary testing |
| **Safari** | macOS | Cross-browser validation |

### Devices

- **Desktop** (1920x1080) - Full functionality
- **Mobile** (iPhone) - Responsive design

---

## Testing Techniques

| Technique | Application |
|-----------|-------------|
| **Exploratory Testing** | Session management, UI flows |
| **Boundary Value Analysis** | Amount validation (0, -1, 1, max values) |
| **Equivalence Partitioning** | API key states, input ranges |
| **Negative Testing** | Invalid inputs, error scenarios |
| **Security Testing** | Authentication, sessions, API keys |
| **Data-Driven Testing** | CSV-based API scenarios |

---

## Key Findings

### High-Risk Areas Discovered

1. **Session Management** - Critical security vulnerability
2. **API Error Handling** - Inconsistent response codes
3. **Security Patterns** - Predictable API key generation

### Testing Effectiveness

- **Exploratory testing** discovered the most critical security bugs
- **Data-driven API testing** enabled systematic coverage
- **Boundary value analysis** revealed validation gaps

---

## Testing Period

**Duration:** July - September 2025 (12 weeks)  
**Project:** CzechiBank Educational Banking Application  
**Role:** Manual & API Testing

---

## Related Documentation

- **[Bug Reports](../bug-reports/)** - Detailed bug documentation by severity
- **[API Testing](../api-testing/)** - Postman collection and CSV scenarios
- **[Case Studies](../case-studies/)** - In-depth analysis of critical findings
- **[Test Cases](../test-cases/)** - Complete test case repository

---

## Contact

**Email:** iveta.charvatova@gmail.com  
**LinkedIn:** [linkedin.com/in/iveta-kuklová](https://www.linkedin.com/in/iveta-kuklová)  
**GitHub:** [github.com/IvetK](https://github.com/IvetK)

---

*Last Updated: October 2025*
