# ukrainischeshaus.de — GitHub Pages

Static site for **Ukrainisches Haus Kassel und Umgebung e. V.** No build step, no
JavaScript. Ten pages — five in German, the same five in Ukrainian.

| Page | German | Ukrainian |
| --- | --- | --- |
| Start | `index.html` | `index-uk.html` |
| Angebote | `angebote.html` | `angebote-uk.html` |
| Kontakt | `kontakt.html` | `kontakt-uk.html` |
| Impressum | `impressum.html` | `impressum-uk.html` |
| Datenschutz | `datenschutz.html` | `datenschutz-uk.html` |

## Publish

1. Create a repository (e.g. `uhku/website`) and push the **contents of this folder**
   to the repository root on branch `main`:

   ```bash
   git init
   git add .
   git commit -m "Website ukrainischeshaus.de"
   git branch -M main
   git remote add origin git@github.com:<org>/<repo>.git
   git push -u origin main
   ```

2. Repository → **Settings → Pages** → Source: *Deploy from a branch*,
   Branch: `main`, Folder: `/ (root)`. Save.

3. Same page, **Custom domain**: enter `ukrainischeshaus.de` and save. The `CNAME`
   file in this folder already contains that value, so this step should already be
   filled in after the first deploy.

4. Tick **Enforce HTTPS** once the certificate is issued (can take up to an hour).

## DNS at your domain registrar

For the apex domain `ukrainischeshaus.de` create four **A** records (and, if IPv6 is
offered, the four **AAAA** records) pointing at GitHub Pages:

```
A     @   185.199.108.153
A     @   185.199.109.153
A     @   185.199.110.153
A     @   185.199.111.153

AAAA  @   2606:50c0:8000::153
AAAA  @   2606:50c0:8001::153
AAAA  @   2606:50c0:8002::153
AAAA  @   2606:50c0:8003::153
```

And one **CNAME** for the `www` subdomain:

```
CNAME www  <org>.github.io.
```

Delete any existing A/AAAA/ALIAS records on `@` first. Propagation is usually minutes,
occasionally a few hours. Verify the domain in GitHub (Settings → Pages → *Verify domain*)
to prevent takeovers.

## Files

```
index.html  angebote.html  kontakt.html      German pages
index-uk.html  angebote-uk.html  kontakt-uk.html   Ukrainian pages
404.html                  error page, bilingual
CNAME                     custom domain for GitHub Pages
.nojekyll                 serve files as-is, no Jekyll processing
robots.txt  sitemap.xml    indexing
assets/css/modernist.css  design-system tokens and components
assets/fonts/*.woff2      Onest, self-hosted (latin, latin-ext, cyrillic, cyrillic-ext)
assets/css/site.css        brand colour overrides and base rules
assets/logo.png  logo-mark.png  favicon.png  apple-touch-icon.png
assets/photo-stand.jpg     photograph
assets/og-image.jpg        social share image, 1200×630
```

## Activate the contact form

The form on `kontakt.html` / `kontakt-uk.html` posts to **Web3Forms**, which needs no
server and no account fee for basic use.

1. Go to https://web3forms.com, enter `ukrainischeshaus.kassel@gmail.com` and confirm
   the address. You receive an access key.
2. In both `kontakt.html` and `kontakt-uk.html` replace the placeholder:

   ```html
   <input type="hidden" name="access_key" value="WEB3FORMS-ACCESS-KEY">
   ```

   with your real key. Until this is done, submissions fail.
3. Send a test message from the published page.

The form already includes a required GDPR consent checkbox linking to the privacy
policy, a hidden `botcheck` honeypot field, and `required` on name, email and message.
Web3Forms shows its own confirmation page after submitting; add
`<input type="hidden" name="redirect" value="https://ukrainischeshaus.de/index.html">`
if you would rather return to the site.

## Before you announce the domain

- **Have a lawyer or the association's advisor read `impressum.html` and
  `datenschutz.html`.** They are written to the usual German requirements
  (§ 5 DDG, § 18 MStV, GDPR Art. 13) with the association's real data, but they are a
  careful draft, not legal advice. In particular check the responsible person named
  under § 18 Abs. 2 MStV and the supervisory authority address.
- Both legal pages carry `<meta name="robots" content="noindex, follow">` — standard
  practice, they are reachable from every footer but not indexed.
- `angebote.html` still has no concrete times or rooms.

## Typography

The site uses **Onest**, self-hosted from `assets/fonts/` as a variable font — one
woff2 per subset, each covering weights 100–900. Four subsets are shipped (latin,
latin-ext, cyrillic, cyrillic-ext, ~88 KB total); browsers download only the ones a
page actually needs, so a German page never fetches the Cyrillic file.

The site makes **no third-party requests** when a page loads.

Onest replaced Archivo, which has no Cyrillic glyphs at all — the Ukrainian pages were
silently falling back to the visitor's system font. Every character used anywhere on
the site is verified to be inside one of the four shipped subsets.

## Optional improvements

- **Clean URLs.** Move each page into its own folder (`angebote/index.html`) so the
  address reads `ukrainischeshaus.de/angebote`. Update the internal links accordingly.
- **Photograph sizes.** Export 800px and 1600px versions and add `srcset` for mobile
  data use.
