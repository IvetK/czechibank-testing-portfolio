# [CZBANK-41] Bank Account Creation - Duplicate Default Names

**Priority:** Medium  
**Severity:** Low  
**Status:** Done / Fixed  
**Reporter:** Iveta Kuklová  
**Date Reported:** July 8, 2025  
**Resolution:** Fixed in new release  
**Verification:** Retested - works correctly now  

---

## 📋 Summary

When creating multiple bank accounts via API endpoint `POST /bank-account/create`, all accounts received the identical default name "New Bank Account" with no differentiation. This made it impossible for users to distinguish between accounts, creating significant usability issues.

**Resolution:** Fixed in subsequent release with sequential numbering ("New Bank Account 01, 02, 03...") and addition of UI functionality allowing custom account names.

---

## 🔍 Description

### Original Issue

At the time of discovery, bank accounts could only be created via API (no UI functionality existed). When testing account creation through Postman:

**Problem:**
- API did not accept a "name" parameter
- System assigned default name: **"New Bank Account"**
- **ALL accounts received the IDENTICAL name**
- No sequential numbering or differentiation

**Impact:**
When user created multiple accounts:
```
Account 1: "New Bank Account"
Account 2: "New Bank Account"  ← IDENTICAL name!
Account 3: "New Bank Account"  ← IDENTICAL name!
```

Users could not distinguish their accounts by name, causing:
- Confusion when selecting accounts
- Risk of sending money to wrong account
- Poor user experience
- Need to check account numbers or balances to identify accounts

---

## 🌐 Environment

**Testing Environment:**
- Application: CzechiBank (educational banking application)
- Environment: Development
- API Endpoint: `POST /api/v1/bank-account/create`
- Testing Tool: Postman
- Date Discovered: July 8, 2025

**Original Limitation:**
- Bank accounts could ONLY be created via API
- No frontend UI functionality available
- API was the only method for account creation

---

## 📝 Steps to Reproduce (Original Bug)

### Prerequisites
- Valid user account with API key
- Access to Postman

### Reproduction Steps

**Step 1: Create new bank Account**
```
POST https://[dev-environment]/api/v1/bank-account/create

Headers:
  x-api-key: [valid-api-key]

Body:
{
  "currency": "CZECHITOKEN"
}

Response:
{
  "name": "New Bank Account"  ← Default name assigned
}
```

**Step 2: Create Second Account**
```
POST /bank-account/create
Body: { "currency": "CZECHITOKEN" }

Response:
{
  "name": "New Bank Account"  ← SAME name as Account 1!
}
```


**Step 3: View Accounts**
```
GET /bank-account

Response shows all accounts with identical names:
[
  { "name": "New Bank Account" },
  { "name": "New Bank Account" },
  { "name": "New Bank Account" }
]
```

**Result:** User cannot distinguish accounts by name.

---

## 🎯 Expected Behavior

**Accounts should be distinguishable:**

### Option 1: Accept Custom Names
Allow users to specify account names via API:
```json
POST /bank-account/create
Body:
{
  "currency": "CZECHITOKEN",
  "name": "Personal Savings"
}
```

### Option 2: Sequential Default Naming
If no name provided, use unique default names:
```
Account 1: "New Bank Account 01"
Account 2: "New Bank Account 02"
Account 3: "New Bank Account 03"
```

### Option 3: UI Functionality
Provide frontend interface where users can:
- Create accounts through UI
- Specify custom names during creation
- Distinguish accounts visually

---

## 🐛 Actual Behavior (Original Bug)

**What was happening:**

1. **No Name Parameter Support**
   - API did not accept "name" field
   - Even if provided, it was ignored
   - No way to customize account name

2. **Identical Default Names**
   - System always assigned: "New Bank Account"
   - No numbering, no differentiation
   - Every account had the exact same name

3. **No UI Alternative**
   - Only API method available
   - Frontend had no account creation feature
   - Users forced to use API

4. **User Confusion**
   - Account list showed duplicate names
   - Impossible to identify correct account
   - Had to check account numbers or balances
   - Risk of selecting wrong account for transfers

---

## 💥 Impact Assessment

### Original Severity: MEDIUM

**User Experience Impact:**
- **High**: Completely unable to distinguish accounts by name
- **Medium**: Increased risk of user errors (wrong account selection)
- **High**: Poor usability, non-intuitive interface

**Frequency:**
- **High**: Affected every user with multiple accounts
- Occurred 100% of the time

**Business Impact:**
- Violated banking UX best practices
- Users expected ability to name/identify accounts
- Standard feature missing from application

---

## ✅ Resolution & Fix

### What Was Implemented

