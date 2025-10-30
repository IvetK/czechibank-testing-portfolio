# [CZBANK-57] API Key Contains Predictable Pattern Based on User ID

**Priority:** Low  
**Severity:** Medium  
**Status:** ✅ RESOLVED  
**Reporter:** Iveta Kuklová 
**Date Reported:** July 15, 2025  
**Date Resolved:** October 2025  
**Environment:** Development  

---

## 📋 Summary

API keys generated during user registration shared a common prefix with User IDs, creating a predictable pattern that reduced the cryptographic randomness of the keys. The first 8-10 characters of the API key consistently matched the User ID prefix.

---

## 🌐 Environment

- **Application:** CzechiBank (educational banking application)
- **Environment:** Development
- **API Endpoint:** `POST /api/v1/user/create`
- **Testing Tool:** Postman

---

## 🔍 Description

During systematic API testing of user registration, newly generated API keys showed structural similarity to User IDs:

**Example:**
```
User ID:  cmcp5mk9i00005u8khwsi4f6b
API Key:  cmcp5mk9i00015u8kmu7rt188
          ^^^^^^^^ (first 8-10 characters identical)
```

This pattern was consistent across 6+ test registrations, suggesting API key generation was partially derived from the User ID generation mechanism.

**Security Implication:**  
If an attacker obtained a User ID, they could predict ~50% of the API key structure, significantly reducing the search space for brute-force attacks.

---

## 📝 Steps to Reproduce

1. **Create test user via API:**
   ```
   POST https://[dev-environment]/api/v1/user/create
   
   Body:
   {
     "name": "Test User",
     "email": "test@example.com",
     "password": "SecurePass123",
     "gender": "male"
   }
   ```

2. **Observe response:**
   ```json
   {
     "success": true,
     "data": {
       "id": "cmcp5mk9i00005u8khwsi4f6b",
       "apiKey": "cmcp5mk9i00015u8kmu7rt188"
     }
   }
   ```

3. **Compare User ID and API Key**
   - First 8-10 characters: Identical
   - Next 4-5 characters: Sequential pattern
   - Remaining characters: Random

4. **Repeat with 5+ additional users to confirm pattern**

---

## 🎯 Expected vs Actual Behavior

**Expected:**
- API keys should be cryptographically random
- No relationship between User ID and API Key
- Full entropy across entire key length

**Actual:**
- API keys shared prefix with User ID
- Only partial randomness in key generation
- Predictable pattern across all test cases

---

## ✅ Resolution

**Status:** RESOLVED

**Fix Verification:**
- API key generation now uses cryptographically secure random generation
- No similarity between User ID and API Key strings
- Tested with new user registrations - no pattern detected

**Verification Steps:**
1. Created new test users
2. Compared User IDs and API Keys
3. Result: No pattern or relationship found ✅

---

## 🏷️ Tags

`security` `api` `cryptography` `resolved`

---

*Report created: July 15, 2025*  
*Resolved: October 2025*
