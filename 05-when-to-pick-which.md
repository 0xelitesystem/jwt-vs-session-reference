# When to pick which

Pick server-side sessions for a single traditional web application, and reach for JWTs when you genuinely need stateless verification across multiple services or many independent servers. When in doubt, sessions are the simpler and safer default, and JWTs are frequently chosen for problems a session would have solved with less risk.

## Choose sessions when

- You are building one web app with a browser front end and one backend.
- You need instant, reliable logout and revocation (banning users, ending stolen sessions immediately).
- You want the smallest attack surface and the least token-handling code to get wrong.
- You already run something like Redis or a database, so a session store is no burden.

For most conventional web apps this describes you, and sessions with a secure, `HttpOnly`, `SameSite` cookie are the right call.

## Choose JWTs when

- Multiple separate services must verify identity without sharing a session store.
- You have many stateless backend instances and want any of them to validate a request independently.
- You are issuing short-lived access tokens in an OAuth or API context, where JWTs are the established standard.
- Machine-to-machine or third-party API access needs a self-contained, verifiable credential.

## Practical guidance

If you choose JWTs, keep access-token lifetimes short (minutes, not days), pair them with a refresh-token flow, and plan for revocation before you need it (a blocklist or token versioning), because "we cannot log this user out right now" is a bad thing to discover during an incident. Store tokens deliberately and never in a way a stray script can read if you can avoid it. And be honest about scale: if you have one app and one backend, the stateless benefit of a JWT is mostly theoretical, and the operational simplicity plus instant revocation of a session is usually the better trade.
