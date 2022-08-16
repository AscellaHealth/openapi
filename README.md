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
