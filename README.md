# kernhendricks.com

Portfolio site for Kern Hendricks — Documentary Filmmaker & Journalist.

Built with plain HTML/CSS/JS. Hosted on GitHub Pages.

## Deployment

This repo is set up for GitHub Pages. Any push to `main` automatically publishes the site.

## Custom Domain Setup

1. In this repo, go to **Settings → Pages**
2. Under "Custom domain", enter: `kernhendricks.com`
3. Click Save — GitHub will create a `CNAME` file automatically
4. In your Cloudflare DNS dashboard, add these records. Set them to **DNS only
   (grey cloud) at first** — GitHub Pages needs an unproxied apex to provision
   its Let's Encrypt certificate:

| Type  | Name | Content                 | Proxy (initial) |
|-------|------|-------------------------|-----------------|
| A     | @    | 185.199.108.153         | DNS only (grey) |
| A     | @    | 185.199.109.153         | DNS only (grey) |
| A     | @    | 185.199.110.153         | DNS only (grey) |
| A     | @    | 185.199.111.153         | DNS only (grey) |
| CNAME | www  | chaoticabacus.github.io | DNS only (grey) |

5. Back in GitHub Pages settings, check **Enforce HTTPS** once DNS propagates
   and the certificate is issued (usually 10–30 mins).
6. **Then switch the records to Proxied (orange cloud)** — this is the live
   state. It enables Cloudflare's CDN, DDoS protection, Web Analytics, and the
   response-header hardening below. In Cloudflare:
   - **SSL/TLS → Overview:** set the encryption mode to **Full (strict)**.
     Do **not** use Flexible — it causes infinite redirect loops with GitHub Pages.
   - **SSL/TLS → Edge Certificates:** enable **Always Use HTTPS** and **HSTS**
     (`max-age=31536000; includeSubDomains`).

> ⚠️ **The live site is proxied (orange cloud).** Don't leave the records on grey
> cloud past the initial cert provisioning — grey cloud disables the CDN,
> Cloudflare Web Analytics, and the security headers below.

## Security headers

`Content-Security-Policy` and `Referrer-Policy` ship in `index.html` (in the
`<head>`). The header-only protections (which a `<meta>` tag cannot deliver) are
set in Cloudflare — the proxy must be on. Go to **Rules → Transform Rules →
Modify Response Header**, and *set static* values for all incoming requests:

| Header | Value |
|--------|-------|
| `X-Content-Type-Options` | `nosniff` |
| `X-Frame-Options` | `SAMEORIGIN` |
| `Referrer-Policy` | `strict-origin-when-cross-origin` |
| `Permissions-Policy` | `camera=(), microphone=(), geolocation=(), payment=(), usb=()` |

When adding new photos, strip their metadata first so camera serial numbers and
capture timestamps aren't published:

```bash
exiftool -all= -tagsfromfile @ -Copyright -Artist photos/NEW-PHOTO.jpg
```

## Updating the site

Just edit `index.html` and push to `main`. Changes go live in ~60 seconds.
