# OAuth IMAP (XOAUTH2) Extension for EspoCRM

This repository contains a standalone EspoCRM extension that adds OAuth2 / XOAUTH2 support for IMAP (Google and Microsoft) and uses OAuth tokens for SMTP when enabled.

The packaged extension is expected at:

```
oauth-imap-0.1.0.zip
```

## Features

- OAuth2 / XOAUTH2 IMAP for Google and Microsoft
- OAuth2 callback handling, token storage, auto-refresh
- OAuth2-based SMTP (XOAUTH2 auth string)
- UI OAuth tab on Email Accounts and Group (Inbound) Email
- No core edits; all wiring uses supported hooks and handlers

## Requirements

- EspoCRM 9.1+
- PHP 8.2+
- Composer (for development build)

## Install (Production)

1. Upload the extension zip in EspoCRM:
   - Administration → Extensions → Install
   - Select `oauth-imap-0.1.0.zip`
2. Run Administration → Repair → Rebuild

## Configure OAuth (EspoCRM)

Go to Administration → OAuth IMAP Settings and set:

- **Google**
  - Client ID / Client Secret
  - Scopes: `https://mail.google.com/ offline_access`
  - Redirect URI: `https://<your-crm>/ ?entryPoint=oauthImapCallback`

- **Microsoft**
  - Client ID / Client Secret / Tenant ID
  - Scopes (exact):
    ```
    https://outlook.office365.com/IMAP.AccessAsUser.All
    offline_access openid profile email
    ```
  - Redirect URI: `https://<your-crm>/ ?entryPoint=oauthImapCallback`

## Entra (Microsoft) App Registration

1. Azure Portal → Entra ID → App registrations → New registration
2. Supported account types:
   - Single tenant (recommended)
3. Redirect URI (Web):
   ```
   https://<your-crm>/?entryPoint=oauthImapCallback
   ```
4. Certificates & secrets → New client secret
5. API Permissions (Microsoft Graph, Delegated):
   - `IMAP.AccessAsUser.All`
   - `offline_access`
   - `openid`
   - `profile`
   - `email`
6. Grant admin consent for the tenant

### IMAP Enabled (Microsoft)

IMAP must be enabled for the mailbox:

- Exchange Admin Center → Mailboxes → (user or shared mailbox) → Email apps → IMAP enabled

## Connect an Account

### Personal Email Account

Emails → Personal Email Accounts → New / Edit:

- Set IMAP host/port/security (e.g., `outlook.office365.com`, `993`, `SSL`)
- Set OAuth Provider and OAuth Email
- Save
- Go to OAuth tab → Connect OAuth

### Group (Inbound) Email

Administration → Group Email Accounts (Inbound Email):

- Set IMAP host/port/security
- Set OAuth Provider and OAuth Email
- Save
- Go to OAuth tab → Connect OAuth

## SMTP (OAuth)

SMTP uses the same OAuth token.

For SMTP to work:

- Ensure SMTP host/port/security are reachable from the server
- If your host blocks SMTP ports, use a relay (SendGrid/Mailgun/etc.) or an allowed port

## Troubleshooting

- If OAuth connects but IMAP fails:
  - Confirm the OAuth Email matches the mailbox
  - Ensure the authenticated user has Full Access if accessing a shared mailbox
  - Microsoft 365 Groups do not support IMAP
- If OAuth UI is missing:
  - Run Repair → Rebuild
  - Clear browser cache

## Development (Optional)

Install tooling and build:

```bash
npm install
npm run all
npm run sync
npm run extension
```

The local Espo instance is created in `site/` (admin / 1).

## Tests

```bash
vendor/bin/phpunit
```
