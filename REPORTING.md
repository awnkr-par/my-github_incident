# Responsible reporting and remediation

## Report context

A phishing / GitHub impersonation report was sent to GitHub Support. The acknowledgement referred to ticket **#4844052**. No final support decision or confirmed domain shutdown was available in the captured evidence.

## Suggested material for an abuse report

Provide the phishing email (with private recipient details removed), the suspicious URL, captured malicious HTML, a clear description of the OAuth token exfiltration destination, and the later `/login` response showing the JavaScript redirect. Keep source evidence separate from inference.

## If an actual GitHub password was entered

1. Navigate manually to `https://github.com` and change the password to a unique one.
2. Review active sessions and recent security events; revoke unrecognized access.
3. Review authorized OAuth / GitHub Apps and personal access tokens; revoke anything unauthorized.
4. Check repository collaborators, deploy keys, webhooks, Actions workflows, and organization access for changes.
5. If a password was reused, change it on the other affected services, beginning with email.
6. Enable and review strong two-factor authentication or passkeys.

Typing into a phishing form may expose credentials even when the site's later behavior changes. Merely loading a page does not on its own establish endpoint malware infection.

## Handling evidence safely

- Never commit full HAR captures containing `Cookie`, `Authorization`, access tokens, refresh tokens, device codes, or passwords.
- Preserve original evidence privately if needed, and publish only sanitized copies.
- Do not execute suspicious JS or revisit active phishing sites from a normal signed-in browser.
- Do not DDoS, probe authentication endpoints with other people's credentials, or attempt retaliation.

## Useful official entry points

- GitHub: `https://github.com`
- GitHub Support: `https://support.github.com/`
- GitHub security activity: `https://github.com/settings/security-log`
- GitHub authorized apps: `https://github.com/settings/applications`

These links are for defensive account review, not an assessment of the phishing operator.
