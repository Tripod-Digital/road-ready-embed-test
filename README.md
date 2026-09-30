# Road Ready embed test host

A static page that plays a partner's booking page on a **different site** than Road Ready, to test
the embedded guide end to end (road-ready-nz-web issue #159). Published with GitHub Pages:

**https://tripod-digital.github.io/road-ready-embed-test/**

`*.github.io` is on the Public Suffix List, so this page is a separate *site* to the browser, not
just a separate origin. That makes it the place to test what the same-site demo page
(`https://staging.roadreadynz.com/developers/embed-demo`) cannot:

- `frame-ancestors`: the guide appears only when this origin is registered for the agency's partner;
- postMessage targeting: the guide posts only to a registered origin;
- storage partitioning and third-party cookie rules (anonymous sign-in, stored code) in a real
  cross-site frame, in Safari, Firefox and Chrome.

## Use

Open the page. It frames `https://staging.roadreadynz.com/embed/REN4?lang=en` (the staging demo
agency) and logs every message. When the traveller passes the quiz, the code lands in the Booking
card and, unless "Confirm receipt automatically" is off, the page confirms it so the guide shows
Done. With it off, the guide shows the traveller their code after 5 seconds; "Confirm receipt" then
switches it to Done.

URL parameters: `guide` (the Road Ready origin), `agency`, `lang`, `ref`, `auto=0`.

A static page holds no key, so it cannot verify. The page prints the `curl` for
`POST /api/partner/booking-codes/verify`; run it with a partner key of the agency.

## Registration

The origin `https://tripod-digital.github.io` must be in the embed origins of the agency's partner,
once per environment (road-ready-nz-web, `docs/release.md`, "Embedded guide"):

```bash
FIREBASE_DATABASE_ID=road-ready-nz-staging-database node scripts/createPartner.mjs \
  --id roadready-demo --set-embed-origins https://staging.roadreadynz.com,https://tripod-digital.github.io --apply
```

Without it the browser refuses the frame: that is the negative test.

## Maintenance

The page has no build step and no dependencies. It follows the host snippet in
`docs/partner-api-guide.md` ("Embed the guide"); change both together.
