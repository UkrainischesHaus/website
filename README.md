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

The site is already live. The repository is **UkrainischesHaus/website**, GitHub Pages
deploys `main` from `/ (root)`, and the custom domain is `ukrainischeshaus.de`. There is
no build step — whatever is on `main` is what visitors get, usually within a minute.

To work on it, clone the repository:

```bash
gh repo clone UkrainischesHaus/website
# or: git clone https://github.com/UkrainischesHaus/website.git
cd website
```

Edit, then publish:

```bash
git add -A
git commit -m "Describe the change"
git push
```

Check the deploy finished with `gh api repos/UkrainischesHaus/website/pages/builds/latest
--jq .status` — it reports `building`, then `built`. If it reports `errored`, the previous
version stays live.

### One-time setup, already done

- **Settings → Pages** → Source: *Deploy from a branch*, Branch `main`, Folder `/ (root)`.
- **Custom domain** `ukrainischeshaus.de` — taken automatically from the `CNAME` file in
  this folder. Do not delete that file; GitHub uses it to route the domain to this repo,
  and removing it unsets the custom domain.
- `.nojekyll` disables Jekyll processing, so files are served exactly as committed.

### Still outstanding

- Tick **Enforce HTTPS** (Settings → Pages) once the certificate is issued. It can only be
  ticked after the DNS records below resolve, and issuance can take up to an hour.

## DNS at Namecheap

The domain is registered with **Namecheap** and uses Namecheap BasicDNS. Records are
edited under Domain List → `ukrainischeshaus.de` → **Manage** → **Advanced DNS**, in the
*Host Records* table. Leave the nameservers on the *Domain* tab set to Namecheap BasicDNS.

Delete the two rows Namecheap creates by default first, or they will conflict:

- `CNAME Record` · Host `www` · Value `parkingpage.namecheap.com.`
- `URL Redirect Record` · Host `@`

Then add nine records. Four **A** records point the apex at GitHub Pages, and the four
**AAAA** records do the same over IPv6:

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
CNAME www  ukrainischeshaus.github.io.
```

Click the green check on each row, then **Save All Changes**. Leave TTL on *Automatic*.

Propagation is usually minutes, occasionally a few hours. Check it with:

```bash
dig +short ukrainischeshaus.de A
dig +short www.ukrainischeshaus.de
```

The apex should return the four `185.199.*` addresses and `www` should return
`ukrainischeshaus.github.io.` — GitHub then redirects `www` to the apex on its own, since
the `CNAME` file names the apex.

Once that resolves, verify the domain in GitHub (organisation Settings → Pages →
*Verify domain*) to prevent takeovers; that adds a `_github-pages-challenge-UkrainischesHaus`
TXT record you also enter at Namecheap.

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
