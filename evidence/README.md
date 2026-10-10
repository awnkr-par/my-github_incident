# Sanitized evidence index

Evidence described here was obtained from user-provided browser captures and text. **Raw sensitive headers, cookies, passwords, and token values are intentionally excluded.**

## E-01 — Phishing email

- Subject/theme: “New sign-in detected”
- Salutation contained a reported outdated GitHub username
- Alleged sign-in: Moscow, Russia, October 10, 2026 at 14:27 UTC
- Link: `https://my-github.com/SDRVuhYw`
- The email's alleged login location/IP was not independently verified

## E-02 — GitHub-impersonating homepage

- GitHub-like HTML and React content
- `githubDomain` pointed to `https://my-github.com`
- `github.my-github.com` served as an asset hostname in HTML references
- Inline JavaScript included OAuth authorization automation and exfiltration instructions

## E-03 — OAuth payload indicators

```text
OAuth client identifier: Ov23ctrqCqZ79UaYKwyq
Requested scopes: repo workflow read:org
State flag: __vh_oauth
Cookie name: vh_device
Relative beacon path: /vh_beacon
Exfiltration destination: https://my-github.com:8443/t
```

**No real token or device-code values stored.**

## E-04 — CAPTCHA/restriction capture

- DataDome CAPTCHA script and iframe present
- An OAuth-oriented inline script was also present
- The presence of CAPTCHA resources was not itself malicious

## E-05 — /login network response

- URL: `https://my-github.com/login`
- Method: GET
- Status: 200 OK
- Remote address: `193.32.204.199:443`
- Content-Type: `text/html`
- Response: `top.location.href = 'https://github.com'`
- Referrer policy in markup: `no-referrer`

## E-06 — Later genuine GitHub page capture

The later saved page contained `githubDomain: https://github.com` and official GitHub asset references, with no identified copy of the previously captured OAuth payload. This supports the observed redirect outcome but does not establish that the suspicious domain was safe.

## E-07 — Support ticket acknowledgement

A submitted support report received acknowledgement referencing ticket `4844052`. The existence, contents, and resolution should be verified directly with official GitHub Support.

## Chain-of-custody limitations

These extracts preserve what was shared during the investigation but are not immutable originals accompanied by independent hashes and trusted timestamps. We cannot prove that a particular server response was served universally to all visitors or identify the site's operator.
