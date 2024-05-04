# Identity and Consent Management Examples

## Specifying One Purpose

---
**Note**

Access tokens content or structure are not part of the OAuth2 nor the OIDC standard. In [RFC7662](https://datatracker.ietf.org/doc/html/rfc7662) only the field `active` is REQUIRED.
`scope` and all other fields are optional. [JSON Web Token](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1) defines some common claims.
RFC7662 response values serve as an **example** how an access token might look like. These access tokens might contain additional field carrying what Camara needs regarding "purpose"

The scope `openid` is needed only in the request to specify that the request is an OpenId request. The scope `openid` is not needed in the access token.

---
**Note**

This document uses the response of the token-introspection endpoint as per RFC7662 to describe an access token.
This document does not say that the access token is self-contained or not.

---

### Purpose as a scope: Requesting one purpose, two scopes of the same API

#### OIDC authorization code flow

```
GET /authorize?
    response_type=code
    &scope=openid%20dpv%3AFraudPreventionAndDetection%20check-sim-swap%20retrieve-sim-swap-date
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
```

#### RFC9101 request object

```
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "response_type": "code",
  "client_id": "s6BhdRkqt3",
  "redirect_uri": "https://client.example.org/cb",
  "scope": "openid dpv:FraudPreventionAndDetection check-sim-swap retrieve-sim-swap-date",
  "state": "af0ifjsldkj",
  "nonce": "n-0S6_WzA2Mj",
  "max_age": 86400
}
```


### Purpose encoded in scope: Requesting one purpose, two scopes of the same API

#### OIDC authorization code flow

```
GET /authorize?
    response_type=code
    &scope=openid%20dpv%3AFraudPreventionAndDetection%23check-sim-swap%20dpv%3AFraudPreventionAndDetection%23retrieve-sim-swap-date
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
```

#### RFC9101 request object

```
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "response_type": "code",
  "client_id": "s6BhdRkqt3",
  "redirect_uri": "https://client.example.org/cb",
  "scope": "openid dpv:FraudPreventionAndDetection#check-sim-swap dpv:FraudPreventionAndDetection#retrieve-sim-swap-date",
  "state": "af0ifjsldkj",
  "nonce": "n-0S6_WzA2Mj",
  "max_age": 86400
}
```

### Purpose as a Authentication Request Parameter

```
GET /authorize?
    response_type=code
    &purpose=dpv%3AFraudPreventionAndDetection
    &scope=openid%20check-sim-swap%20retrieve-sim-swap-date
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
```

#### RFC9101 request object

```
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "response_type": "code",
  "client_id": "s6BhdRkqt3",
  "redirect_uri": "https://client.example.org/cb",
  "purpose": "dpv:FraudPreventionAndDetection"
  "scope": "openid check-sim-swap retrieve-sim-swap-date",
  "state": "af0ifjsldkj",
  "nonce": "n-0S6_WzA2Mj",
  "max_age": 86400
}
```

### Use Rich Authorization Request to convey one purpose

```
GET /authorize?
    response_type=code
    &authorization_details=%5B%7B%22type%22%3A%22org.camaraproject.simswap%22%2C%22purpose%22%3A%22dpv%3AFraudPreventionAndDetection%22%7D%5D
    &scope=openid%20check-sim-swap%20retrieve-sim-swap-date
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
```

#### RFC9101 request object

```
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "response_type": "code",
  "client_id": "s6BhdRkqt3",
  "redirect_uri": "https://client.example.org/cb",
  "authorization_details": "[{"type":"org.camaraproject.simswap","purpose":"dpv:FraudPreventionAndDetection"}]"
  "scope": "openid check-sim-swap retrieve-sim-swap-date",
  "state": "af0ifjsldkj",
  "nonce": "n-0S6_WzA2Mj",
  "max_age": 86400
}
```


### [RFC7662](https://datatracker.ietf.org/doc/html/rfc7662) response for **purpose**

#### Access Token Variant 1 [RFC7662](https://datatracker.ietf.org/doc/html/rfc7662) response on introspecting an access token issued by the above requests

**Note**: The RS might have to split `purpose` and `technical-scope`.

```
{
  "active": true,
  "client_id": "s6BhdRkqt3",
  "username": "jdoe",
  "scope": "dpv:FraudPreventionAndDetection#check-sim-swap dpv:FraudPreventionAndDetection#retrieve-sim-swap-date",
  "sub": "Z5O3upPC88QrAjx00dis",
  "aud": "https://protected.example.net/resource",
  "iss": "https://server.example.com/",
  "exp": 1419356238,
  "iat": 1419350238
}
```

#### Access Token Variant 2 [RFC7662](https://datatracker.ietf.org/doc/html/rfc7662) response on introspecting an access token issued by the above requests

**Note**: The value of the `technical-scope` is an array of `purpose`, so one `technical-scope` can have different `purpose` values.

```
{
  "active": true,
  "client_id": "s6BhdRkqt3",
  "username": "jdoe",
  "scopes": {
    "check-sim-swap": ["dpv:FraudPreventionAndDetection"],
    "retrieve-sim-swap-date": ["dpv:Advertising"]
  },
  "sub": "Z5O3upPC88QrAjx00dis",
  "aud": "https://protected.example.net/resource",
  "iss": "https://server.example.com/",
  "exp": 1419356238,
  "iat": 1419350238
}
```


## Specifying two purpose

---
**Note**

These following examples are only here now to demonstrate extensibility of the different options in requesting `purpose`.
Please ignore for now otherwise.

---

### Requesting two purpose, two scopes of the same API

#### OIDC authorization code flow

```
GET /authorize?
    response_type=code
    &scope=openid%20dpv%3AFraudPreventionAndDetection%23check-sim-swap%20dpv%Advertising%23retrieve-sim-swap-date
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
```

#### RFC9101 request object

```
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "response_type": "code",
  "client_id": "s6BhdRkqt3",
  "redirect_uri": "https://client.example.org/cb",
  "scope": "openid dpv:FraudPreventionAndDetection#check-sim-swap dpv:Advertising#retrieve-sim-swap-date",
  "state": "af0ifjsldkj",
  "nonce": "n-0S6_WzA2Mj",
  "max_age": 86400
}
```

#### Option 1 [RFC7662](https://datatracker.ietf.org/doc/html/rfc7662) response on introspecting an access token issued by the above requests

**Note**: The RS might have to split `purpose` and `technical-scope`.

```
{
  "active": true,
  "client_id": "s6BhdRkqt3",
  "username": "jdoe",
  "scope": "dpv:FraudPreventionAndDetection#check-sim-swap dpv:Advertising#retrieve-sim-swap-date",
  "sub": "Z5O3upPC88QrAjx00dis",
  "aud": "https://protected.example.net/resource",
  "iss": "https://server.example.com/",
  "exp": 1419356238,
  "iat": 1419350238
}
```

#### Option 2 [RFC7662](https://datatracker.ietf.org/doc/html/rfc7662) response on introspecting an access token issued by the above requests

**Note**: The value of the `technical-scope` is an array of `purpose`, so one `technical-scope` can have different `purpose` values.

```
{
  "active": true,
  "client_id": "s6BhdRkqt3",
  "username": "jdoe",
  "scopes": {
    "check-sim-swap": ["dpv:FraudPreventionAndDetection"],
    "retrieve-sim-swap-date": ["dpv:Advertising"]
  },
  "sub": "Z5O3upPC88QrAjx00dis",
  "aud": "https://protected.example.net/resource",
  "iss": "https://server.example.com/",
  "exp": 1419356238,
  "iat": 1419350238
}
```



