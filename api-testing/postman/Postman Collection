# 📮 Postman Collection

## Overview

This Postman collection contains all API endpoints I tested for the CzechiBank application. It's organized into logical folders and includes example requests with various authentication scenarios.

---

## 🗂️ Collection Structure

### **1. Users**
Endpoints for user management and authentication:
- **Create new user** - Register a new user account
- **Get current user** - Retrieve authenticated user details
- **Get APIkey** - Fetch user's API key for authentication

### **2. Bank Accounts**
Complete CRUD operations for bank accounts:
- **Create new bank account** - Open a new account (auto-generates account number)
- **Get bank accounts (user)** - List all accounts owned by authenticated user
- **Get all bank accounts (bank)** - Admin endpoint for all users' accounts
- **Get bank account by ID** - Retrieve specific account details
- **Delete bank account** - Remove account (discovered it works even with balance ≠ 0 ⚠️)
- **Rename bank account** - Update account name

### **3. Transactions**
Transaction management endpoints:
- **Create transaction** - Transfer money between accounts
- **Get transaction by ID** - Retrieve specific transaction details
- **Get user transactions** - List all user's transactions (with pagination)

### **4. Training Scenarios**
Organized test flows for systematic testing:
- **API Key scenarios** - Testing various authentication states
- **E2E Transaction Flow** - Complete transaction creation and retrieval
- **E2E Bank Account Flow** - Full account lifecycle (create → get → delete)

---

## 🔑 Authentication

All endpoints (except user registration) require authentication via **API Key**.

### **How Authentication Works**
```
Header: x-api-key
Value: [Your 64-character API key]
```

### **Getting Your API Key**
1. Register a user via `POST /api/v1/users`
2. Use the returned API key from the response
3. Alternative: Call `GET /api/v1/users/apikey` with session

---

## 🚀 How to Import This Collection

### **Option 1: Import to Postman**
1. Open Postman
2. Click **Import** button
3. Select `CzechiBank_collection.json`
4. Collection will appear in your workspace

### **Option 2: View Without Postman**
The JSON file is human-readable and can be examined directly to see:
- Request URLs and methods
- Required headers
- Request body examples
- Endpoint organization

---

## ⚙️ Setting Up Variables

For easier testing, set up these Postman variables:

| Variable | Description | Example |
|----------|-------------|---------|
| `base_url` | API base URL | `https://[dev-environment]/api/v1` |
| `apikey` | Your authentication key | `fAHLepbnGYWXOKPKwYBqdq...` |
| `userId` | Current user ID | `cmeabc123...` |
| `accountId` | Test bank account ID | `cmeb4g2hr000...` |
| `transactionId` | Test transaction ID | `cmelbycsr0007...` |

**Setting variables in Postman:**
1. Click on the collection name
2. Go to **Variables** tab
3. Add your values in the **Current Value** column

---

## 📋 Example Requests

### **Creating a Transaction**
```json
POST /api/v1/transactions
Headers: x-api-key: your-api-key-here

{
  "amount": 3,
  "fromBankNumber": "277296219210/5555",
  "toBankNumber": "159968087860/5555"
}
```

### **Getting Transaction by ID**
```
GET /api/v1/transactions/{transaction-id}
Headers: x-api-key: your-api-key-here
```

### **Creating a Bank Account**
```json
POST /api/v1/bank-accounts/create
Headers: x-api-key: your-api-key-here

{
  "currency": "CZECHITOKEN"
}
```

---

## 🧪 Testing Different Scenarios

### **Testing Happy Path**
Use a valid API key in the collection variables and run requests normally.

### **Testing Authorization Failures**
Test these scenarios by modifying the API key:
- **Missing key** - Remove the `x-api-key` header completely
- **Invalid key** - Use `invalidapi123`
- **Expired key** - Use `TKLTTsIyTQAiYMnkZnNHWDyCflxOnSulgnDWHXLWjlIGHMdFFQkZFAEWomgHcXeT`
- **Deleted key** - Use `yaPSbxAGzUSbXXZeMpPqrJYIPhiwEgIrPzCbvVpUhJYvouPHIGhXngsrvEFpNtLY`

### **Testing Edge Cases**
Modify request bodies to test:
- Empty required fields
- Invalid data types (text in number fields)
- Boundary values (0, negative numbers, very large numbers)
- Non-existent IDs (e.g., `9999`)

---

## 🐛 Known Issues (Found During Testing)

### **1. Deleted API Key Returns 500 (CZBANK-71)**
**Issue:** Using a deleted API key on `GET /transactions` returns 500 Internal Server Error instead of 401 Unauthorized.

**How to reproduce:**
```
GET /api/v1/transactions
x-api-key: yaPSbxAGzUSbXXZeMpPqrJYIPhiwEgIrPzCbvVpUhJYvouPHIGhXngsrvEFpNtLY

Expected: 401
Actual: 500
```

### **2. Bank Account Deletion Ignores Balance (CZBANK-47)**
**Issue:** Accounts with non-zero balance can be deleted, potentially causing data loss.

**How to reproduce:**
```
DELETE /api/v1/bank-accounts/{id}
(where account has balance > 0)

Expected: 422 or 403 (business rule violation)
Actual: 200 (successful deletion)
```

### **3. API Key Security Concern (CZBANK-57)**
**Issue:** API key contains the first half of the user's ID, potentially making it easier to guess.

**Example:**
```
User ID:  fAHLepbn...
API Key:  fAHLepbnGYWXOKPKwYBqdq...
          ^^^^^^^^ (matching prefix)
```

---

## 📈 Test Coverage in This Collection

| Category | Requests | Auth Scenarios | Bug Discoveries |
|----------|----------|----------------|-----------------|
| Users | 3 | 2 | 1 |
| Bank Accounts | 7 | 5 | 2 |
| Transactions | 4 | 5 | 3 |
| **Total** | **14** | **12+** | **6+** |

---

## 💡 What This Collection Demonstrates

- ✅ **Comprehensive endpoint coverage** - All major API operations tested
- ✅ **Organized structure** - Logical grouping for easy navigation
- ✅ **Real authentication testing** - Multiple API key scenarios
- ✅ **Practical test flows** - E2E scenarios for realistic usage
- ✅ **Bug discovery methodology** - Systematic approach revealed multiple issues

---

## 🔗 Related Resources

- [Test Data (CSV files)](../test-data/) - Structured test scenarios used with this collection
- [Bug Reports](../../bug-reports/) - Detailed documentation of issues found
- [API Testing Overview](../README.md) - Complete API testing strategy

---

## 📝 Notes

- **Base URL Placeholder**: All URLs use `https://[dev-environment]/api/v1/` as a placeholder
- **Test Data**: For full scenario coverage, refer to the CSV files in `/test-data/`
- **Session vs API Key**: Some endpoints accept both authentication methods - I primarily tested with API keys
- **Pagination**: Transaction and bank account list endpoints support pagination (tested in dedicated requests)

