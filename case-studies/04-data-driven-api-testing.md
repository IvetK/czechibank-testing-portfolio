# Case Study: Data-Driven API Testing with Postman

**Achievement Type:** Testing Methodology Implementation  
**Technology:** Postman Collection Runner + CSV Data Files  
**Date:** July - August 2025  
**Status:** Implemented and Actively Used  

---

## 🎯 Executive Summary

Implemented a comprehensive data-driven API testing framework using Postman Collection Runner with CSV test scenarios. This approach enabled systematic testing of 30+ scenarios across multiple endpoints with automated assertions, significantly improving test coverage and efficiency while reducing manual testing effort.

**Key Results:**
- 📊 **30+ test scenarios** automated with data-driven approach
- 🔄 **4 CSV data files** covering different endpoint testing strategies
- ✅ **Automated assertions** for status code validation
- 🚀 **90% reduction** in repetitive manual testing
- 🐛 **Multiple bugs discovered** through systematic coverage

---

## 🔍 The Challenge

### Initial Testing Approach (Manual)

When I started testing the CzechiBank API, my workflow looked like this:

1. Open Postman
2. Create a request for `POST /transactions`
3. Test with valid data → Check response
4. Manually change to invalid data → Check response
5. Manually change to missing field → Check response
6. Manually change API key → Check response
7. Repeat for every scenario...

**Problems with this approach:**
- ⏰ **Time-consuming:** Each test required manual input changes
- 🔄 **Repetitive:** Same steps for different data combinations
- 🐛 **Error-prone:** Easy to miss edge cases or make typos
- 📝 **Hard to track:** No clear record of what was tested
- 🔁 **Not repeatable:** Regression testing meant doing everything again manually

### The Breaking Point

When the API Keys feature was merged into the application, I needed to test:
- 5 different API key states (valid, missing, invalid, expired, deleted)
- Across 15+ API endpoints
- With various request bodies and parameters

**That would be 75+ manual test combinations!** 

I realized: *"There has to be a better way."*

---

## 💡 The Solution: Data-Driven Testing

### What is Data-Driven Testing?

Instead of manually changing test data in Postman, I:
1. **Define test scenarios in CSV files** (one row per test case)
2. **Create reusable test scripts** that read data from CSV
3. **Run all scenarios automatically** using Collection Runner

**Example CSV:**
```csv
scenario,api_key,expected_status
happy_path,validkey123,200
missing_key,,401
invalid_key,fakekey,401
```

Postman reads each row and runs the request with that data!

---

## 🛠️ Implementation

### Step 1: CSV File Structure

I created structured CSV files with clear column names:

**transactions_id_scenarios.csv:**
```csv
scenario,api_key,tx_id,expected_status
happy_path,fAHLepbnGYW...,cmelbycsr0007ef4u,200
unauthorized_missing_key,,cmelbycsr0007ef4u,401
unauthorized_expired_key,TKLTTsIyTQA...,cmelbycsr0007ef4u,401
unauthorized_deleted_key,yaPSbxAGzUS...,cmelbycsr0007ef4u,401
unauthorized_invalid_key,invalidapi123,cmelbycsr0007ef4u,401
not_found,fAHLepbnGYW...,9999,404
```

Each row represents one test case with:
- **scenario:** Descriptive name for the test
- **api_key:** The API key to use (can be empty for missing key tests)
- **tx_id:** Transaction ID to test
- **expected_status:** What HTTP status code we expect

### Step 2: Postman Test Scripts

I created reusable test scripts that work with CSV data:
```javascript
// Pre-request Script
// Reads CSV data and sets up the request

const base = pm.environment.get("BASE_URL") || pm.collectionVariables.get("BASE_URL");
pm.variables.set("E_BASE", base);

// Get API key from CSV, trim whitespace
const rawKey = pm.iterationData.get("api_key");
const key = rawKey ? String(rawKey).trim() : "";

// Set or remove the x-api-key header
pm.request.headers.remove("Authorization");
if (!key) {
  pm.request.headers.upsert({ key: "x-api-key", value: "", disabled: true });
} else {
  pm.request.headers.upsert({ key: "x-api-key", value: key });
}

// Debug logging
console.log("ITER:", pm.info.iteration, "API Key:", JSON.stringify(key));
```
```javascript
// Test Script
// Validates the response against expected status from CSV

// Get expected status from CSV or default to 200
const expectedStatus = parseInt(
  pm.iterationData.get("expected_status") || "200", 
  10
);

// Assert that response status matches expected
pm.test(`Status == ${expectedStatus}`, () => {
  pm.response.to.have.status(expectedStatus);
});

// Sanity check that tests are running
pm.test("Tests are running", () => {
  pm.expect(true).to.be.true;
});
```

### Step 3: Collection Runner Setup

**How to run data-driven tests in Postman:**

1. Click **"Runner"** button in Postman
2. Select your collection
3. Click **"Select File"** next to "Data"
4. Upload your CSV file
5. Click **"Run"**

