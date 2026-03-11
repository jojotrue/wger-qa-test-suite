# wger Workout Manager – QA API Test Suite

**Author:** Joanne Trujillo  
**Tool:** Postman v11.87.3  
**API Under Test:** [wger Workout Manager](https://wger.de) — open source REST API  
**Total Test Cases:** 53 across 8 test folders  
**Testing Type:** Manual API Testing

---

## Project Overview

This project is a comprehensive manual API test suite built against the [wger Workout Manager](https://wger.de) REST API. It covers authentication, CRUD operations, pagination, security, and negative testing across all major endpoints.

The suite was designed to simulate real-world QA API testing practices including:

- Token-based authentication and session management
- Positive, negative, and edge case coverage
- Security and input validation testing
- Documented findings from live API testing
- Structured test case organization by feature area

---

## Tech Stack

- **Postman** v11.87.3
- **REST API** — wger v2 (`https://wger.de/api/v2/`)
- **Auth:** JWT (Access + Refresh tokens)

---

## Collection Structure

| Folder | Tests | Description |
|--------|-------|-------------|
| 🔐 Authentication | 10 | Login, token refresh, token verify, protected endpoint access |
| 📋 User Profile | 3 | Get profile, update profile, mass assignment security check |
| 🏋️ Routines | 10 | Full CRUD, pagination, unauthenticated access, not found |
| ⚽ Exercises (Public) | 8 | Public endpoint access, filtering, schema validation |
| 📅 Workout Sessions | 6 | Create, retrieve, filter by date, delete |
| ⚖️ Weight Log | 5 | Create, list, filter, delete weight entries |
| 🔒 Security Tests | 7 | SQL injection, JWT tampering, unauthorized access |
| 🏗️ API Health & Structure | 4 | Schema validation, response time, content-type headers |

---

## Environment Variables

The suite uses a shared Postman environment with the following variables:

| Variable | Description |
|----------|-------------|
| `baseUrl` | `https://wger.de/api/v2` |
| `username` | Your wger account username |
| `password` | Your wger account password |
| `accessToken` | Obtained from login response — paste manually |
| `refreshToken` | Obtained from login response — paste manually |
| `testRoutineId` | ID from a created routine — paste manually |
| `testSessionId` | ID from a created session — paste manually |

---

## Setup & Import Instructions

### Prerequisites
- [Postman](https://www.postman.com/downloads/) installed
- A free [wger account](https://wger.de/en/user/register)

### Steps

1. **Clone this repo**
```bash
git clone https://github.com/jojotrue/wger-qa-test-suite.git
```

2. **Import the collection into Postman**
   - Open Postman → click **Import**
   - Select `wger_Workout_Manager_QA_Test_Suite.json`
   - Click **Import**

3. **Create a new environment**
   - Go to **Environments** → click **+**
   - Name it `wger – QA Environment`
   - Add the variables from the table above
   - Enter your wger `username` and `password`
   - Select this environment from the top-right dropdown

4. **Get your access token**
   - Run **TC-AUTH-01 | POST Login – Valid Credentials**
   - Copy the `access` value from the response
   - Paste it into `accessToken` in your environment

5. **Run tests**
   - Select a folder and run requests in order
   - Update `testRoutineId` and `testSessionId` manually after creating those resources

---

## Recommended Test Run Order

Some tests depend on data created by earlier requests. Run in this order to avoid false failures:

```
TC-AUTH-01        → get fresh accessToken, paste into environment
TC-ROUT-02        → create routine, copy returned ID into testRoutineId
TC-ROUT-03        → GET routine by ID
TC-ROUT-04        → PATCH routine
TC-ROUT-09        → DELETE routine (run last in Routines folder)
TC-SESS-02        → create session, copy returned ID into testSessionId
TC-SESS-03        → GET session by ID
TC-SESS-06        → DELETE session (run last in Sessions folder)
```

---

## Test Results

**Last full run:** March 11, 2026  
**Environment:** wger – QA Environment  
**Runner:** Postman Collection Runner  

| Metric | Result |
|--------|--------|
| Total Assertions | 98 |
| Passed | 98 |
| Failed | 0 |
| Skipped | 0 |
| Errors | 0 |
| Avg Response Time | ~540ms |
| Duration | 1m 14s |

![Test Run Results](test-results/run-results.png)

> **Note:** Response times reflect the public wger.de server. Times may vary depending on server load.

---

## Findings & Bug Report

Real defects and API behavior anomalies discovered during manual testing:

| ID | Test Case | Endpoint | Status | Finding |
|----|-----------|----------|--------|---------|
| BUG-01 | TC-SESS-03 | GET /workoutsession/{id}/ | Schema | `workout` field absent from response — replaced by `routine`. Undocumented API change. |
| BUG-02 | TC-USER-03 | PATCH /api/v2/gym/userconfig/ | 405 | PATCH and PUT both return Method Not Allowed — endpoint appears read-only despite documented update support. |
| BUG-03 | TC-ROUT-04 | PATCH /routine/{id}/ | 400 | PATCH requires all fields including `start` and `end` — behaves as PUT. Partial updates not supported. Violates REST PATCH convention. |

---

## Test Case Naming Convention

All test cases follow this naming standard:

```
TC-[FOLDER]-[NUMBER] | [METHOD] [Description] – [Scenario]
```

Examples:
- `TC-AUTH-01 | POST Login – Valid Credentials`
- `TC-ROUT-06 | GET Routine – Not Found (ID 999999)`
- `TC-SESS-04 | POST Create Session – Invalid Data`

---

## Key Testing Decisions

**Why wger?**  
wger is a real, production REST API with authentication, relational data, and public/private endpoints — making it suitable for demonstrating real-world API testing practices.

**Negative testing**  
Each folder includes at least one negative test case (wrong credentials, invalid ID, missing required fields, unauthenticated access) to validate proper API error handling.

**Security tests**  
The Security Tests folder covers SQL injection attempts, JWT tampering, and privilege escalation — standard checks for any authenticated API.

**Findings documentation**  
Bugs and API behavior anomalies are logged with test case ID, endpoint, status code, and description — consistent with how defects would be reported in a professional QA environment.

---

## Repository Structure

```
wger-qa-test-suite/
├── README.md
├── wger_Workout_Manager_QA_Test_Suite.json    ← Postman collection (import this)
└── docs/
    └── test-cases.md                           ← Full test case documentation
```

---

## Author

**Joanne Trujillo**  
QA Engineer  
GitHub: [@jojotrue](https://github.com/jojotrue)
