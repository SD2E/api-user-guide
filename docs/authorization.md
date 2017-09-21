---
layout: page
title: Authorization with the SD2E Tenant
tagline:
---

Authorization is handled with OAuth2 authorization tokens. You can generate
a token and save it to your ~/.agave/current configuration file by executing
the following command:
```
%auth-tokens-create -S
Password:   # enter TACC password
```

The token is active for 4 hours (14400 seconds), at which point it expires. You
can check the status of your token by performing:
```
% auth-check
```

When your token expires, there is no need to generate a new token. It can be
refreshed by using the command:
```
% auth-tokens-refresh
```

<br>
#### Troubleshooting

If you observe authorization errors with Agave, then first try generating a
new token with:
```
% auth-tokens-create -S
```

If errors persist, generate a new client:
```
% clients-create -S
```

---
Return to the [API Documentation Overview](../index.md)
