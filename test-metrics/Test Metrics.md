# 📊 Test Metrics

## Executive Summary

**Testing Period:** June - October 2025 (20 weeks)  
**Project:** CzechiBank Educational Banking Application  
**Role:** Manual & API Tester  
**Team:** 2 QA Testers (IK + DD)

> Comprehensive API testing portfolio demonstrating systematic testing approach, security awareness, and data-driven testing methodology.

---

## 📈 Quick Stats Dashboard

| Metric | Individual Work | Team Collaboration |
|--------|-----------------|-------------------|
| **Bugs Reported** | 13 (all approved) | — |
| **Security Bugs** | 2 (High priority) | — |
| **API Endpoints Tested** | 12 (full coverage) | — |
| **Test Scenarios (CSV)** | 29 (data-driven) | — |
| **Postman Requests** | 15 (automated) | — |
| **Test Cases Documented** | ~35 (API & Transactions) | 67 total |
| **Testing Techniques** | 6 applied & proven | — |
| **Tools Mastered** | 5 (Postman, DevTools, Jira, CSV, Git) | — |

---

## 🎯 Individual Testing Contributions

*100% my independent work*

### Bug Discovery & Reporting

**Total Bugs Reported:** 13  
**Status:** All documented bugs were approved by developers  
**Average Developer Response Time:** 1-3 days

#### Bug Distribution by Priority

| Priority | Count | Key Examples |
|----------|-------|--------------|
| **High** | 2 | Session invalidation (CZBANK-69), API 500 error (CZBANK-71) |
| **Medium** | 4 | DELETE without balance check, duplicate email registration |
| **Low** | 6 | Mobile UI, timestamp, documentation gaps |
| **Lowest** | 1 | Generate avatar (fixed) |

#### Critical Findings

**🔴 CZBANK-69: Session Not Invalidated After Logout (High - Major)**
- **Discovery Method:** Exploratory testing with browser back button
- **Impact:** Users could execute transactions after logout
- **Type:** Critical security vulnerability
- **Status:** Fixed and verified

**🔴 CZBANK-71: GET Transactions Returns 500 Instead of 401 (High)**
- **Discovery Method:** Systematic API key state testing
- **Impact:** Improper error handling across all authenticated endpoints
- **Type:** API best practices violation
- **Status:** Approved for fix

**🟡 CZBANK-57: Predictable API Key Pattern (Low - Security)**
- **Discovery Method:** Pattern recognition across 6+ user registrations
- **Finding:** API keys share first half with User ID
- **Impact:** Reduced cryptographic randomness
- **Type:** Security design flaw

---

### API Testing Framework

*Complete API testing infrastructure - 100% individual work*

| Component | Count | Details |
|-----------|-------|---------|
| **Endpoints Tested** | 12 | Users (3), Bank Accounts (5), Transactions (3), About (1) |
| **Postman Requests** | 15 | Organized in 4 folders with automation |
| **CSV Test Scenarios** | 29 | Data-driven validation testing |
| **Bugs Found via API** | 5+ | Through systematic scenario testing |

#### Data-Driven Testing Results

| CSV File | Scenarios | Focus | Bugs |
|----------|-----------|-------|------|
| `Transaction_Flow_POSTGETDEL.csv` | 11 | E2E transaction validation | 2 |
| `transactions_idbankaccounts_id_scenarios.csv` | 12 | Cross-endpoint auth testing | 2 |
| `GET__Apikey_User_Bank_Account_Scenarios.csv` | 5 | Multi-endpoint authentication | 1 |
| **Total** | **29** | **Systematic API coverage** | **5+** |

**Scenario Coverage:**
- ✅ Happy path validation
- ✅ Authorization states: valid, invalid, missing, expired, deleted
- ✅ Boundary values: 0, negative, minimum, maximum
- ✅ Input validation: missing fields, invalid formats
- ✅ Error handling: HTTP status codes, error messages

---

### Testing Techniques Applied

**All techniques proven with project evidence:**

