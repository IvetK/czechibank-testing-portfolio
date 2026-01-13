# Bug Reports

> Professional documentation of defects discovered during systematic testing of CzechiBank application.

---

## 📊 Summary

**14+ bugs discovered** | **100% developer approval rate** | **All critical bugs resolved**

| Priority | Count | Status |
|----------|-------|--------|
| 🔴 High | 3 | ✅ All resolved |
| 🟡 Medium | 4+ | ✅ All resolved |
| 🟢 Low | 7+ | 📝 Some open |

**Testing Coverage:** API, Frontend, Security, Business Logic, UX

---

## 🚨 Notable Findings

### 🔴 [CZBANK-69] Session Not Invalidated After Logout
**Severity:** High | **Impact:** Critical Security Vulnerability | **Status:** ✅ Resolved

Users could perform transactions after logout using browser back button. Session cookie was removed, but API still accepted requests without validation.

**Discovery:** Exploratory testing with browser navigation  
**Business Impact:** Unauthorized access to banking functions

---

### 🔴 [CZBANK-71] API Returns 500 Instead of 401
**Severity:** High | **Impact:** Poor Error Handling | **Status:** ✅ Resolved

Deleted API keys cause `500 Internal Server Error` instead of proper `401 Unauthorized` response.

**Discovery:** Systematic API key lifecycle testing  
**Technical Impact:** Misleading errors, harder debugging, violates REST best practices

---

### 🟡 [CZBANK-47] Delete Account with Non-Zero Balance
**Severity:** Medium | **Impact:** Business Logic Violation | **Status:** ✅ Resolved

Bank account deletion succeeds even when balance ≠ 0, contradicting API documentation.

**Discovery:** Testing against documented business rules  
**Developer Feedback:** *"Bug approved. This is bug in application, we should fix it."*

---

### 🟡 [CZBANK-57] Predictable API Key Pattern
**Severity:** Medium | **Impact:** Security Weakness | **Status:** ✅ Resolved

API keys shared first 8-10 characters with User ID, reducing entropy:
```
User ID:  cmcp5mk9i00005u8khwsi4f6b
API Key:  cmcp5mk9i00015u8kmu7rt188
          ^^^^^^^^ (identical)
```

**Discovery:** Pattern recognition across 6+ registrations

---

## 📁 Complete Bug Index

### By Priority
- **[High Priority Bugs](high/)** - Critical security and functionality issues
- **[Medium Priority Bugs](medium/)** - Business logic violations, security weaknesses
- **[Low Priority Bugs](low/)** - UX improvements, validation gaps, documentation issues

### By Category
- **Security:** [CZBANK-69](critical/CZBANK-69_Session_Invalidation.md), [CZBANK-57](medium/CZBANK-57_API_Key_Security_Pattern.md)
- **API:** [CZBANK-71](high/CZBANK-71_API_Error_Handling.md), [CZBANK-50](medium/CZBANK-50_Transaction_Source_Account.md)
- **Business Logic:** [CZBANK-47](medium/CZBANK-47_Delete_Account_Business_Logic.md), [CZBANK-76](medium/CZBANK-76_Initial_Balance.md)
- **Frontend/UX:** [CZBANK-74](low/CZBANK-74_Mobile_UI_Overlap.md), [CZBANK-33](low/CZBANK-33_Avatar_Generation_Bug.md)

---

## 🔍 Testing Approach

**Discovery Methods:**
- 🧭 Exploratory Testing - Session bugs, UX issues
- 📋 Systematic Test Execution - API validation, business rules
- 🔐 Security Testing - Authentication, authorization scenarios
- 🔢 Boundary Testing - Edge cases, extreme values
- 📊 Data-Driven Testing - CSV scenarios with Postman

**Tools:**
- JIRA - Bug tracking and team collaboration
- Postman - API testing with automated assertions
- Browser DevTools - Network analysis, session debugging
- Safari & Chrome - Cross-browser testing

---

## 💬 Developer Feedback

> *"Bug approved. This is critical issue we need to fix."*  
> *"This is bug in application, we should fix it. Thanks for reporting it!"*  
> *"Good point and I agree with your suggested solution."*

All reported bugs were confirmed and prioritized by the development team.

---

## 📚 Related Documentation

- [Test Cases](../test-cases/) - Test scenarios that discovered these bugs
- [Case Studies](../case-studies/) - Deep-dive investigations of key findings
- [API Testing](../api-testing/) - Postman collection and test framework

---

*Last Updated: October 2025*
