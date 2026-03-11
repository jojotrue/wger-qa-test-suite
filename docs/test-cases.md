# Test Case Documentation

**Project:** wger Workout Manager – QA API Test Suite  
**Author:** Joanne Trujillo  
**Total Test Cases:** 50  
**Last Updated:** March 2026

---

## Authentication (10 tests)

| Test Case | Method | Endpoint | Scenario | Expected Result | Actual Result |
|-----------|--------|----------|----------|----------------|---------------|
| TC-AUTH-01 | POST | /api/v2/token/ | Valid credentials | 200 OK, access + refresh tokens returned | ✅ 200 OK |
| TC-AUTH-02 | POST | /api/v2/token/ | Wrong password | 401 Unauthorized | ✅ 401 |
| TC-AUTH-03 | POST | /api/v2/token/ | Non-existent user | 401 Unauthorized | ✅ 401 |
| TC-AUTH-04 | POST | /api/v2/token/ | Empty body | 400 Bad Request | ✅ 400 |
| TC-AUTH-05 | POST | /api/v2/token/ | SQL injection in username | 400/401 — injection not executed | ✅ 401 |
| TC-AUTH-06 | POST | /api/v2/token/refresh/ | Valid refresh token | 200 OK, new access token returned | ✅ 200 OK |
| TC-AUTH-07 | POST | /api/v2/token/refresh/ | Invalid refresh token | 401 Unauthorized | ✅ 401 |
| TC-AUTH-08 | POST | /api/v2/token/verify/ | Valid access token | 200 OK | ✅ 200 OK |
| TC-AUTH-09 | GET | /api/v2/routine/ | No token provided | 401 Unauthorized | ⚠️ 403 — see BUG-04 |
| TC-AUTH-10 | GET | /api/v2/routine/ | Tampered JWT | 401 Unauthorized | ⚠️ 403 — see BUG-04 |

---

## User Profile (3 tests)

| Test Case | Method | Endpoint | Scenario | Expected Result | Actual Result |
|-----------|--------|----------|----------|----------------|---------------|
| TC-USER-01 | GET | /api/v2/userprofile/ | Get own profile | 200 OK, user profile fields returned | ⚠️ 200 OK — see BUG-05 |
| TC-USER-02 | PATCH | /api/v2/userprofile/ | Update user profile | 200 OK | ⚠️ 405 — see BUG-02 |
| TC-USER-03 | PATCH | /api/v2/userprofile/ | Mass assignment — escalate to Trainer | 403 Forbidden or role unchanged | ⚠️ 405 — see BUG-02 |

---

## Routines (9 tests)

| Test Case | Method | Endpoint | Scenario | Expected Result | Actual Result |
|-----------|--------|----------|----------|----------------|---------------|
| TC-ROUT-01 | GET | /api/v2/routine/ | Authenticated list | 200 OK, array of routines | ✅ 200 OK |
| TC-ROUT-02 | POST | /api/v2/routine/ | Valid creation | 201 Created, ID returned | ✅ 201 Created |
| TC-ROUT-03 | GET | /api/v2/routine/{id}/ | Valid ID | 200 OK, routine object | ✅ 200 OK |
| TC-ROUT-04 | PATCH | /api/v2/routine/{id}/ | Valid update | 200 OK | ⚠️ 200 OK — see BUG-03 |
| TC-ROUT-06 | GET | /api/v2/routine/999999/ | Non-existent ID | 404 Not Found | ✅ 404 |
| TC-ROUT-07 | GET | /api/v2/routine/ | No auth token | 401 Unauthorized | ⚠️ 403 — see BUG-04 |
| TC-ROUT-08 | GET | /api/v2/routine/?limit=5&offset=0 | Pagination | 200 OK, paginated results | ✅ 200 OK |
| TC-ROUT-09 | DELETE | /api/v2/routine/{id}/ | Valid delete | 204 No Content | ✅ 204 |
| TC-ROUT-10 | DELETE | /api/v2/routine/{id}/ | Already deleted | 404 Not Found | ✅ 404 |

---

## Exercises — Public Endpoints (7 tests)

| Test Case | Method | Endpoint | Scenario | Expected Result | Actual Result |
|-----------|--------|----------|----------|----------------|---------------|
| TC-EX-01 | GET | /api/v2/exercise/ | No auth required | 200 OK, public list | ✅ 200 OK |
| TC-EX-02 | GET | /api/v2/exercise/{id}/ | Valid exercise ID | 200 OK, exercise object | ✅ 200 OK |
| TC-EX-03 | GET | /api/v2/exercise/999999/ | Non-existent ID | 404 Not Found | ✅ 404 |
| TC-EX-04 | GET | /api/v2/exercise/?category=10 | Filter by category | 200 OK, filtered results | ✅ 200 OK |
| TC-EX-05 | GET | /api/v2/exercise/?equipment=1 | Filter by equipment | 200 OK, filtered results | ✅ 200 OK |
| TC-EX-06 | GET | /api/v2/exercisecategory/ | Public categories list | 200 OK | ✅ 200 OK |
| TC-EX-07 | GET | /api/v2/muscle/ | Public muscles list | 200 OK | ✅ 200 OK |

