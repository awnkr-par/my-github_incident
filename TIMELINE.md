# Incident timeline

All dates are **October 10, 2026** unless noted. This sequence reflects the investigator's reports and preserved materials, not independently timestamped server logs.

| Phase | Observation | Evidence strength |
| --- | --- | --- |
| Initial message | A “New sign-in detected” email alleged a Moscow login and directed the reader to `https://my-github.com/SDRVuhYw`. | Message supplied by recipient; allegations not validated |
| Identity clue | The salutation used `km7bnv`, which the recipient said was an old GitHub username. | Recipient report |
| First site inspection | Browser-visible CAPTCHA/restriction content included DataDome resources; a captured HTML response also included an OAuth-oriented script. | Source capture |
| Homepage capture | A GitHub-style HTML homepage replaced official GitHub references with `my-github.com` subdomains and included the injected OAuth script. | Source capture |
| Script analysis | Payload attempted GitHub device authorization and token exfiltration to port 8443; scoping and origin issues limited what could be proven. | Static analysis |
| Reporting | Recipient submitted an abuse/support report; automated acknowledgement referenced ticket #4844052. | Email supplied by recipient; resolution unknown |
| Changed navigation | Browser began landing on official `github.com` after opening the suspicious URLs. | Recipient report and later response capture |
| Network evidence | `GET https://my-github.com/login` returned HTTP **200 OK**, remote address `193.32.204.199:443`, content type `text/html`. | Browser Network capture |
| Redirect response | Response HTML instructed `top.location.href = 'https://github.com'` and set `no-referrer`. | Response body captured |
| Subsequent captures | A saved GitHub homepage showed legitimate GitHub domains and no identified copy of the earlier malicious script. | Static analysis of saved page; does not clear original site |

## Outstanding questions

- Was the suspect domain operating as a reverse proxy to GitHub at any point?
- Did any authorization sequence succeed?
- Was the redirect universal or dependent on the user, browser, time, or path?
- Did GitHub or an infrastructure provider take enforcement action?
- Were any real credentials submitted or reused?

**Do not infer the answers from a 200 response, a redirect, or code alone.**
