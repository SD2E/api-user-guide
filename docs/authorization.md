---
layout: page
title: Authorization with the SD2E Tenant
tagline:
---

Authorization is handled with OAuth2 authorization tokens. You can generate
a token and save it to your ~/.agave/current configuration file by executing
the following command:
```
% auth-tokens-create -S
API password:   # enter TACC password

Token for sd2e:wallen successfully refreshed and cached for 14400 seconds
5836ffcf4ta4a3638df7fde7adf6159ab
```

The token is active for 4 hours (14400 seconds), at which point it expires. You
can check the status of your token by performing:
```
% auth-check

tenant: sd2e
username: wallen
time left: 14217 seconds
expires at: Thu Sep 21 16:40:51 CDT 2017
```

When your token expires, there is no need to generate a new token. It can be
refreshed by using the command:
```
% auth-tokens-refresh

Token for sd2e:wallen successfully refreshed and cached for 14400 seconds
d6f5da11v69ad337f9gaf1926487f9ec4
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
