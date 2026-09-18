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

## Working on the site

Clone the repository — it already exists, so there is nothing to initialise:

```bash
gh repo clone UkrainischesHaus/website
# or: git clone https://github.com/UkrainischesHaus/website.git
cd website
```

There is no build step and no dependencies to install. To preview your changes, serve
the folder over HTTP and open <http://localhost:8000>:

```bash
python3 -m http.server 8000
```

Use a server rather than opening the `.html` files directly — `file://` breaks the
root-relative paths in `404.html` and blocks the web fonts.

Then commit and push:

```bash
git add -A
git commit -m "Describe the change"
git push
```

Pushing to `main` publishes. Whatever is on `main` is what visitors get, usually within
a minute. Check the deploy with:

```bash
gh api repos/UkrainischesHaus/website/pages/builds/latest --jq .status
```

It reports `building`, then `built`. If it reports `errored`, the previously published
version stays live — a bad push takes the site down only if it is valid but wrong.

### Things not to delete

- **`CNAME`** — GitHub reads it to route `ukrainischeshaus.de` to this repository.
  Deleting it unsets the custom domain and the site falls back to
  `ukrainischeshaus.github.io`.
- **`.nojekyll`** — disables Jekyll processing so files are served exactly as committed.
  Without it, anything starting with `_` or `.` stops being served.

### How it is deployed

GitHub Pages, *Deploy from a branch*: `main` at `/ (root)`, configured under the
repository's Settings → Pages. Custom domain `ukrainischeshaus.de`, HTTPS enforced,
certificate issued by Let's Encrypt and renewed automatically. Nothing needs to be
re-done; the settings below are recorded only so they can be rebuilt if the repository
is ever recreated.

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

### Verify the domain

Verifying stops anyone else claiming `ukrainischeshaus.de` on GitHub Pages if this repo is
ever deleted or renamed. Go to
<https://github.com/organizations/UkrainischesHaus/settings/pages> → **Add a domain**,
enter `ukrainischeshaus.de`, and GitHub shows a one-off token. It is generated per
organisation and domain, so it cannot be looked up anywhere else.

Add it at Namecheap as a **TXT Record**:

```
TXT   _github-pages-challenge-ukrainischeshaus   <token from GitHub>
```

GitHub displays the full record name, `_github-pages-challenge-ukrainischeshaus.ukrainischeshaus.de`.
Namecheap's *Host* field is relative to the domain, so enter only the part before it —
pasting the full name creates the record one level too deep and verification fails.

Check it with `dig +short TXT _github-pages-challenge-ukrainischeshaus.ukrainischeshaus.de`,
then click **Verify** in GitHub.

## Files

```
index.html  angebote.html  kontakt.html      German pages
index-uk.html  angebote-uk.html  kontakt-uk.html   Ukrainian pages
404.html                  error page, bilingual
CNAME                     custom domain for GitHub Pages
.nojekyll                 serve files as-is, no Jekyll processing
robots.txt  sitemap.xml    indexing
assets/css/modernist.css  design-system tokens and components
assets/css/site.css       brand colour overrides and base rules
assets/fonts/*.woff2      Onest, self-hosted (latin, latin-ext, cyrillic, cyrillic-ext)
assets/logo.png  logo-mark.png  favicon.png  apple-touch-icon.png
assets/photo-stand.jpg     photograph
assets/og-image.jpg        social share image, 1200×630
```

## Contact form

The form on `kontakt.html` / `kontakt-uk.html` posts to **Web3Forms**, which needs no
server and no account fee for basic use. The access keys are already set — line 78 of
each page:

```html
<input type="hidden" name="access_key" value="...">
```

The two pages use **different** keys, which lets you tell German submissions from
Ukrainian ones. Both must be registered and confirmed against
`ukrainischeshaus.kassel@gmail.com` at https://web3forms.com; an unconfirmed key fails
silently, with no error shown to the visitor. Send a test message from each published
page after a DNS change or a key change.

The form already includes a required GDPR consent checkbox linking to the privacy
policy, a hidden `botcheck` honeypot field, and `required` on name, email and message.
Web3Forms shows its own confirmation page after submitting; add
`<input type="hidden" name="redirect" value="https://ukrainischeshaus.de/index.html">`
if you would rather return to the site.

Note that the access keys are public — they sit in the page source, which is how
Web3Forms is designed to work. They identify the destination inbox, they are not
secrets, and they can be rotated at any time from the Web3Forms dashboard.

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
