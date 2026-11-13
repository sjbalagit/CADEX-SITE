# Cadex website

The Cadex product site at https://cadexai.ca, published with GitHub Pages from the `main` branch. `CNAME` holds the custom domain.

- `index.html` — the page, with its styles and scripts inside (scroll scenes, header, early-access form)
- `privacy.html` — the privacy policy
- `favicon.svg`, `favicon.png` — tab and home-screen icons
- `share-wide.png` — image in shared link previews (`share.png` is the older square version)
- `shots/` — product screenshots (`hero-chat.png` is a 2x screenshot so it stays sharp on retina screens)
- `apps-script/early-access.gs` — the Google Apps Script that saves early-access sign-ups

Instrument Sans loads from Google Fonts. The company site, VIBIN AI LABS, lives at https://vibinlabs.si in its own repo.

## Run the site locally

The site is plain static files. From the repo root run `python3 -m http.server 8765` and open http://localhost:8765. Press `Ctrl+C` to stop.

## Early access (live)
The form runs on its own: email → "You're on the list" details → "Thanks!".
Sign-ups go to a Google Sheet through a small Apps Script, `apps-script/early-access.gs`. Each email gets one row: Signed up, Email, What does your work involve?, Province.

To connect it:
1. Open the sheet, then Extensions > Apps Script.
2. Replace the editor contents with `apps-script/early-access.gs` and save.
3. Choose `setup` in the function menu and click Run. Approve the permissions when asked. This also formats the sheet.
4. Deploy > New deployment > Web app. Set Execute as: Me, and Who has access: Anyone. Copy the Web app URL, which ends in /exec.
5. Set that URL in index.html:

       <div class="early" id="early" data-endpoint="https://script.google.com/macros/s/XXXX/exec">

After changing the script later, publish it with Deploy > Manage deployments > Edit > New version. This keeps the same URL.

Each step POSTs JSON as text/plain, because Apps Script can't answer the CORS preflight that application/json triggers:
- step 1: { step: "email", email }
- step 2 (Send): { step: "details", email, work: [...], province: [...], workOther?, provinceOther? }
  Skip sends nothing more (the email is already in).
The endpoint must answer with JSON containing `"ok": true`. The next screen shows immediately and saving happens in the background, with one automatic retry. If step 1 still fails, the visitor returns to the first screen with an error and their email filled in. If step 2 fails, the visitor still sees "Thanks!" and the email stays recorded, without their answers. Choosing Other requires typing a description before Send; Skip is always available. With no endpoint set, nothing is sent (a warning shows in the console).

## Before launch
- Add a Terms page and footer link before the first customer signs up.

## Linking to the form
To jump straight to the form, link to https://cadexai.ca/#early.
