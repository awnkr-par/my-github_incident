# Indicators of compromise / investigation (IOCs)

These are **investigation indicators**, not standalone proof that every referenced host or IP is malicious. Domains below are rendered as code to avoid accidental navigation.

| Indicator | Context |
| --- | --- |
| `my-github.com` | Impersonation domain |
| `my-github.com/SDRVuhYw` | URL included in fake sign-in email |
| `my-github.com/login` | HTTP 200 HTML later used to redirect to real GitHub |
| `github.my-github.com` | Asset hostname in fake homepage |
| `api.my-github.com` | API hostname referenced in fake homepage |
| `collector.my-github.com` | Analytics hostname referenced in fake homepage |
| `docs.my-github.com` | Documentation hostname referenced in fake homepage |
| `193.32.204.199:443` | Remote address reported by browser for the /login document |
| `my-github.com:8443/t` | Token exfiltration destination hardcoded in injected JavaScript |
| `Ov23ctrqCqZ79UaYKwyq` | OAuth client ID string present in payload; ownership not verified |
| `__vh_oauth` | Browser localStorage/window flag used by payload |
| `vh_device` | Device-code cookie name used by payload |
| `/vh_beacon` | Relative beacon endpoint used by payload |
| `/login/device/code` | Relative device authorization endpoint |
| `/login/oauth/access_token` | Relative token polling endpoint |
| `95.199.84.125` | IP **claimed in phishing email**, not independently verified as the login source |

## Caveats

- The Moscow IP and claimed device were part of the phishing email and should **not** be treated as authenticated security-log data.
- The browser's remote IP is a server endpoint observation, not attribution to an individual.
- Relative API paths on `my-github.com` do not automatically reach `github.com`; server-side proxying was not verified.
- `ct.captcha-delivery.com` and `geo.captcha-delivery.com` appeared as DataDome CAPTCHA resources; **do not categorize the CAPTCHA provider as an IOC solely because it appeared on the page**.

## Safe sharing

Use these indicators for reporting and defensive investigation only. Remove query parameters if they carry tokens, authentication codes, email addresses, or other sensitive values.
