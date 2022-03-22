I was looking for something like `aws sts get-caller-identity` for GCP's Python SDK. It doesn't look like they have a direct equivalent, but you can get similar details by doing the following.

```python
credentials, project_id = google.auth.default()
credentials.refresh(google.auth.transport.requests.Request())
response = requests.get("https://www.googleapis.com/oauth2/v1/tokeninfo?id_token=" + credentials.id_token)
pprint(response.json())
```

```json
{"audience": "764086051850-6qr4p6gpi6hn506pt8ejuq83di341hur.apps.googleusercontent.com",
 "email": "michael@sentry.io",
 "email_verified": True,
 "expires_in": 3599,
 "issued_at": 1647618851,
 "issued_to": "764086051850-6qr4p6gpi6hn506pt8ejuq83di341hur.apps.googleusercontent.com",
 "issuer": "https://accounts.google.com",
 "user_id": "101630805336827459447"}
```
