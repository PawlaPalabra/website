# pawlapalabra.com — static site

The public pages for PawlaPalabra. Currently one thing that matters: the privacy
policy Apple fetches during review, at <https://pawlapalabra.com/privacy>.

It is **not** served by the backend on purpose. The ALB and its single EC2 target
could serve this fine, but that would tie the privacy URL's availability to the API's
availability, and Apple polls that URL during and after review. GitHub Pages keeps
the two independent. `api.pawlapalabra.com` stays on the ALB.

## Layout

| Path | Serves |
|---|---|
| `CNAME` | Custom domain for GitHub Pages |
| `index.html` | `/` — one-screen landing page |
| `privacy.html` | `/privacy` — GitHub Pages resolves extensionless URLs to `.html`, so this serves at `/privacy` with **no redirect**. A `privacy/index.html` directory would work too, but Pages 301s `/privacy` → `/privacy/`, and the URL in App Store Connect should not bounce. |

The policy's prose also lives at [`../privacy-policy.md`](../privacy-policy.md), next
to the code that justifies each claim. **Edit both together** and bump the
"Last updated" date in both.

## Publishing

1. Push the *contents of this directory* as the root of a **public** repo named
   `website`. Public matters: GitHub Pages on a private repo needs a paid plan.
2. Repo → Settings → Pages → Source `main` / `/ (root)`.
3. Set the custom domain to `pawlapalabra.com`, wait for the certificate to issue,
   then tick **Enforce HTTPS**.
4. At Porkbun, add for the apex an `ALIAS` → `pawlapalabra.github.io` (or GitHub's
   four A records if ALIAS misbehaves) and a `www` `CNAME` → the same target.
   Leave the `api` record and the ACM `_`-prefixed validation CNAMEs alone.
5. Porkbun → Email Forwarding: `support@pawlapalabra.com` → `rneviantsev@gmail.com`.
   MX records coexist with the apex ALIAS. Send one test mail — a contact address in
   a legal notice that silently blackholes is worse than none.

Verify: `curl -sI https://pawlapalabra.com/privacy` returns `200` with a valid
certificate and no redirect to a login.
