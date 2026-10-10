# Analysis: injected OAuth device-flow script

## Summary

The suspicious HTML included an inline script designed to seek a GitHub-style logged-in avatar, request an OAuth device code, submit authorization forms, poll for an access token, then send any received token to a separate HTTPS endpoint on the suspect domain.

This demonstrates **attempted credential exfiltration**, not confirmed token theft.

## Sequence shown in the saved source

1. Check `window.__vh_oauth` and `localStorage.getItem("__vh_oauth")` to avoid repeating the flow.
2. Query for avatar selectors including `img[src*="avatars.githubusercontent.com"]` and `img.avatar-user`.
3. On a matching avatar, request a device authorization using OAuth client ID `Ov23ctrqCqZ79UaYKwyq` and scopes `repo workflow read:org`.
4. Fetch HTML from `/login/device`, scrape matching forms using regular expressions, and attempt to submit device and authorization values.
5. Poll `/login/oauth/access_token` for an access token.
6. If a token is returned, attempt exfiltration to `https://my-github.com:8443/t` with token and username in query parameters.

## Redacted exfiltration logic

The original code took the shape:

```js
// Nonfunctional excerpt; token content deliberately replaced.
if (td.access_token) {
  const destination = "https://my-github.com:8443/t";
  // Original code placed td.access_token in the URL query string
  // and attempted a fetch with { mode: "no-cors" }.
}
```

Putting tokens in URLs risks exposure in server/access logs and other telemetry. This is an explicit indicator of hostile design.

## Execution limitations

**Scoping bug:** In the delayed avatar-detection branch, `flowStart` is referenced where it is not defined in the surrounding scope, likely causing a `ReferenceError` if the avatar appears only after the first check.

**Origin issue:** Calls such as `fetch("/login/device/code")` address the **current origin**. When executed on `my-github.com`, they target that origin. No server-side reverse proxy was confirmed.

**Form parsing fragility:** Regex matching relies on particular form and input attribute layouts. Real auth pages and CSRF protections can break the sequence.

**Early persistent flag:** The script sets a `__vh_oauth` flag before verifying authorization success; this could suppress subsequent retries.

**No observed successful exchange:** Captured sources and reports did not show an actual issued access token or successful exfiltration request.

## Risk interpretation

If an attacker obtains an OAuth token with `repo` and `workflow` scopes, repository and workflow resources may be at risk within granted permissions. Here, only the attempt was shown. A separate login credential collection mechanism was not established from the observed logged-out homepage.

## Related evidence

See [sanitized source extracts](../evidence/README.md) and [the redirect analysis](redirect-behavior.md).
