# Analysis: later redirect behavior

## Captured document request

```http
GET https://my-github.com/login
HTTP/1.1 200 OK
Content-Type: text/html
Cache-Control: no-cache, no-store
Remote address observed in browser: 193.32.204.199:443
```

*Cookie and authorization headers omitted.*

The preserved response body was:

```html
<html>
  <head>
    <meta name='referrer' content='no-referrer'>
    <script>
      top.location.href = 'https://github.com';
    </script>
  </head>
  <body></body>
</html>
```

## Interpretation

- HTTP **200** is not an HTTP 3xx redirect.
- The page's JavaScript navigates the top-level browsing context to official `https://github.com`.
- The `no-referrer` meta tag requests that referrer information not be sent to the destination.
- The subsequent appearance of genuine GitHub resources in the browser is therefore explained without proving that the original suspicious URL was legitimate.
- The observed response contains no password form or embedded OAuth theft code **in this later response**.

## Not proven

We cannot determine whether this change resulted from a takedown, maintenance, cloaking, a timed campaign, or a decision by site operators. The original malicious HTML and later redirect are both relevant evidence.

## Privacy

An original request capture included `_gh_sess`, `_octo`, and `datadome` cookie values. Their names may appear in incident analysis, but **their values are deliberately not included** in this repository. Cookie names resembling GitHub cookies do not by themselves prove that the genuine `github.com` cookies crossed browser-origin boundaries.
