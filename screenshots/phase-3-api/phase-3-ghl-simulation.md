# Phase 3: Simulate GoHighLevel Contact Creation

## Overview
In this phase, we created a mock GoHighLevel API using Postman and integrated it with our Make.com automation workflow. This simulation allows us to test the complete lead automation flow without connecting to the actual GoHighLevel API.

---

## Learning Objectives
- Understand how to create and use mock API servers
- Learn HTTP request configuration in Make.com
- Practice API testing and troubleshooting
- Master the complete webhook-to-API workflow

---

## Step 1: Set Up Postman Mock Server

### 1.1 Create Collection
1. Opened Postman
2. Created new collection: **"GoHighLevel API Simulation"**
3. Added description: "Mock server simulating GoHighLevel contact creation endpoint"

**Screenshot Reference:** `01-postman-collection-setup.png`

---

### 1.2 Create Mock Endpoint
1. Inside collection, created new request: **"Create Contact"**
2. Configured request:
   - Method: `POST`
   - URL: `{{url}}/contacts`

**Screenshot Reference:** `02-create-contact-request-config.png`

---

### 1.3 Configure Example Response
1. Created example: **"Successful Contact Creation"**
2. Set response code: `201 Created`
3. Added header:
   - Key: `Content-Type`
   - Value: `application/json`

4. Response body:
```json
{
  "contact": {
    "id": "cont_{{$randomUUID}}",
    "firstName": "John",
    "lastName": "Smith",
    "email": "john@example.com",
    "phone": "+15551234567",
    "source": "Facebook Lead Ad",
    "tags": ["facebook-lead", "needs-followup"],
    "dateAdded": "{{$isoTimestamp}}",
    "customFields": []
  },
  "success": true,
  "message": "Contact created successfully"
}
```

**Screenshot Reference:** `03-example-response-configuration.png`

**Key Learning:** Postman variables like `{{$randomUUID}}` and `{{$isoTimestamp}}` generate dynamic data for each request.

---

## Step 2: Create Mock Server

### 2.1 Generate Mock Server
1. Clicked three dots (⋯) on collection
2. Selected "Mock collection"
3. Configuration:
   - Name: `GHL Contact API Mock`
   - Saved URL as environment variable: ✅ Yes
   - Make private: ❌ No (for easier testing)

4. Mock server URL generated:
```
https://26f00862-1da6-4d7a-873a-012e51db5e30.mock.pstmn.io
```

**Screenshot Reference:** `04-mock-server-url-creation.png`

**Important:** This URL is unique to your mock server. The `/contacts` endpoint must be added when making requests.

---

## Step 3: Test Mock Server

### 3.1 Create Test Request
1. Created new request in Postman
2. Configuration:
   - Method: `POST`
   - URL: `https://26f00862-1da6-4d7a-873a-012e51db5e30.mock.pstmn.io/contacts`
   - Body type: `raw` → `JSON`

3. Test payload:
```json
{
  "firstName": "Jane",
  "lastName": "Doe",
  "email": "jane@test.com",
  "phone": "+15559876543",
  "source": "Facebook Lead Ad",
  "tags": ["facebook-lead", "needs-followup"]
}
```

### 3.2 Test Results
- Status: `201 Created`
- Response time: ~1.26 seconds
- Received complete contact data with generated ID and timestamp

**Screenshot Reference:** `05-mock-server-test-success.png`

**Key Learning:** Testing the mock server independently before integrating with Make.com helps identify issues early.

---

## Step 4: Integrate Make.com with Mock Server

### 4.1 Add HTTP Module
1. In Make.com scenario, added new module after "Set Multiple Variables"
2. Selected: **HTTP > Make a Request**

**Screenshot Reference:** `06-makecom-http-module-added.png`

---

### 4.2 Configure HTTP Module

**Basic Settings:**
- Authentication: `No auth`
- URL: `https://26f00862-1da6-4d7a-873a-012e51db5e30.mock.pstmn.io/contacts`
- Method: `POST`

**Headers:**
- Name: `Content-Type`
- Value: `application/json`

**Body Configuration:**
- Body content type: `application/json`
- Body input method: `Enter manually`
- Parse response: `Yes`
- Pagination type: `None`

**Request Content (JSON):**
```json
{
  "firstName": "{{fullName}}",
  "lastName": "",
  "email": "{{email}}",
  "phone": "{{phone}}",
  "source": "Facebook Lead Ad",
  "tags": ["facebook-lead", "needs-followup"]
}
```

**Screenshot Reference:** `07-http-module-full-configuration.png`

**Key Learning:** The `{{fullName}}`, `{{email}}`, and `{{phone}}` variables reference the data extracted in the "Set Multiple Variables" module.

---

## Step 5: Test Complete Flow

