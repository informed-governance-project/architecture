## Portal

https://github.com/informed-governance-project/portal

The portal can act as reverse proxy which will auto sets a custom Auth Header
in the request of the users.

In consequence a successful authentication to the portal will automatically
authenticate the user in MONARC (and other compatible tools).

Since the portal will lead to different tools (MONARC, incident notification
system, MISP, etc.), it is better to manage users and permissions at the portal
level.


Features:

- acts as a reverse proxy, capability to inject custom HTTP headers;
- authentication to the portal (2FA - see FIDO authentication)
- provide access to admin views;
- management of the home page (adapt the branding, texts, logos, etc.); 
- management of the users directory (tokens, permissions and roles, etc.);
- provide an API in order to let tools (MONARC, MISP, etc.) query the users
  directory.


Adaptation to the branding is at the portal level, not in MONARC.
