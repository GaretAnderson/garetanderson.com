# garetanderson.com

Static site for https://www.garetanderson.com, served by GitHub Pages from `main`.
It exists so the Google OAuth app **Anderson Home Automation** (Home Assistant's Nest
and Google Assistant SDK sign-in) can pass Google branding verification:

The front page (`index.html`) is a plain personal page that says nothing about the app.
The app pages live under an unlinked subpath and carry `noindex`, so casual visitors and
search engines don't see them. Anyone with the URL (for example from the consent screen)
can still open them, and this repo is public.

| Page | URL | Google requirement |
|---|---|---|
| App home | https://www.garetanderson.com/apps/home-automation/ | Explains the app's purpose, names the app, links the privacy policy |
| Privacy Policy | https://www.garetanderson.com/apps/home-automation/privacy.html | Discloses Google user data use, Limited Use statement |
| Terms of Service | https://www.garetanderson.com/apps/home-automation/terms.html | Optional link on the consent screen |

Don't link the app pages from the front page, and don't list them in `robots.txt`
(that would advertise the path).

Plain HTML and one stylesheet. No build step, no JavaScript, no trackers.
Tracking: https://github.com/GaretAnderson/homeassistant/issues/131

## Keep these in sync

- The consent screen **App name** must equal the name on the app home page: `Anderson Home Automation`.
- The scopes listed on the app home page and in the privacy policy must match what the OAuth
  client requests: `sdm.service`, `pubsub` (Nest) and `assistant-sdk-prototype` (Assistant SDK).
  If an integration is added to or removed from Home Assistant, update both pages.

## DNS (Namecheap → Advanced DNS)

The domain moved from Weebly hosting to GitHub Pages. Records:

| Type | Host | Value |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `garetanderson.github.io.` |
| TXT | `@` | `google-site-verification=…` (from Google Search Console) |

Delete the old Weebly records (`199.34.228.77`) for `@` and `www`. Keep the existing
email-forwarding MX/SPF records. Add a forwarder `privacy` → your inbox (Domain List →
Manage → Redirect Email), because the pages publish `privacy@garetanderson.com`.

After DNS resolves to GitHub, set the custom domain and HTTPS:

```powershell
gh api -X PUT repos/GaretAnderson/garetanderson.com/pages -f cname=www.garetanderson.com
# once the certificate is issued (minutes to an hour):
gh api -X PUT repos/GaretAnderson/garetanderson.com/pages -F https_enforced=true
```

If the certificate never issues, clear and re-set `cname` (set it to an empty string, then back).

## Google ownership verification

Use a **Domain** property in Google Search Console (https://search.google.com/search-console),
signed in with the Google account that owns the Google Cloud project. It verifies the whole
domain through the DNS TXT record above, independent of hosting. Then wait about 24 hours
before choosing "I have fixed the issues" on the branding verification screen.
