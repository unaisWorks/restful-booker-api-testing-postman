# Restful Booker API — Portfolio Test Suite

> A production-style Postman collection covering CRUD operations, negative testing, security boundaries, and end-to-end integration flows for the [Restful Booker API](https://restful-booker.herokuapp.com).

---

## 📁 Collection Structure

| Folder | Purpose |
|--------|---------|
| `00 - Authentication` | Token generation — run this first |
| `01 - Health Check` | Smoke test to confirm API is reachable |
| `02 - GET Bookings` | List all, filter by name/date, invalid ID, malformed date |
| `03 - POST Create Booking` | Valid creation, empty fields, whitespace-only, no-auth |
| `04 - PUT Full Update` | Full replacement, invalid payload, auth boundary |
| `05 - PATCH Partial Update` | Partial update + GET verification |
| `06 - DELETE Booking` | Delete + 404 verification, non-existent ID, no-auth |
| `07 - Integration Flows` | 3 chained end-to-end scenarios |

**Total requests: 35 | Total test assertions: 90+**

---

## 🧪 Test Coverage

### Functional
- All CRUD operations (GET, POST, PUT, PATCH, DELETE)
- Query parameter filtering (firstname, lastname, checkin, checkout)
- Response schema validation using JSON Schema (AJV)
- Field-level assertions (type, value, format)
- Response time assertions (< 2000ms)
- Content-Type header validation

### Negative / Edge Cases
- Invalid booking ID (0, 999999999)
- Empty string fields
- Whitespace-only field values
- Malformed date format (non-ISO strings)
- Mismatched name filter combinations

### Security
- All write operations (POST body, PUT, PATCH, DELETE) tested without auth token → assert 403
- Credentials stored in environment as `secret` type — not in collection

### Integration Flows
| Flow | Scenario |
|------|---------|
| Flow 01 | Create → PATCH update → GET verify → DELETE cleanup |
| Flow 02 | Create → DELETE → GET verify 404 |
| Flow 03 | GET existing booking ID → PATCH → GET verify persistence |

---

## 🚀 How to Run

### In Postman
1. Import `restful_booker_portfolio_collection.json`
2. Import `restful_booker_environment.json`
3. Select **Restful Booker — Portfolio Environment** from the environment dropdown
4. Run folder `00 - Authentication` first
5. Run remaining folders in order (or use Collection Runner)

### Via Newman (CLI)

```bash
# Install Newman and HTML reporter
npm install -g newman newman-reporter-htmlextra

# Run with CLI output
newman run restful_booker_portfolio_collection.json \
  -e restful_booker_environment.json \
  --reporters cli

# Run with HTML report
newman run restful_booker_portfolio_collection.json \
  -e restful_booker_environment.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export report.html
```

### Via GitHub Actions (CI/CD)

```yaml
# .github/workflows/api-tests.yml
name: API Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 8 * * *'   # Daily at 8 AM UTC

jobs:
  api-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Newman
        run: npm install -g newman newman-reporter-htmlextra

      - name: Run API Tests
        run: |
          newman run restful_booker_portfolio_collection.json \
            --env-var "baseUrl=https://restful-booker.herokuapp.com" \
            --env-var "username=${{ secrets.API_USERNAME }}" \
            --env-var "password=${{ secrets.API_PASSWORD }}" \
            --reporters cli,htmlextra \
            --reporter-htmlextra-export newman-report.html

      - name: Upload HTML Report
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: newman-report
          path: newman-report.html
```

> **Security note:** Never commit credentials. Use GitHub Secrets for `API_USERNAME` and `API_PASSWORD` and inject them via `--env-var` in CI.

---

## 🔑 Variables Reference

### Environment Variables (environment file)
| Variable | Description |
|----------|-------------|
| `baseUrl` | API base URL |
| `username` | Admin username (secret) |
| `password` | Admin password (secret) |

### Collection Variables (auto-managed at runtime)
| Variable | Set By | Used By |
|----------|--------|---------|
| `authToken` | 00 - Auth | All write operations |
| `existingBookingId` | 02 - GET All | 02 - GET by ID |
| `createdBookingId` | 03 - POST Create | 04 PUT, 05 PATCH, 06 DELETE |
| `patchedFirstname` | 05 - PATCH | 05 - GET Verify |
| `int1_bookingId` | Flow 01 Step 1 | Flow 01 Steps 2-4 |
| `int2_bookingId` | Flow 02 Step 1 | Flow 02 Steps 2-3 |
| `int3_bookingId` | Flow 03 Step 1 | Flow 03 Steps 2-3 |

---

## 🛠 Tech Stack

- **Postman** (Collection v2.1)
- **Newman** — CLI runner
- **newman-reporter-htmlextra** — HTML reports
- **AJV (Ajv)** — JSON Schema validation inside Postman sandbox
- **GitHub Actions** — CI/CD pipeline (see `.github/workflows/`)

---

## 📝 API Under Test

- **API:** [Restful Booker](https://restful-booker.herokuapp.com) by Mark Winteringham
- **Purpose:** Public practice API for API testing training
- **Docs:** https://restful-booker.herokuapp.com/apidoc/

---

## 👤 Author

**[Your Name]**  
QA Engineer | 7 Years Experience  
[LinkedIn](https://linkedin.com) · [GitHub](https://github.com)

