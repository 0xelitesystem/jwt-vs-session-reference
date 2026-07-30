# JWT vs Session

A plain-language reference on the two common ways to keep a user logged in: server-side session cookies and stateless JSON Web Tokens (JWTs). It covers how each one works, the difference between storing session state on the server and carrying signed state in a token, the real tradeoffs (revocation, scaling, size, XSS and CSRF exposure), and how to decide which to use.

## The core idea

A session cookie stores a random ID in the browser and keeps the real user data on the server, while a JWT packs the user data into a signed token the client carries and the server verifies without a lookup. Sessions are easy to revoke and small on the wire but require server-side storage that every request must consult; JWTs scale statelessly and carry their own claims but are hard to revoke before they expire and must be stored and handled carefully to avoid theft. The right choice depends on whether you value instant revocation and simplicity (sessions) or stateless horizontal scaling across services (JWTs).

## What is inside

- [01-how-sessions-work.md](01-how-sessions-work.md) server-side session store and the session cookie.
- [02-how-jwts-work.md](02-how-jwts-work.md) the signed, stateless token and its three parts.
- [03-jwt-structure-decoded.md](03-jwt-structure-decoded.md) a decoded header.payload.signature example.
- [04-tradeoffs.md](04-tradeoffs.md) revocation, scaling, size, and XSS versus CSRF.
- [05-when-to-pick-which.md](05-when-to-pick-which.md) a practical decision guide.

## The stance

Neither approach is universally better. This reference treats sessions as the sane default for a single traditional web app and JWTs as the tool for stateless, distributed, or cross-service authentication, and it is honest that JWTs are frequently reached for when a session would have been simpler and safer.

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).

## License

MIT. Copyright 0xelitesystem 2026.