Postman automatically:
- Runs the request once for each CSV row
- Substitutes CSV values into the request
- Executes test assertions
- Shows results summary

---

## 📊 Test Coverage Achieved

### CSV Data Files Created

I created **4 CSV files** for different testing strategies:

#### **1. transactions_id_scenarios.csv** (6 scenarios)
Tests: `GET /transactions/{id}`

**Coverage:**
- ✅ Valid API key + valid transaction ID
- ❌ Missing API key
- ❌ Expired API key  
- ❌ Deleted API key
- ❌ Invalid API key
- ❌ Non-existent transaction ID

**Purpose:** Comprehensive authentication testing for transaction retrieval

---

#### **2. Transaction_Flow_POSTGETDEL.csv** (11 scenarios)
Tests: `POST /transactions` (create)

**Coverage:**
- ✅ Valid transaction (happy path)
- ❌ Unauthorized: missing/invalid API key
- ❌ Missing required field: toBankNumber
- ❌ Invalid account format
- ❌ Non-existent account
- ❌ Boundary values: amount = 0, negative, too large
- ❌ Invalid data type: amount = "abc"
- ✅ Minimum valid amount: amount = 1

**Purpose:** Comprehensive validation testing for transaction creation

**Example rows:**
```csv
scenario,api_key,toBankNumber,amount,expected_post,expected_get
happy_path,fAHL...,287982456568/5555,100,201,200
amount_zero,fAHL...,287982456568/5555,0,422,422
amount_negative,fAHL...,287982456568/5555,-50,422,422
missing_account,fAHL...,,100,422,422
```

---

#### **3. transactions_idbankaccounts_id_scenarios.csv** (12 scenarios)
Tests: Both `GET /transactions/{id}` AND `GET /bank-account/{id}`

**Coverage:**
- ✅ Valid requests for both endpoints
- ❌ Authorization failures for both endpoints
- ❌ Not found scenarios for both endpoints

**Purpose:** Reusable scenarios across multiple endpoints

**Smart design:** Uses a "target" column to specify which endpoint to test:
```csv
target,api_key,tx_id,account_id,expected_status
transactions_happy_path,validkey,txid123,,200
bankaccount_happy_path,validkey,,accountid456,200
```

---

#### **4. Scenario_BankAccount_Flow.csv** (2 scenarios)
Tests: Complete CRUD flow for bank accounts

**Coverage:**
- ✅ Full CRUD cycle: POST (create) → GET (read) → DELETE (remove)
- ❌ Unauthorized CRUD operations

**Purpose:** End-to-end workflow testing
```csv
scenario,api_key,expected_post,expected_get,expected_delete
happy_path,fAHL...,201,200,200
unauthorized_missing_key,,401,401,401
```

---

## 📈 Results and Impact

### Before Data-Driven Testing

**Manual approach statistics:**
- ⏰ Time per endpoint: ~30 minutes (testing 5-6 scenarios manually)
- 🔄 Endpoints tested: 5
- ⏱️ **Total time:** ~2.5 hours
- 📊 Scenarios covered: ~25-30 (but inconsistent)
- 🐛 Bugs found: Some, but might have missed edge cases

### After Data-Driven Testing

**Automated approach statistics:**
- ⏰ Time to run all tests: ~5 minutes (Collection Runner)
- 🔄 Endpoints covered: 8+
- ⏱️ **Total time:** ~5 minutes (after initial setup)
- 📊 Scenarios covered: 30+ (consistent, repeatable)
- 🐛 Bugs found: **More bugs discovered** through systematic coverage

### Key Benefits

**1. Speed**
- Manual testing: 2.5 hours
- Automated testing: 5 minutes
- **Time saved: 97%** 🚀

**2. Repeatability**
- Can re-run all tests after any code change
- Regression testing is now effortless
- Consistent coverage every time

**3. Better Coverage**
- Systematically test all edge cases
- No forgetting to test a scenario
- Easy to add new test cases (just add a CSV row)

**4. Documentation**
- CSV files serve as test documentation
- Clear record of what was tested
- Easy for others to understand test coverage

**5. Bug Discovery**
- Found the "500 vs 401" bug through systematic auth testing
- Discovered inconsistent error handling across endpoints
- Identified missing validation scenarios

---

## 🐛 Bugs Discovered Through This Approach

### 1. Deleted API Key Returns 500 (CZBANK-71)

**How data-driven testing helped:**
- CSV included "deleted_key" scenario
- Automated test caught the 500 error
- Would have been easy to miss in manual testing

### 2. Inconsistent Error Handling

**Discovery:**
```csv
scenario,toBankNumber,amount,expected
missing_field,,100,422
invalid_format,ABC-XYZ,100,422
```

Some missing fields returned 422, others returned 500. Data-driven approach revealed this inconsistency.

### 3. Boundary Value Issues

**CSV testing revealed:**
```csv
amount_zero,0,422  ✅ Works
amount_negative,-50,422  ✅ Works  
amount_decimal,10.5,???  ⚠️ No test case!
```

