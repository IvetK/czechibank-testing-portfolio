# CzechiBank Testing Portfolio

> Comprehensive manual and API testing portfolio demonstrating systematic approach to quality assurance in a banking application environment.

**Portfolio Author:** Iveta Kuklová  
**Role:** Junior QA Tester  
**Project Duration:** July - September 2025  

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/your-linkedin-profile)
[![GitHub](https://img.shields.io/badge/GitHub-IvetK-black)](https://github.com/IvetK)

---

## 🎯 Project Overview

CzechiBank is an educational web banking application designed for testing practice. This portfolio showcases my systematic approach to testing a full-stack banking application, including web UI, REST API, and security testing.

### What I Tested
- 🔐 User authentication & authorization
- 💳 Bank account management (CRUD operations)
- 💸 Money transfers & transaction history
- 🔑 API key lifecycle management
- 📱 Responsive UI across browsers (Safari, Chrome)
- 🛡️ Security & session management

**Application Stack:**
- Frontend: React SPA
- Backend: REST API with JSON responses
- Authentication: API Key-based authorization
- Environment: Development & Testing instances

---

## 🏆 Key Achievements

### Testing Results Summary

📊 **14+ bugs discovered** across all severity levels  
✅ **100% bug approval rate** - all bugs confirmed by developers  
🔍 **30+ test scenarios** in Postman with automated assertions  
📝 **50+ test cases** documented across 5 functional areas  

### Bug Distribution by Severity

| Priority | Count | Examples |
|----------|-------|----------|
| **High** | 3 | Session invalidation, API error handling, authorization gaps |
| **Medium** | 4 | Business logic violations, security patterns, data inconsistencies |
| **Low** | 7+ | UX improvements, validation edge cases, documentation gaps |

---

## 🌟 Notable Findings

### 🔴 High: Session Not Invalidated After Logout
**Impact:** Critical security vulnerability  
**Discovery:** Found during exploratory testing using browser back button

Discovered that users could perform financial transactions after logout by using browser back button. The session cookie was removed from browser, but the API endpoint `POST /transactions` still accepted requests without proper session validation, allowing unauthorized transaction creation.

**Why it matters:** Enables unauthorized financial operations, violates authentication best practices  
**Developer response:** "Bug approved. This is critical issue we need to fix."

👉 [Read full case study](case-studies/01-session-invalidation-bug.md)

---

### 🟠 High: API Returns 500 Instead of 401
**Impact:** Improper error handling affecting all authenticated endpoints  
**Discovery:** Systematic testing of authentication scenarios

Through systematic testing of various API key states (valid, missing, invalid, expired, **deleted**), I discovered that deleted API keys cause `500 Internal Server Error` instead of proper `401 Unauthorized` response. This affects debugging and violates REST API best practices.

**Why it matters:** 
- 500 errors indicate server problems, not authentication failures
- Makes debugging harder for API consumers
- Inconsistent error handling across endpoints

**Developer response:** "This is still valid. I will fix it."

👉 [Read full case study](case-studies/02-api-error-handling-analysis.md)

---

### 🟡 Security: Predictable API Key Pattern
**Impact:** Potential security vulnerability  
**Discovery:** Pattern recognition during API key testing

Identified that API keys share the first half of their generated string with the User ID:
```
User ID:  cmcp5mk9i00005u8khwsi4f6b
API Key:  cmcp5mk9i00015u8kmu7rt188
          ^^^^^^^^^^^^ (identical prefix)
```

After testing 6+ user registrations, the pattern was consistent. This creates potential predictability and reduces randomness of API key generation.

**Why it matters:** 
- Enables potential API key prediction
- Violates cryptographic best practices for token generation
- Could allow unauthorized access if User IDs are discoverable

👉 [Read full case study](case-studies/03-security-apikey-pattern.md)

---

### 🔧 Data-Driven API Testing with Postman
**Achievement:** Implemented automated, scalable API testing approach  
**Technology:** Postman Collection Runner + CSV data files

Created data-driven testing framework using Postman with CSV test scenarios:
- **30+ test scenarios** covering happy paths, edge cases, and negative testing
- **Automated assertions** for status code validation
- **Reusable test scripts** with dynamic expected values
- **4 CSV data files** for different endpoint testing

**Coverage:**
- Authorization states: valid, missing, invalid, expired, deleted API keys
- Boundary values: zero, negative, minimum, maximum amounts
- Validation: missing fields, invalid formats, non-existent resources

👉 [Read full case study](case-studies/04-data-driven-api-testing.md)

---

## 🛠️ Tools & Technologies

### Testing Tools
- **Postman** - API testing with automated scripts and Collection Runner
- **Browser DevTools** - Network analysis, session debugging, cookie inspection
- **Jira** - Bug tracking, team communication, and workflow management
- **Git/GitHub** - Version control and documentation

### Testing Techniques Applied
- 🧭 **Exploratory Testing** - Discovering unexpected behavior through guided exploration
- ⚙️ **Functional Testing** - Verifying features work according to specifications
- 🔢 **Boundary Value Analysis** - Testing edge cases and limits
- 🔢 **Equivalence Partitioning** - Efficient test data selection
- ❌ **Negative Testing** - Invalid inputs and error handling validation
- 🔒 **Security Testing** - Authentication, authorization, and session management
- 📊 **Data-Driven Testing** - CSV-based test scenarios with Postman

---

## 📂 Portfolio Structure

### 📋 [Test Cases](test-cases/)
50+ organized test scenarios covering authentication, API keys, bank accounts, transactions, and UI testing.
- Structured by functional area
- Clear preconditions and expected results
- Priority and severity classification

### 🐛 [Bug Reports](bug-reports/)
Detailed bug reports organized by severity, including:
- Reproduction steps with screenshots
- Expected vs actual behavior analysis
- Business impact assessment
- Developer communication and resolution

### 📖 [Case Studies](case-studies/)
In-depth analysis of significant findings, showcasing:
- My problem-solving process
- Technical investigation approach
- Security and business impact
- Lessons learned

### 🔧 [API Testing](api-testing/)
Complete API testing framework including:
- Postman collection (downloadable)
- CSV test data files
- Automated test scripts documentation
- API endpoint coverage matrix

### 📊 [Test Metrics](test-metrics/)
- Test coverage summary
- Bug statistics and trends
- Testing timeline and milestones

### 🚀 [Future Improvements](future-improvements/)
My learning roadmap including:
- Performance testing with Postman
- Test automation experiments
- Regression testing strategy
- Advanced DevTools techniques

---

## 💡 What Makes This Portfolio Special

✅ **Real bugs from real project** - Not theoretical examples, all bugs were approved and fixed by developers  
✅ **Security mindset** - Multiple security findings showing advanced awareness  
✅ **Systematic approach** - Data-driven testing with CSV scenarios and automated assertions  
✅ **Full testing lifecycle** - From test planning through execution to bug retesting  
✅ **Clear communication** - Professional bug reports with reproduction steps and visual evidence  
✅ **Growth mindset** - Documented learning journey and future improvement plans  

---

## 📚 Documentation Highlights

### Most Comprehensive Sections
1. **[Testing Strategy](docs/test-strategy.md)** - How I approached the testing process
2. **[API Testing Documentation](api-testing/README.md)** - Deep dive into API testing framework
3. **[Session Security Case Study](case-studies/01-session-invalidation-bug.md)** - Critical bug investigation
4. **[Lessons Learned](docs/lessons-learned.md)** - Key takeaways and professional growth

---

## 🎓 Background & Skills

### Testing Expertise
- Manual testing of web applications
- REST API testing and validation
- Security testing and vulnerability identification
- Test case design and documentation
- Bug reporting and tracking
- Team collaboration and communication

### Technical Skills
- Postman (advanced usage with scripts)
- Browser DevTools (Network, Console, Application tabs)
- Basic JavaScript for test automation
- Git/GitHub for version control
- Jira for project management
- Markdown for documentation

### Currently Learning
- Performance testing methodologies
- Test automation frameworks (Selenium, Playwright)
- Advanced security testing techniques
- SQL for database validation

---

## 📧 Contact & Connect

I'm actively seeking opportunities as a QA Tester where I can contribute my systematic approach to testing, security awareness, and continuous learning mindset.

**LinkedIn:** www.linkedin.com/in/iveta-kuklová
**Email:** iveta.charvatova@gmail.com  
**GitHub:** [github.com/IvetK](https://github.com/IvetK)  
**Location:** Czech Republic  

Feel free to reach out if you'd like to discuss testing strategies, quality assurance, or if you have any questions about my work!

---

## 📝 Project Notes

This portfolio represents testing work on CzechiBank, an educational banking application created for testing practice. All test data and user accounts are fictional and created specifically for testing purposes. Bug reports and findings are real and were communicated to the development team through Jira.

**Testing Period:** July - September 2025  
**Last Portfolio Update:** October 2025  

---

## ⭐ Acknowledgments

Special thanks to the CzechiBank development team for creating a realistic testing environment and providing valuable feedback on bug reports. This project was an invaluable learning experience in collaborative software development.

---

*"Quality is not an act, it is a habit." - Aristotle*
