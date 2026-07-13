# JWT structure decoded

A JWT is three Base64URL-encoded parts joined by dots: `header.payload.signature`. The header says how the token is signed, the payload holds the claims, and the signature proves the first two parts were not tampered with. Reading a JWT means splitting on the dots and decoding each part.

## The raw token

A JWT looks like one long string with two dots in it:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI0MiIsInJvbGUiOiJhZG1pbiIsImV4cCI6MTcxMDAwMDAwMH0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

Split it on the dots and you get three pieces.

## Part 1: the header

Decoding the first segment gives JSON describing the signing algorithm and token type:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

`alg` is the algorithm (here HMAC with SHA-256). The server uses this to know how to verify the signature.

## Part 2: the payload (claims)

Decoding the second segment gives the claims, the actual data about the user and the token:

```json
{
  "sub": "42",
  "role": "admin",
  "exp": 1710000000
}
```

`sub` is the subject (the user ID), `role` is a custom claim, and `exp` is the expiry as a Unix timestamp. Remember this is only encoded, not encrypted, so anyone can read it.

## Part 3: the signature

The signature is computed over the encoded header and payload using the secret key:

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

To verify, the server recomputes this with its own key and checks it matches. If someone edits the payload (say, changes `role` to `admin`), the recomputed signature will not match, and the token is rejected. That is why the claims can be trusted without a database lookup, even though they are readable by anyone.
