# Case Study: Data-Driven API Testing Implementation

**Type:** Testing Methodology  
**Tool:** Postman Collection Runner + CSV  
**Date:** July - August 2025  

## Executive Summary

Implemented a data-driven API testing framework using Postman Collection Runner with CSV test scenarios, automating 30+ test cases across multiple endpoints and reducing repetitive manual testing by 90%.

**Impact:** Faster test execution, better coverage, repeatable regression testing  
**Key Innovation:** Separating test logic from test data for scalable automation  
**Bugs Found:** Discovered CZBANK-71 and endpoint inconsistencies through systematic coverage

---

## The Challenge

**Initial manual testing workflow:**
1. Open Postman → Create request
2. Test with valid data → Check response
3. Manually change to invalid data → Check response
4. Manually change API key → Check response
5. Repeat for every scenario...

**Problems:**
- ⏰ 30 minutes per endpoint for 5-6 scenarios
- 🔄 Repetitive and error-prone
- 📝 No clear record of test coverage
- 🔁 Regression testing = doing everything manually again

**Breaking point:** When API Keys feature was added, I needed to test:
- 5 API key states (valid, missing, invalid, expired, deleted)
- Across 15+ endpoints
- With various request bodies

**That's 75+ manual test combinations!** 

I thought: *"There has to be a better way."*

---

## The Solution

**Data-driven testing approach:**

Instead of manually changing test data, I:
1. Defined test scenarios in CSV files (one row = one test case)
2. Created reusable Postman test scripts that read CSV data
3. Ran all scenarios automatically using Collection Runner

**Example CSV structure:**
```csv
scenario,api_key,tx_id,expected_status
happy_path,fAHL...,cmelbycsr0007,200
missing_key,,cmelbycsr0007,401
deleted_key,yaPSb...,cmelbycsr0007,500
invalid_tx_id,fAHL...,9999,404
```

Each row represents one automated test case.

---

## Implementation Overview

**Created 4 CSV data files:**

1. **transactions_id_scenarios.csv** (6 scenarios)  
   Tests: GET /transactions/{id} with different auth states

2. **Transaction_Flow_POSTGETDEL.csv** (11 scenarios)  
   Tests: POST /transactions validation (boundary values, missing fields, invalid formats)

3. **transactions_idbankaccounts_id_scenarios.csv** (12 scenarios)  
   Tests: Reusable scenarios across multiple endpoints

**Total coverage:** 30+ automated test scenarios

---

## Results and Impact

### Before vs After

| Metric | Manual Testing | Data-Driven Testing |
|--------|----------------|---------------------|
| Time per test run | ~2.5 hours | ~5 minutes |
| Test coverage | 25-30 scenarios (inconsistent) | 30+ scenarios (consistent) |
| Repeatability | Manual re-testing required | One-click re-run |
| Documentation | None | CSV files serve as test docs |

**Time saved:** 90% reduction in testing effort

### Bugs Discovered Through This Approach

**1. CZBANK-71: Deleted API Key Returns 500**
- CSV included systematic auth testing
- Automated test caught the 500 vs 401 error
- Would have been easy to miss in manual testing

**2. Inconsistent Error Handling**
- Some endpoints returned 422 for missing fields
- Others returned 500 for the same scenario
- Data-driven approach revealed this pattern across endpoints

**3. Boundary Value Gaps**
- Testing revealed missing test cases for decimal amounts
- Systematic CSV coverage exposed gaps in validation

---

## Key Insights

- **Efficiency mindset** - Recognized repetitive work and automated it
- **Scalability thinking** - Built solution that grows with the project (add new test = add CSV row)
- **Systematic approach** - Comprehensive coverage through equivalence partitioning in CSV
- **Automation fundamentals** - Understanding test automation concepts without full coding
- **Bug discovery** - Systematic testing finds patterns manual testing misses
- **Documentation value** - CSV files document test coverage for team review

**Most important lesson:** Investing 3 hours in framework setup saved 2+ hours on every regression cycle. Automation isn't just about speed - it's about consistency, repeatability, and better coverage.

---

**Related:** [API Testing Documentation](../api-testing/README.md) | [CSV Test Data Files](../api-testing/test-data/)

---

*Last updated: October 2025*
