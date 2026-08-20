# Abdul Hadi — One-Page Site

Live at: (your Netlify URL goes here once deployed, e.g. abdul-hadi.netlify.app)

A single static page: positioning, what I'm building, and links to GitHub,
LinkedIn, my CV, and a booking link. Built as the FlyRank AI Fluency track's
PF-05 assignment — a minimal, permanent front door that will later carry a
`yourname.flyrank.ai` subdomain once my capstone is approved.

## Files

```
index.html    The entire page — one file, no routing, no build step
style.css     Styling (fonts, colors, layout)
```

No JavaScript, no framework, no dependencies beyond two Google Fonts loaded
via `style.css`'s `@import`. Everything on the page is either static text or
a plain `<a href>` link out to an external service (GitHub, LinkedIn, Google
Drive, Calendly).

## Run locally

Open `index.html` directly in a browser, or serve the folder:

```bash
python -m http.server 8000
```

## Deploy

Hosted on Netlify's free tier, deployed by dragging this folder into
Netlify's dashboard (or connecting the GitHub repo for auto-deploy on push).
Site renamed from the random default (`something-1234.netlify.app`) to a
clean, CV-ready name under Site configuration → Change site name.
