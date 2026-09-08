# complexdecisions.org — deploy notes

Single static file: `index.html`. No build step, no JS, no analytics, no forms.
Only external request: Google Fonts (EB Garamond). Everything else inline.

## Cloudflare Pages (recommended)
1. Cloudflare → Workers & Pages → Create → Pages → Upload assets → upload `index.html`.
2. Custom domain: add `complexdecisions.org` and `www.complexdecisions.org`.
3. Move DNS to Cloudflare (or add the CNAMEs Cloudflare gives you at Hostinger).
4. SSL/TLS → Full; Edge Certificates → Always Use HTTPS on.
5. Rules → Redirect: `www.complexdecisions.org/*` → `https://complexdecisions.org/$1` (301).

## Render (alternative)
New → Static Site → connect this repo, root directory `site`, publish directory `site`,
build command empty. Add custom domain, then point DNS as Render instructs.

## Email deliverability (do this before sending the contract from info@)
Add at the DNS provider that hosts the domain's records:
- SPF (TXT @):   the value your mailbox provider gives — for Hostinger mail:
                 `v=spf1 include:_spf.mail.hostinger.com ~all`
- DKIM (TXT):    copy the selector record from the mailbox provider's panel.
- DMARC (TXT _dmarc): `v=DMARC1; p=quarantine; rua=mailto:info@complexdecisions.org`
Verify with a test send to a Gmail address → "Show original" → SPF PASS, DKIM PASS, DMARC PASS.

## Partner login (internal — `/office/`, never linked from the public site)
`/office/` is the firm's internal desk: webmail, files, calendar. It is not linked anywhere and is
protected by Cloudflare Zero Trust Access (email one-time PIN, allowlist). Nothing on the site stores
credentials. Partners reach it by typing the URL.

1. Cloudflare → Zero Trust → Access → Applications → Add → Self-hosted.
   - Application name: Office
   - Domain: `complexdecisions.org`, Path: `office`  (protects `/office` and everything under it)
   - Session duration: 24 hours. App Launcher: off.
2. Policy: Action **Allow** → Include → **Emails** → the partners' exact addresses.
3. Authentication → Login methods: **One-time PIN** only.
4. Zero Trust → Settings → Custom Pages: organization name "Complex Decisions".
5. Test in a private window: `/office/` must prompt for email, send a PIN, and admit only listed addresses.

Before go-live, replace the `Files` link (`href="#"`) in `office/index.html` with the firm's drive or
data-room URL. Do not add `/office` to robots.txt (that would publish the path); the `noindex` meta and
Access already keep it out of search.
