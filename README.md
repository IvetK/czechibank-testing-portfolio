# CzechiBank Testing Portfolio

> Comprehensive manual and API testing portfolio demonstrating systematic approach to quality assurance with focus on security and technical depth.

**Portfolio Author:** Iveta Kuklová  
**Role:** Junior QA Tester  
**Project Duration:** July - September 2025  

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/iveta-kuklová)
[![GitHub](https://img.shields.io/badge/GitHub-IvetK-black)](https://github.com/IvetK)

---

## 🎯 Project Overview

CzechiBank is an educational web banking application designed for testing practice. This portfolio showcases systematic approach to testing a full-stack banking application with emphasis on security vulnerabilities and API validation.

### What I Tested
Authentication & authorization • Bank accounts (CRUD) • Money transfers & transaction history • API key management • Responsive UI (Safari, Chrome) • Security & session handling

**Stack:** React SPA | REST API with JSON | API Key-based authorization | Dev/Test environments

---

## 🏆 Key Achievements

### Testing Results Summary

📊 **14+ bugs discovered** across all severity levels  
🔍 **29 test scenarios** in Postman with automated assertions  
📝 **50+ test cases** documented across 5 functional areas  

### Bug Distribution by Severity

| Priority | Count | Focus Areas |
|----------|-------|-------------|
| **High** | 3 | Session security, API error handling, authorization validation |
| **Medium** | 4 | Business logic, security patterns, data consistency |
| **Low** | 7+ | UX improvements, validation edge cases, documentation |

---

## 🌟 Notable Findings

### 🔴 Session Not Invalidated After Logout (Critical Security)
Users could perform financial transactions after logout using browser back button. Session cookies were removed, but API endpoint `POST /transactions` still accepted requests without proper session validation - enabling unauthorized financial operations.

**Impact:** Critical security vulnerability violating authentication best practices  
**Developer response:** ✅ "Bug approved. This is critical issue we need to fix."

👉 [Full investigation & technical analysis →](case-studies/01-session-invalidation-bug.md)

---

### 🟠 API Returns 500 Instead of 401 for Deleted Keys (API Design)
Through systematic testing of authentication states (valid, missing, invalid, expired, deleted), discovered that deleted API keys cause `500 Internal Server Error` instead of proper `401 Unauthorized` response. This violates REST API best practices and makes debugging significantly harder.

**Impact:** Improper error handling affecting all authenticated endpoints  
**Developer response:** ✅ "This is still valid. I will fix it."

👉 [Full API testing methodology →](case-studies/02-api-error-handling-analysis.md)

---

### 🟡 Predictable API Key Pattern (Security Risk)
Identified that API keys share the first 50% of their string with User IDs, creating predictable pattern:
```
User ID:  cmcp5mk9i00005u8khwsi4f6b
API Key:  cmcp5mk9i00015u8kmu7rt188
          ^^^^^^^^^^^^ identical prefix
```
Pattern confirmed across 6+ user registrations, indicating systematic generation flaw that violates cryptographic best practices.

**Impact:** Enables potential API key prediction if User IDs are discoverable  
**Security concern:** Reduces randomness of authentication tokens

👉 [Full security analysis →](case-studies/03-security-apikey-pattern.md)

---

## 🛠️ Tools & Technologies

### Testing Tools
- **Postman** - API testing with Collection Runner, automated scripts, CSV data-driven testing
- **Browser DevTools** - Network analysis, session debugging, cookie/localStorage inspection
- **Jira** - Bug tracking and team collaboration
- **Git/GitHub** - Version control and documentation

### Testing Techniques Applied
- 🔒 **Security Testing** - Session management, authentication flows, authorization validation
- 🧭 **Exploratory Testing** - Discovering unexpected behavior through guided exploration
- ⚙️ **Functional Testing** - Verifying features against specifications
- 🔢 **Boundary Value Analysis** - Testing edge cases and system limits
- ❌ **Negative Testing** - Invalid inputs and error handling validation
- 📊 **Data-Driven Testing** - CSV-based test scenarios with automated assertions

---

## 📂 Portfolio Structure

### 📋 [Test Cases](test-cases/)
50+ test scenarios covering authentication, API keys, bank accounts, transactions, and UI testing. Organized by functional area with clear preconditions, steps, and expected results.

### 🐛 [Bug Reports](bug-reports/)
Detailed bug reports organized by severity (High, Medium, Low) including reproduction steps, expected vs actual behavior, business impact assessment, and screenshots.

### 📖 [Case Studies](case-studies/)
In-depth technical analysis of 3 critical findings showcasing problem-solving process, security investigation approach, and lessons learned.

### 🔧 [API Testing](api-testing/)
Complete API testing framework with Postman collection, CSV test data files, automated assertions, and endpoint coverage matrix.

### 📊 [Test Strategy & Documentation](docs/)
Testing approach, methodology decisions, and comprehensive lessons learned throughout the project.

---

## 💡 Why This Portfolio Demonstrates Technical Skills

**Security-First Mindset**  
Multiple security vulnerabilities identified including session management flaws, predictable token patterns, and authorization gaps - showing advanced security awareness beyond functional testing.

**API Testing Expertise**  
Data-driven Postman testing with 30+ automated scenarios, systematic authentication state coverage, and proper REST API validation including error handling verification.

**Systematic Approach**  
Organized test cases, reproducible bug reports with clear steps, and comprehensive documentation showing methodical quality assurance process.

**Full Testing Lifecycle**  
From test strategy and planning through execution, bug reporting, developer communication, to verification - demonstrating complete QA workflow.

---

## 📧 Contact

I'm actively seeking opportunities as a QA Tester where I can contribute systematic testing approach, security awareness, and technical problem-solving skills.

**LinkedIn:** www.linkedin.com/in/iveta-kuklová  
**Email:** iveta.charvatova@gmail.com  
**GitHub:** [github.com/IvetK](https://github.com/IvetK)  
**Location:** Czech Republic  

---

## 📝 Project Notes

This portfolio represents testing work on CzechiBank, an educational banking application created for testing practice. All test data and user accounts are fictional. Bug reports and findings are real and were communicated to the development team through Jira.

**Testing Period:** July - September 2025  
**Last Portfolio Update:** November 2025

---

*Professional quality assurance through systematic testing and security awareness.*