| Technique | Application | Evidence |
|-----------|-------------|----------|
| **Exploratory Testing** | Session management, UI flows | Found CZBANK-69 (critical security bug) |
| **Boundary Value Analysis** | Amount validation | CSV: 0, -50, 1, 1000000000 |
| **Equivalence Partitioning** | API key states | CSV: valid, invalid, missing, expired, deleted |
| **Negative Testing** | Invalid inputs | CSV: "abc" amounts, wrong formats |
| **Security Testing** | Auth & authorization | 2 security vulnerabilities found |
| **Data-Driven Testing** | CSV-based scenarios | 29 automated test scenarios |

---

## 👥 Team Collaboration

*Joint documentation efforts with QA colleague (DD)*

### Test Case Documentation

**Total Test Cases:** 67 (collaborative)  
**My Primary Areas:** API testing, Transactions (~35 test cases)  
**Colleague's Primary Areas:** Bank Accounts, UI (~32 test cases)

**Coverage Areas:**
- User Registration & Authentication (shared)
- API Key Lifecycle (IK primary)
- Bank Account Management (DD primary)
- Money Transfers & Transactions (IK primary)
- Profile Management (shared)
- UI/Responsive Design (DD primary)

**Collaboration Approach:**
- Independent testing for maximum coverage
- Consolidated documentation in shared Excel
- Cross-verification of critical functionality

---

## 🛠️ Tools & Environment

### Testing Stack

| Tool | Usage | Proficiency |
|------|-------|-------------|
| **Postman** | API testing, automation, Collection Runner | Advanced |
| **Browser DevTools** | Network analysis, debugging, cookie inspection | Intermediate |
| **Jira** | Bug tracking, developer communication | Proficient |
| **Excel/CSV** | Test management, data-driven testing | Advanced |
| **Git/GitHub** | Version control, documentation | Intermediate |

### Test Environment

- **Application:** CzechiBank (Development)
- **Browsers:** Safari (primary), Chrome (cross-browser)
- **Devices:** Desktop (1920x1080), Mobile (iPhone)
- **API:** REST API v1 with JSON responses

---

## 💡 Quality Impact

### Bug Detection Effectiveness

| Category | Bugs Found | Impact |
|----------|------------|--------|
| **Security** | 2 | Critical authentication vulnerabilities |
| **API** | 5 | Error handling, validation gaps |
| **Frontend** | 4 | UX improvements, responsive design |
| **Documentation** | 2 | API docs accuracy |

### Key Success Metrics

✅ **Developer Approval** - All 13 documented bugs confirmed by developers  
✅ **Critical Security Finding** - Session management vulnerability  
✅ **Systematic Coverage** - 12/12 API endpoints tested  
✅ **Data-Driven Approach** - 29 automated scenarios  
✅ **Fast Feedback Loop** - 1-3 day developer response average  

---

## 📚 Portfolio Documentation

### Related Materials

- **[Bug Reports](../bug-reports/)** - 13 detailed bug reports by severity
- **[API Testing](../api-testing/)** - Postman collection + CSV scenarios
- **[Test Cases](../test-cases/)** - 67 test cases (collaborative)
- **[Case Studies](../case-studies/)** - Deep-dive analysis of critical bugs
- **[Testing Strategy](../docs/test-strategy.md)** - Overall approach

---

## 📞 Contact

**Iveta Kuklová**  
**Email:** iveta.charvatova@gmail.com  
**LinkedIn:** [linkedin.com/in/iveta-kuklová](https://www.linkedin.com/in/iveta-kuklová)  
**GitHub:** [github.com/IvetK](https://github.com/IvetK)

---

## 📝 Methodology Notes

**Individual vs Team Metrics:**
- Individual metrics represent work where I was primary contributor
- Team metrics represent collaborative documentation
- All individual bugs verified through Jira reporter field
- API testing framework developed independently

**Data Authenticity:**
All metrics based on actual testing activities June-October 2025. Verifiable through project documentation, Jira tickets, and CSV test files.

---

*Last Updated: November 2025*  
*Testing Period: June - October 2025*  
