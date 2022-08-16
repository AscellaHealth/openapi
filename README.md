## AscellaHealth API

Welcome to the AscellaHealth API.

The AscellaHealth API is REST based, uses JSON data format and is secured with HTTPS.

The base URL is: `https://api.ascellahealth.com/{version}`.

To see a list of all available endpoints, please refer to our <a href="https://api.ascellahealth.com/swagger/index.html" target="_blank">API Endpoints page</a>. The documentation also lets you "try it out" on each endpoint directly within the page.

### Authentication

All API resources require a valid access token for authentication.

Once you have obtained an access token, you must use HTTP Bearer Authentication, as defined in <a href="https://www.rfc-editor.org/rfc/rfc6750.html" target="_blank">RDC6750</a>, to authenticate when sending requests to the API.

#### Authenticating
To authenticate and obtain the Access Token, you should have the Client Id and Client Secret in hands.

An HTTP GET request should be sent along with the Client Id and Client Secret in order to obtain the access token.

The access token is only valid for a finite period of time.

Using an expired token will cause operations to fail. This value is returned in the `expires_in` property (in seconds).

Request:

```markdown
curl -X GET 'https://api.ascellahealth.com/v1/auth?grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}'
```

Response:

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
  "expires_in": 3600,
  "token_type": "Bearer"
}
```

#### Access Token Usage
The method for sending as access token is by using an Authorization Request Headr where the access token is sent in the HTTP request header.

```markdown
curl -X GET "https://api.ascellahealth.com/v1/client-accounts" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
```

### HTTP Response
AscellaHealth API uses conventional HTTP response codes to indicate the success or failure of an API request.

- In general, codes in **2xx** range indicate success.
- Codes in **4xx** range indicate an error that failed given the information provided (e.g., a required parameter was omitted, resource not found, wrong JSON format, etc.).
- Some **4xx** errors that could be handled programmatically include an error message that briefly explains the error reported.
- Codes in the **5xx** range indicate an error with AscellaHealth's servers (there are rare).

####  HTTP Status Code Summary
- **200 - OK** Everything worked as expected.
- **400 - Bad Request** The request was unacceptable, often due to missing a required parameter.
- **401 - Unauthorized** No valid API key provided.
- **403 - Forbidden** The API key or IP Address doesn't have permissions to perform the request.
- **404 - Not Found** The requested resource doesn't exist
- **409 - Conflict** The request conflicts with another request (perhaps due to using the same idempotent key).
- **429 - Too Many Request** Too many requests hit the API too quickly or quota exceeded.
- **500,502,503,504 - Server Errors** Something went wrong on AscellaHealth's end (there are rare).
