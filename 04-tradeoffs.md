# Tradeoffs

The core tradeoff is state: sessions keep authoritative state on the server (easy to control, needs a lookup), while JWTs push state into a signed token (scales without a store, hard to revoke). Every other difference flows from that one distinction. Here are the tradeoffs that actually matter.

## Revocation

Sessions win. To log a user out or ban them, the server deletes or updates the session record and the change takes effect on the very next request. JWTs are stateless, so a valid token stays valid until it expires; there is nothing to delete. Teams work around this with short expiry times plus refresh tokens, or by adding a server-side blocklist of revoked tokens, but the blocklist reintroduces exactly the server-side state JWTs were meant to avoid.

## Scaling

JWTs win here for distributed systems. Any server holding the signing key can verify a token with no shared store, which suits many stateless backend instances and authentication that spans separate services. Sessions require every server to reach a shared session store or to pin each user to one server. For a single app this is trivial; across many services it is more friction.

## Size on the wire

Sessions win. The session cookie carries only a short opaque ID, a few dozen bytes. A JWT carries all its claims plus a signature and travels on every request, often several hundred bytes or more. On high-volume APIs that overhead adds up, and oversized tokens can bump against header size limits.

## XSS and CSRF exposure

This depends on storage, and it is where JWTs are often used less safely.

- A session cookie set with `HttpOnly`, `Secure`, and `SameSite` is unreadable by JavaScript, which blunts token theft via XSS, but cookies are auto-sent, so CSRF protection is needed.
- A JWT stored in `localStorage` is readable by any script on the page, so a single XSS flaw leaks the token outright. Storing the JWT in an `HttpOnly` cookie instead removes that exposure but then behaves like a cookie and needs CSRF defenses, which erases part of the reason people chose JWTs.

There is no free lunch: you trade CSRF risk (cookies) against XSS token-theft risk (localStorage), and you must handle whichever you take on.
