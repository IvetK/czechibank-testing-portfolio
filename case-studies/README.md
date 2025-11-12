# Case Studies

## Overview

This section contains detailed analyses of significant bugs and testing methodologies discovered during the CzechiBank testing project. Each case study demonstrates the investigation process, technical analysis, and key insights from real testing scenarios.

---

## Featured Case Studies

### 🔐 [Session Invalidation Vulnerability](./session-invalidation-bug.md)
**Bug ID:** CZBANK-69 | **Severity:** High

Discovered users could perform financial transactions after logout using browser back button. Session cookie was deleted, but backend still processed requests without validation.

**Key Skills:** Security testing, exploratory testing, comparative analysis, DevTools investigation

---

### 🔍 [API Error Handling - 500 vs 401](./api-error-handling-analysis.md)
**Bug ID:** CZBANK-71 | **Severity:** Medium | **Priority:** High

Deleted API keys caused 500 Internal Server Error instead of 401 Unauthorized across all authenticated endpoints, violating REST API standards and misleading developers.

**Key Skills:** Systematic testing, equivalence partitioning, pattern recognition, HTTP standards

---

### 🛡️ [Predictable API Key Pattern](./api-key-security-pattern.md)
**Bug ID:** CZBANK-57 | **Severity:** Medium | **Priority:** Low

API keys shared predictable 8-10 character prefix with User IDs, reducing cryptographic randomness by ~50% and enabling potential brute-force attacks.

**Key Skills:** Pattern recognition, hypothesis-driven testing, security mindset, data analysis

---

### ⚙️ [Data-Driven API Testing Implementation](./data-driven-testing-framework.md)
**Type:** Testing Methodology

Implemented CSV-based data-driven testing framework in Postman, automating 30+ test scenarios and reducing repetitive manual testing by 90%.

**Key Skills:** Process automation, efficiency mindset, tool mastery, systematic approach

---

## Navigation

- [← Back to Main Portfolio](../README.md)
- [Bug Reports](../bug-reports/)
- [Test Cases](../test-cases/)
- [Testing Checklists](../checklists/)