This showed we needed to add decimal amount testing.

---

## 📚 Lessons Learned

### What This Experience Taught Me

**1. Initial Setup Takes Time, But Pays Off**
- Spent ~3 hours setting up CSV files and scripts
- Saved 2+ hours on every regression test cycle
- Investment paid back after 2nd test run

**2. CSV Structure Matters**
- Clear column names make CSVs easy to understand
- Include "scenario" column for documentation
- Keep expected results in CSV, not hardcoded

**3. Start Simple, Then Expand**
- Started with 5 scenarios for one endpoint
- Gradually added more endpoints and scenarios
- Now have comprehensive coverage

**4. Automation Finds Edge Cases**
- Systematic testing reveals patterns
- Easier to spot inconsistencies across endpoints
- Forces you to think about all scenarios upfront

**5. Collaboration Benefit**
- CSV files can be reviewed by team members
- Non-technical people can suggest new scenarios
- Easy to share test coverage documentation

---

## 🔧 Technical Details

### Postman Collection Structure
```
CzechiBank Collection
│
├── GET_all_CSV (Data-driven folder)
│   ├── api-key (with test scripts)
│   ├── Transactions (with test scripts)
│   ├── User (with test scripts)
│   └── BankAccount (with test scripts)
│
└── Manual Testing Requests
    ├── POST Create Transaction
    ├── GET Transaction by ID
    └── ... (other endpoints)
```

### Environment Variables

**Used in all requests:**
- `{{BASE_URL}}` - API base URL
- `{{apikey}}` - Default API key for manual testing

**Dynamically set from CSV:**
- `api_key` - Per-scenario API key
- `tx_id` - Transaction ID to test
- `account_id` - Bank account ID
- `expected_status` - Expected HTTP status

---

## 🚀 Future Enhancements

### What I'd Like to Add

**1. Response Body Validation**
Currently testing only status codes. Could add:
```javascript
// Validate response structure
pm.test("Response has required fields", () => {
  pm.expect(pm.response.json()).to.have.property('success');
  pm.expect(pm.response.json()).to.have.property('data');
});

// Validate specific values
const expectedAmount = pm.iterationData.get("amount");
pm.test("Amount matches request", () => {
  pm.expect(pm.response.json().data.amount).to.equal(expectedAmount);
});
```

**2. Chain Testing**
Test complete workflows:
```csv
step,action,api_key,expected
1,create_transaction,validkey,201
2,get_transaction,validkey,200
3,delete_transaction,validkey,200
4,verify_deleted,validkey,404
```

**3. Performance Metrics**
Track response times:
```javascript
pm.test("Response time < 500ms", () => {
  pm.expect(pm.response.responseTime).to.be.below(500);
});
```

**4. Newman CLI Integration**
Run tests from command line:
```bash
newman run collection.json -d data.csv --reporters cli,html
```

---

## 📊 Statistics

### Test Execution Metrics

**Collection Runner Results:**

| Metric | Value |
|--------|-------|
| Total Scenarios | 31 |
| Total Requests | 31 |
| Assertions | 62 (2 per request) |
| Passed Tests | 58 |
| Failed Tests | 4 (bugs found!) |
| Execution Time | ~4 minutes |
| Success Rate | 94% |

### Coverage Metrics

| Area | Scenarios |
|------|-----------|
| Authentication | 10 scenarios |
| Authorization | 8 scenarios |
| Validation | 9 scenarios |
| Boundary Values | 6 scenarios |
| Not Found Cases | 4 scenarios |
| Happy Paths | 5 scenarios |

---

## 🎓 Key Takeaway

This case study demonstrates:
- ✅ **Efficiency mindset** - Looking for ways to work smarter, not harder
- ✅ **Automation fundamentals** - Understanding test automation concepts
- ✅ **Systematic approach** - Comprehensive test coverage planning
- ✅ **Tool mastery** - Advanced Postman usage beyond basic requests
- ✅ **Data-driven thinking** - Separating test logic from test data
- ✅ **Scalability** - Building solutions that grow with the project

**The most important lesson:** Good testing isn't about working harder - it's about working smarter. By investing time upfront to create a data-driven framework, I created a testing solution that is faster, more reliable, and more comprehensive than manual testing could ever be.

**Why this matters for QA career:** Data-driven testing is a fundamental concept that applies to all test automation frameworks (Selenium, Playwright, API testing). Understanding this approach shows I'm ready to transition from pure manual testing to automation-assisted testing, which is essential for modern QA roles.

---

## 🔗 Related Resources

**In this portfolio:**
- [API Testing README](../api-testing/README.md) - Complete API testing documentation
- [Postman Collection](../api-testing/postman-collection/CzechiBank.postman_collection.json) - Download and try it yourself!
- [CSV Test Data](../api-testing/test-data/) - All test scenario files

**External Learning:**
- Postman Learning Center: Data-driven testing
- REST API testing best practices
- Test automation pyramid concept

---

*Last updated: October 2025*
