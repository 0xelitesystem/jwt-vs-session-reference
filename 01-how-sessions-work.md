# How sessions work

A session keeps the user's login state on the server and gives the browser only a random session ID stored in a cookie. On each request the browser sends that ID back, and the server looks up the matching session record to know who the user is. The token in the browser is meaningless on its own; all the real data lives server-side.

## The flow

1. The user logs in with valid credentials.
2. The server creates a session record, for example `{ user_id: 42, role: "admin", created_at: ... }`, and stores it in a session store keyed by a random ID like `sess_9f3a...`.
3. The server sends that ID back in a `Set-Cookie` header.
4. The browser stores the cookie and automatically attaches it to every future request to that site.
5. On each request the server reads the ID, fetches the session record, and knows who the user is and what they can do.

## The session store

The session record lives somewhere the server can read on every request: in memory, in a database, or commonly in a fast key-value store like Redis. This store is the defining feature of sessions. Because the server owns the authoritative record, it can change or delete it at any moment. Logging a user out, revoking access, or updating their role takes effect immediately on the next request, because the server simply reads the updated record or finds none.

## What this buys you

Sessions are simple to reason about and easy to control. Revocation is instant, the cookie on the wire is tiny (just an opaque ID), and sensitive data never leaves the server. The cost is that the server must keep state: every request needs a lookup, and if you run many servers they must share the session store or route each user to the same server. For a single application this is rarely a real burden, which is why sessions remain the default choice for traditional web apps.
