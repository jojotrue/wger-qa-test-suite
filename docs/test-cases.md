# Test Cases: wger Workout Manager API Suite

Last verified: November 11, 2026 (123 assertions, 116 passed, 7 known-bug failures, 0 errors).

Each request runs multiple assertions; the **Expected** column summarizes them. Tests marked *Known fail* assert the correct behavior and fail until the defect is fixed. See the README for full findings.

## Authentication

| ID | Scenario | Request | Expected | Result |
|----|----------|---------|----------|--------|
| TC-AUTH-01 | POST Login – Valid Credentials | `POST /allauth/app/v1/auth/login` | 200, access and refresh tokens in `meta`, password not echoed | Pass |
| TC-AUTH-02 | POST Login – Wrong Password | `POST /allauth/app/v1/auth/login` | 400 with `errors`, no tokens | Pass |
| TC-AUTH-03 | POST Login – Non-existent User | `POST /allauth/app/v1/auth/login` | 400, no tokens, same error code as AUTH-02 | Pass |
| TC-AUTH-04 | POST Login – Empty Body | `POST /allauth/app/v1/auth/login` | 400 with validation errors, no tokens | Pass |
| TC-AUTH-05 | POST Login – SQL Injection Attempt | `POST /allauth/app/v1/auth/login` | 400, no tokens, no database error text | Pass |
| TC-AUTH-06 | POST Refresh Token – Valid | `POST /allauth/app/v1/tokens/refresh` | 200, new access token, refresh token rotated | Pass |
| TC-AUTH-07 | POST Refresh Token – Invalid Token | `POST /allauth/app/v1/tokens/refresh` | 400, rejected as invalid (not missing), no tokens | Pass |
| TC-AUTH-08 | POST Verify Token – Valid | `POST {{baseUrl}}/token/verify` | 200 | Pass |
| TC-AUTH-09 | GET Protected Endpoint – No Token | `GET {{baseUrl}}/routine/` | 401 with no data returned | Known fail: 403 (BUG-01) |
| TC-AUTH-10 | GET Protected Endpoint – Tampered JWT | `GET {{baseUrl}}/routine/` | 401 with no data returned | Known fail: 403 (BUG-01) |
| TC-AUTH-11 | POST Refresh Token – Documented Endpoint | `POST {{baseUrl}}/token/refresh` | 200 (documented endpoint accepts login refresh token) | Known fail: 401 "Token has no type" (BUG-02) |

## User Profile

| ID | Scenario | Request | Expected | Result |
|----|----------|---------|----------|--------|
| TC-USER-01 | GET User Profile – Own | `GET {{baseUrl}}/userprofile/` | 200 with profile fields, no password | Pass |
| TC-USER-02 | PATCH Update User Profile – Valid | `PATCH {{baseUrl}}/userprofile/` | 200, `sleep_hours` updated to the random value sent | Pass |
| TC-USER-03 | Mass Assignment – Try to Escalate to Trainer | `PATCH {{baseUrl}}/userprofile/` | 200 or 400, no staff/superuser/trainer escalation | Pass |

## Routines

| ID | Scenario | Request | Expected | Result |
|----|----------|---------|----------|--------|
| TC-ROUT-01 | GET List Routines – Authenticated | `GET {{baseUrl}}/routine/` | 200 paginated list | Pass |
| TC-ROUT-02 | POST Create Routine – Valid | `POST {{baseUrl}}/routine/` | 201, numeric ID, name matches; saves `testRoutineId` | Pass |
| TC-ROUT-03 | GET Routine by ID – Valid | `GET {{baseUrl}}/routine/{{testRoutineId}}/` | 200, ID matches saved routine | Pass |
| TC-ROUT-04 | PATCH Update Routine – Valid | `PATCH {{baseUrl}}/routine/{{testRoutineId}}/` | 200, name updated with name-only PATCH | Pass |
| TC-ROUT-06 | GET Routine – Not Found (ID 999999) | `GET {{baseUrl}}/routine/999999/` | 404 | Pass |
| TC-ROUT-07 | GET Routine – Unauthenticated | `GET {{baseUrl}}/routine/{{testRoutineId}}/` | 401 with no data returned | Known fail: 403 (BUG-01) |
| TC-ROUT-08 | GET List Routines – Pagination | `GET {{baseUrl}}/routine/?limit=5&offset=0` | 200, at most 5 results | Pass |
| TC-ROUT-09 | DELETE Routine – Valid | `DELETE {{baseUrl}}/routine/{{testRoutineId}}/` | 204 | Pass |
| TC-ROUT-10 | DELETE Routine – Already Deleted (404) | `DELETE {{baseUrl}}/routine/{{testRoutineId}}/` | 404 | Pass |

## Exercises (Public Endpoints)

