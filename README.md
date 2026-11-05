# AscellaHealth API

Welcome to the AscellaHealth API.

The AscellaHealth API is REST based, uses JSON data format and is secured with HTTPS.

The base URL is:
- UAT: `https://uat-api.ascellahealth.com`
- Production: `https://api.ascellahealth.com`

To see a list of all available endpoints, please refer to our API Endpoints page. The documentation also lets you "try it out" on each endpoint directly within the page.
- <a href="https://uat-api.ascellahealth.com/swagger/index.html" target="_blank">UAT API Endpoints page</a>
- <a href="https://api.ascellahealth.com/swagger/index.html" target="_blank">Production API Endpoints page</a>

## Authentication

All API resources require a valid access token for authentication.

Once you have obtained an access token, you must use HTTP Bearer Authentication, as defined in <a href="https://www.rfc-editor.org/rfc/rfc6750.html" target="_blank">RDC6750</a>, to authenticate when sending requests to the API.

### Authenticating
To authenticate and obtain the Access Token, you should have the Client Id and Client Secret in hands.

An HTTP GET request should be sent along with the Client Id and Client Secret in order to obtain the access token.

The access token is only valid for a finite period of time.

Using an expired token will cause operations to fail. This value is returned in the `expires_in` property (in seconds).

Request:

```markdown
curl --location 'https://api.ascellahealth.com/v1/connect/token'
--header 'Content-Type: application/x-www-form-urlencoded'
--data-urlencode 'grant_type=client_credentials'
--data-urlencode 'client_id={CLIENT_ID}'
--data-urlencode 'client_secret={CLIENT_SECRET}'
```

Response:

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
  "expires_in": 3600,
  "token_type": "Bearer"
}
```

### Access Token Usage
The method for sending as access token is by using an Authorization Request Headr where the access token is sent in the HTTP request header.

```markdown
curl -X GET "https://api.ascellahealth.com/v1/client-accounts" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
```

## HTTP Response
AscellaHealth API uses conventional HTTP response codes to indicate the success or failure of an API request.

- In general, codes in **2xx** range indicate success.
- Codes in **4xx** range indicate an error that failed given the information provided (e.g., a required parameter was omitted, resource not found, wrong JSON format, etc.).
- Some **4xx** errors that could be handled programmatically include an error message that briefly explains the error reported.
- Codes in the **5xx** range indicate an error with AscellaHealth's servers (these are rare).

###  HTTP Status Code Summary

| Code | Description |
| --- | --- |
| **200 - OK** | Everything worked as expected. |
| **400 - Bad Request** | The request was unacceptable, often due to missing a required parameter. |
| **401 - Unauthorized** | No valid API key provided. |
| **403 - Forbidden** | The API key or IP Address doesn't have permissions to perform the request. |
| **404 - Not Found** | The requested resource doesn't exist |
| **409 - Conflict** | The request conflicts with another request (for example, a request with the same `Idempotency-Key` is still being processed). |
| **422 - Unprocessable Content** | The `Idempotency-Key` was already used with a different request body. |
| **429 - Too Many Request** | Too many requests hit the API too quickly or quota exceeded. |
| **500,502,503,504 - Server Errors** | Something went wrong on AscellaHealth's end (these are rare). |

## Idempotency

To retry a request safely after a timeout or network failure, send an `Idempotency-Key` header. If we already processed a request with that key, we return the original response instead of executing it again.

| Endpoint | Idempotency-Key |
| --- | --- |
| `PUT /v1/freshworks/referrals` | Optional |

Without the header, the endpoint behaves as before. Endpoints not listed here ignore the header. The header is also available as an input field on the endpoint in the API Endpoints (Swagger) page.

```markdown
curl -X PUT "https://api.ascellahealth.com/v1/freshworks/referrals"
-H "Authorization: Bearer {ACCESS_TOKEN}"
-H "Content-Type: application/json"
-H "Idempotency-Key: 3f6c1e52-8a47-4d0b-9b1e-2c5a7d9e4f10"
-d '{ ...referral payload... }'
```

### Using the key

- Send a unique key per referral, once per request: 8 to 255 visible ASCII characters. We recommend a random UUID v4.
- To retry, resend the **same key and a byte-identical body**.
- Keys are scoped to your client account and kept for 24 hours.

### Responses

| Situation | Response |
| --- | --- |
| New key | Processed normally. |
| Same key and body, already completed | Original status and body, with header `Idempotent-Replayed: true`. Nothing is executed again. |
| Same key, different body | **422** |
| Same key, first request still running | **409** with `Retry-After` (seconds). Wait, then retry. |
| Malformed or repeated key | **400** |
| First attempt failed (4xx or 5xx) | Nothing is stored. Retry with the same key. |

The `400`, `409` and `422` errors are returned as problem details (RFC 9457) with a distinct `type`, so you can tell them apart from the endpoint's own validation errors.

> **Note:** Duplicate protection is best-effort, not an exactly-once guarantee. In rare failure scenarios a retry may still create a duplicate.

> **Rate limits:** Retried, replayed and rejected (409/422) requests count toward the [Limits and Quotas](#limits-and-quotas) like any other request.

## Limits and Quotas
This section describes the limits and quotas of requesting the AscellaHealth's APIs.

| Period | Max Requests |
| --- | --- |
| **1 minute** | 120 |