### 5.1 Test Execution
1. Clicked "Run once" in Make.com
2. Sent Facebook lead data via Postman webhook test
3. Test payload:
```json
{
  "leadgen_id": "123456789",
  "ad_id": "987654321",
  "form_id": "555444333",
  "field_data": [
    {"name": "full_name", "values": ["John Smith"]},
    {"name": "email", "values": ["john@example.com"]},
    {"name": "phone_number", "values": ["+15551234567"]}
  ],
  "created_time": "2024-12-01T10:30:00+0000"
}
```

### 5.2 Flow Execution Success
All three modules executed successfully:
1. ✅ **Webhooks** - Received Facebook lead data
2. ✅ **Set Multiple Variables** - Extracted fullName, email, phone
3. ✅ **HTTP** - Sent data to mock server, received 201 response

**Screenshot Reference:** `08-complete-flow-execution-success.png`

---

### 5.3 Mock Server Response
The mock server returned complete contact data:

```json
{
  "contact": {
    "id": "cont_47e21f10-c55e-4927-bb59-a1cea2612027",
    "firstName": "John",
    "lastName": "Smith",
    "email": "john@example.com",
    "phone": "+15551234567",
    "source": "Facebook Lead Ad",
    "tags": ["facebook-lead", "needs-followup"],
    "dateAdded": "December 3, 2025 9:20 AM",
    "customFields": []
  },
  "success": true,
  "message": "Contact created successfully"
}
```

**Screenshot Reference:** `09-http-response-contact-data.png`

---

## Troubleshooting

### Issue: 404 Error - "mockRequestNotFoundError"
**Problem:** Mock server couldn't find the endpoint.

**Solution:** 
- Verified URL included `/contacts` path: `https://26f00862-1da6-4d7a-873a-012e51db5e30.mock.pstmn.io/contacts`
- The base mock server URL alone won't work; the endpoint path is required

**Key Learning:** Mock servers require the complete request path to match the examples configured in Postman.

---

### Issue: Tunneling Socket Error (ENOTFOUND)
**Attempted Solutions:**
1. Disabled Postman proxy settings
2. Verified mock server URL in browser
3. Restarted Postman application

**Resolution:** Adding the full `/contacts` path resolved the issue.

---

## Key Learnings

### 1. Mock Servers for Development
- Mock servers allow testing workflows without production API access
- Perfect for learning, development, and testing scenarios
- Can simulate various response scenarios (success, errors, edge cases)

### 2. HTTP Request Configuration
- Authentication type depends on the API (we used "No auth" for mock)
- Headers must match API requirements (Content-Type: application/json)
- Body format must match API expectations

### 3. Variable Mapping in Make.com
- Variables from previous modules can be referenced using `{{variableName}}`
- Data transformation happens between modules
- Parse response should be enabled to use returned data in subsequent modules

### 4. API Testing Best Practices
- Test endpoints independently before integration
- Verify request/response formats match expectations
- Use proper error handling and status codes
- Document all configurations for future reference

---

## Next Steps

### Phase 4 Preview: Error Handling & Notifications
In the next phase, we will:
1. Add error handling to the HTTP module
2. Implement retry logic for failed requests
3. Set up email/SMS notifications for lead processing
4. Add logging and monitoring

### Real API Integration
To connect to the actual GoHighLevel API:
1. Obtain API key from GoHighLevel account
2. Update authentication to "API Key" type
3. Replace mock server URL with production endpoint
4. Add additional error handling for production scenarios

---

## Resources

### Documentation
- [Make.com HTTP Module Documentation](https://www.make.com/en/help/apps/http)
- [Postman Mock Server Guide](https://learning.postman.com/docs/designing-and-developing-your-api/mocking-data/setting-up-mock/)
- [GoHighLevel API Documentation](https://highlevel.stoplight.io/)

### Tools Used
- **Make.com** - Automation platform
- **Postman** - API testing and mock server
- **Webhook.site** - Webhook testing (Phase 1)

---

## Success Metrics
- ✅ Mock server created and operational
- ✅ HTTP module properly configured
- ✅ Complete workflow tested successfully
- ✅ 201 status code received from mock API
- ✅ Contact data properly formatted and transmitted
- ✅ All modules executing without errors

---

## Completion Checklist
- [x] Created Postman collection for GoHighLevel API
- [x] Configured mock endpoint with example response
- [x] Generated and tested mock server
- [x] Added HTTP module to Make.com scenario
- [x] Configured HTTP request with proper headers and body
- [x] Successfully tested complete webhook-to-API flow
- [x] Documented all steps with screenshots
- [x] Identified and resolved troubleshooting issues

---

**Phase 3 Completed:** December 3, 2025
**Time Invested:** 2 hours
**Outcome:** Successfully simulated GoHighLevel contact creation using mock API server