The development team implemented a comprehensive fix in a new release:

**1. UI Functionality Added** ✅
- Frontend now includes account creation interface
- Users can create accounts directly through UI
- Custom naming available during creation

**2. API Enhanced** ✅
- API now accepts "name" parameter
- Users can specify custom account names via API
- Validation added for name field

**3. Improved Default Naming** ✅
- Sequential numbering implemented
- Default names now: "New Bank Account 01", "New Bank Account 02", etc.
- Each account receives unique identifier
- No more duplicate names

**Example After Fix:**
```
User creates 3 accounts without names:
→ "New Bank Account 01"
→ "New Bank Account 02"
→ "New Bank Account 03"

Or user provides custom names:
→ "Personal Savings"
→ "Business Account"
→ "Emergency Fund"
```

---

## 🧪 Verification Testing

### Retest After Fix

**Test 1: Default Sequential Naming**
```
Create 3 accounts without names via API
Result: ✅ "New Bank Account 01, 02, 03"
Status: PASS - Unique names assigned
```

**Test 2: Custom Names via API**
```
POST /bank-account/create
Body: { "name": "My Savings" }
Result: ✅ Account created with custom name
Status: PASS - Name parameter accepted
```

**Test 3: UI Account Creation**
```
Create account through frontend interface
Enter custom name: "Business Account"
Result: ✅ Account created with specified name
Status: PASS - UI functionality works
```

**Test 4: Mixed Creation Methods**
```
Create accounts via both UI and API
Verify all have unique identifiable names
Result: ✅ All accounts distinguishable
Status: PASS - No duplicate names
```

**Overall Verification:** ✅ **BUG RESOLVED**

---

## 💬 Communication History

**Initial Report:**
> "When creating multiple bank accounts via API, the system assigns identical default name 'New Bank Account' to all accounts with no differentiation. Users cannot distinguish between accounts, causing confusion and usability issues. Recommend implementing either custom naming capability or sequential numbering for defaults."

**Developer Response:**
> "Bug approved. This is issue and we can improve this. Good point and I agree with your suggested solution."

**Note on Implementation:**
The developer team had plans to add UI functionality and improve account management. This bug report may have helped prioritize these improvements and accelerate implementation of user-friendly features.

---

## 🔗 Related Improvements

**Features Added in Fix:**
- UI for bank account creation
- Custom account naming via both UI and API
- Sequential default naming system
- Improved account management overall

**Benefits:**
- Better user experience
- Reduced confusion
- Alignment with banking application standards
- Enhanced usability

---

## 🎓 Lessons Learned

### What This Bug Taught Me

**1. User-Centric Testing**
- Put myself in user's position
- Asked: "How would I manage 3 identical accounts?"
- Identified real usability problem

**2. API-Only Testing Reveals Gaps**
- Testing through API exposed missing UI functionality
- Discovered both naming issue AND missing feature
- Comprehensive testing across all access methods

**3. Real-World Comparison**
- Compared with actual banking applications
- Recognized missing standard feature
- Industry norms help identify gaps

**4. Impact of Good Bug Reports**
- Clear documentation of problem
- Suggested practical solutions
- May have influenced implementation priorities

**5. Full Testing Lifecycle**
- Reported bug → Developer fixed → Retested
- Verified resolution works correctly
- Closed loop on bug lifecycle

### Testing Techniques Applied

- **Exploratory Testing:** Created multiple accounts to observe behavior
- **Usability Testing:** Evaluated from user's perspective
- **API Testing:** Tested through Postman systematically
- **Regression Testing:** Verified fix in new release
- **Comparative Analysis:** Compared with real banking apps

---

## 📊 Bug Lifecycle Summary

| Stage | Date | Status |
|-------|------|--------|
| **Discovery** | July 8, 2025 | Bug found during API testing |
| **Reported** | July 8, 2025 | Documented and submitted |
| **Acknowledged** | July 2025 | Developer confirmed issue |
| **Fixed** | August/September 2025 | Implemented in new release |
| **Retested** | September 2025 | Verified fix works correctly |
| **Closed** | September 2025 | ✅ Bug resolved |

---

## 🏷️ Labels

`api` `ux` `usability` `account-management` `naming` `fixed` `verified` `user-experience`

---

## ✨ Final Status

**Status:** ✅ **RESOLVED**  
**Fix Verified:** Yes  
**User Impact:** Positive - Significant improvement in usability  
**Lessons:** Importance of user-centric testing and clear bug documentation  

---

*Bug report created: July 8, 2025*  
*Bug fixed: August/September 2025*  
*Verification completed: September 2025*  
*Last updated: October 2025*