| ID | Scenario | Request | Expected | Result |
|----|----------|---------|----------|--------|
| TC-EX-01 | GET Exercise List – No Auth Required | `GET {{baseUrl}}/exercise/` | 200 paginated list; saves `testExerciseId` | Pass |
| TC-EX-02 | GET Exercise by ID – Valid | `GET {{baseUrl}}/exercise/{{testExerciseId}}/` | 200 with id, category, muscles | Pass |
| TC-EX-03 | GET Exercise by ID – Not Found | `GET {{baseUrl}}/exercise/99999999/` | 404 | Pass |
| TC-EX-04 | GET Exercise List – Filter by Category | `GET {{baseUrl}}/exercise/?category=10` | 200, every result in category 10 | Pass |
| TC-EX-05 | GET Exercise List – Filter by Equipment | `GET {{baseUrl}}/exercise/?equipment=3` | 200, results array | Pass |
| TC-EX-06 | GET Exercise Categories – Public | `GET {{baseUrl}}/exercisecategory/` | 200, non-empty list with names | Pass |
| TC-EX-07 | GET Muscles – Public | `GET {{baseUrl}}/muscle/` | 200, non-empty list | Pass |

## Workout Sessions

| ID | Scenario | Request | Expected | Result |
|----|----------|---------|----------|--------|
| TC-SESS-01 | GET Workout Sessions List | `GET {{baseUrl}}/workoutsession/` | 200 paginated list | Pass |
| TC-SESS-02 | POST Create Session – Valid | `POST {{baseUrl}}/workoutsession/` | 201, UUID string ID; saves `testSessionId` | Pass |
| TC-SESS-03 | GET Session by ID – Valid | `GET {{baseUrl}}/workoutsession/{{testSessionId}}/` | 200, current-schema fields, ID matches | Pass |
| TC-SESS-04 | POST Create Session – Invalid Date | `POST {{baseUrl}}/workoutsession/` | 400 with date field error | Pass |
| TC-SESS-05 | GET Sessions – Ordered by Start Time | `GET {{baseUrl}}/workoutsession/?ordering=-datetime_start` | 200, sorted newest first by `datetime_start` | Pass |
| TC-SESS-06 | DELETE Session – Valid | `DELETE {{baseUrl}}/workoutsession/{{testSessionId}}/` | 204 | Pass |

## Weight Log

| ID | Scenario | Request | Expected | Result |
|----|----------|---------|----------|--------|
| TC-WGHT-01 | GET Weight Entries | `GET {{baseUrl}}/weightentry/` | 200 paginated list | Pass |
| TC-WGHT-02 | POST Create Weight Entry – Valid | `POST {{baseUrl}}/weightentry/` | 201, weight 75.50 saved | Pass |
| TC-WGHT-03 | POST Create Weight Entry – Negative Weight | `POST {{baseUrl}}/weightentry/` | 400 on `weight` only, message states 20–350 kg | Pass |
| TC-WGHT-04 | POST Create Weight Entry – Missing Date (defaults to now) | `POST {{baseUrl}}/weightentry/` | 201, date defaults to current time, weight saved | Pass |

## Security Tests

| ID | Scenario | Request | Expected | Result |
|----|----------|---------|----------|--------|
| TC-SEC-01 | BOLA – Access Another User's Routine (ID 1) | `GET {{baseUrl}}/routine/1/` | 403 or 404, no data leaked | Pass (404) |
| TC-SEC-02 | BOLA – Access Another User's Weight Entry (ID 1) | `GET {{baseUrl}}/weightentry/1/` | 403 or 404, no data leaked | Pass (404) |
| TC-SEC-03 | Expired JWT – Access Protected Route | `GET {{baseUrl}}/routine/` | 401 with no data returned | Known fail: 403 (BUG-01) |
| TC-SEC-04 | No Auth Header – Protected Route | `GET {{baseUrl}}/workoutsession/` | 401 with no data returned | Known fail: 403 (BUG-01) |
| TC-SEC-05 | Malformed Auth Header | `GET {{baseUrl}}/routine/` | 401 with no data returned | Known fail: 403 (BUG-01) |
| TC-SEC-06 | XSS Payload in Routine Name | `POST {{baseUrl}}/routine/` | 201 or 400, JSON response; created routine deleted | Pass (201, stored as is) |
| TC-SEC-07 | Oversized Payload | `POST {{baseUrl}}/routine/` | 400 on `name` only, 25-character limit | Pass |

## API Health & Structure

| ID | Scenario | Request | Expected | Result |
|----|----------|---------|----------|--------|
| TC-HLTH-01 | GET API Root – No Auth | `GET {{baseUrl}}/` | 200, root lists main endpoints | Pass |
| TC-HLTH-02 | GET API Root – HTTPS Request, No Version Leak | `GET {{baseUrl}}/` | HTTPS request, no server version in headers | Pass |
| TC-HLTH-03 | OPTIONS – Check Allowed Methods on Routine | `OPTIONS {{baseUrl}}/routine/` | 200, `Allow` includes GET and POST | Pass |
| TC-HLTH-04 | GET Invalid Endpoint – 404 Handling | `GET {{baseUrl}}/thisdoesnotexist/` | 404 with no stack trace or framework text | Pass |

## Defects

| ID | Tests | Summary |
|----|-------|---------|
| BUG-01 | TC-AUTH-09, TC-AUTH-10, TC-ROUT-07, TC-SEC-03, TC-SEC-04, TC-SEC-05 | Missing, tampered, expired, or malformed credentials return 403 instead of 401. Access is denied correctly. |
| BUG-02 | TC-AUTH-11 | `POST /api/v2/token/refresh` rejects refresh tokens issued by the current login endpoint (`"Token has no type"`), while `/api/v2/token/verify` accepts them. |
