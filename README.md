# kernhendricks.com

Portfolio site for Kern Hendricks — Documentary Filmmaker & Journalist.

Built with plain HTML/CSS/JS. Hosted on GitHub Pages.

## Deployment

This repo is set up for GitHub Pages. Any push to `main` automatically publishes the site.

## Custom Domain Setup

1. In this repo, go to **Settings → Pages**
2. Under "Custom domain", enter: `kernhendricks.com`
3. Click Save — GitHub will create a `CNAME` file automatically
4. In your Cloudflare DNS dashboard, add these records:

| Type  | Name | Content          | Proxy |
|-------|------|------------------|-------|
| A     | @    | 185.199.108.153  | DNS only (grey cloud) |
| A     | @    | 185.199.109.153  | DNS only |
| A     | @    | 185.199.110.153  | DNS only |
| A     | @    | 185.199.111.153  | DNS only |
| CNAME | www  | chaoticabacus.github.io | DNS only |

5. Back in GitHub Pages settings, check **Enforce HTTPS** once DNS propagates (usually 10–30 mins)

## Updating the site

Just edit `index.html` and push to `main`. Changes go live in ~60 seconds.