---

## Workout Sessions (6 tests)

| Test Case | Method | Endpoint | Scenario | Expected Result | Actual Result |
|-----------|--------|----------|----------|----------------|---------------|
| TC-SESS-01 | GET | /api/v2/workoutsession/ | Authenticated list | 200 OK, array of sessions | ✅ 200 OK |
| TC-SESS-02 | POST | /api/v2/workoutsession/ | Valid session creation | 201 Created, ID returned | ✅ 201 Created |
| TC-SESS-03 | GET | /api/v2/workoutsession/{id}/ | Valid ID | 200 OK, session fields present | ⚠️ 200 OK — see BUG-01 |
| TC-SESS-04 | POST | /api/v2/workoutsession/ | Invalid date format | 400 Bad Request | ✅ 400 |
| TC-SESS-05 | GET | /api/v2/workoutsession/?date=2026-03-11 | Filter by date | 200 OK, filtered results | ✅ 200 OK |
| TC-SESS-06 | DELETE | /api/v2/workoutsession/{id}/ | Valid delete | 204 No Content | ✅ 204 |

---

##  Weight Log (4 tests)

| Test Case | Method | Endpoint | Scenario | Expected Result | Actual Result |
|-----------|--------|----------|----------|----------------|---------------|
| TC-WGHT-01 | GET | /api/v2/weightentry/ | Authenticated list | 200 OK, array of entries | ✅ 200 OK |
| TC-WGHT-02 | POST | /api/v2/weightentry/ | Valid entry | 201 Created | ✅ 201 Created |
| TC-WGHT-03 | POST | /api/v2/weightentry/ | Negative weight value | 400 Bad Request | ✅ 400 |
| TC-WGHT-04 | POST | /api/v2/weightentry/ | Missing date field | 400 Bad Request | ✅ 400 |

---

## Security Tests (7 tests)

| Test Case | Method | Endpoint | Scenario | Expected Result | Actual Result |
|-----------|--------|----------|----------|----------------|---------------|
| TC-SEC-01 | GET | /api/v2/routine/1/ | BOLA — access another user's routine | 403 or 404 | ✅ 404 |
| TC-SEC-02 | GET | /api/v2/weightentry/1/ | BOLA — access another user's weight entry | 403 or 404 | ✅ 404 |
| TC-SEC-03 | GET | /api/v2/routine/ | Expired JWT | 401 Unauthorized | ⚠️ 403 — see BUG-04 |
| TC-SEC-04 | GET | /api/v2/workoutsession/ | No Authorization header | 401 Unauthorized | ⚠️ 403 — see BUG-04 |
| TC-SEC-05 | GET | /api/v2/routine/ | Malformed auth header | 401 Unauthorized | ⚠️ 403 — see BUG-04 |
| TC-SEC-06 | POST | /api/v2/routine/ | XSS payload in routine name | 400 or payload sanitized | ✅ Pass |
| TC-SEC-07 | POST | /api/v2/routine/ | Oversized payload | 400 or 413 | ✅ Pass |

---

##  API Health & Structure (4 tests)

| Test Case | Method | Endpoint | Scenario | Expected Result | Actual Result |
|-----------|--------|----------|----------|----------------|---------------|
| TC-HLTH-01 | GET | /api/v2/ | API root — no auth | 200 OK, endpoint list | ✅ 200 OK |
| TC-HLTH-02 | GET | /api/v2/ | HTTPS enforced | No HTTP downgrade | ✅ Pass |
| TC-HLTH-03 | OPTIONS | /api/v2/routine/ | Check allowed methods | 200 OK, methods listed | ✅ 200 OK |
| TC-HLTH-04 | GET | /api/v2/invalidendpoint/ | 404 handling | 404 Not Found, JSON response | ✅ 404 |

---

## Findings Summary

| ID | Test Cases Affected | Endpoint | Finding |
|----|---------------------|----------|---------|
| BUG-01 | TC-SESS-03 | GET /workoutsession/{id}/ | `workout` field absent from response — replaced by `routine`. Undocumented API change. |
| BUG-02 | TC-USER-02, TC-USER-03 | PATCH /userprofile/ | PATCH and PUT both return 405 — user profile endpoint appears read-only. |
| BUG-03 | TC-ROUT-04 | PATCH /routine/{id}/ | PATCH requires all fields — partial updates not supported. Violates REST PATCH convention. |
| BUG-04 | TC-AUTH-09, TC-AUTH-10, TC-ROUT-07, TC-SEC-03, TC-SEC-04, TC-SEC-05 | Multiple | API returns 403 Forbidden instead of 401 Unauthorized for unauthenticated requests. Deviates from REST standard. |
| BUG-05 | TC-USER-01 | GET /userprofile/ | Response is a flat object, not a paginated list. Fields `id` and `trainer` absent from response schema. |
