# How JWTs work

A JSON Web Token carries the user's data inside the token itself, signed by the server so it cannot be tampered with. The server does not store the token or look anything up; it verifies the signature on each request and trusts the claims inside. This is what "stateless" means: the server holds no per-session record, because the token is self-describing.

## The flow

1. The user logs in with valid credentials.
2. The server builds a set of claims, for example `{ "sub": "42", "role": "admin", "exp": 1710000000 }`.
3. The server signs those claims with a secret key (or a private key) to produce the token.
4. The client stores the token and sends it on each request, typically in an `Authorization: Bearer <token>` header.
5. The server verifies the signature. If it is valid and not expired, the server trusts the claims without any database lookup.

## Signed, not encrypted

A standard JWT is signed, not encrypted. Anyone holding it can read the claims (the payload is only encoded, not hidden). The signature guarantees the claims were issued by your server and have not been altered, but it does not conceal them. So never put secrets in a JWT payload, and treat the token like a bearer credential: whoever holds it is treated as the user until it expires.

## What this buys you

Because verification needs only the signing key and not a stored record, any server that knows the key can validate the token independently. That makes JWTs attractive for horizontally scaled backends and for authentication that spans multiple separate services, where a shared session store would be awkward. The price is the mirror image of the session's strength: since there is no server-side record to delete, a valid token stays valid until it expires, which makes instant revocation hard. That tradeoff is the heart of the comparison.
