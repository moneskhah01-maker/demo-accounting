# demo-accounting

Interactive demo of **لاجورد (Lajvard) v2**: issuing and sending electronic invoices to the Iranian tax authority's Moudian system (دموی تعاملی لاجورد، صدور و ارسال صورتحساب الکترونیکی به سامانه مودیان).

Live: https://demo.mouneskhah.com (noindex)

## What is here

| File | What it is |
| --- | --- |
| `index.html` | The whole demo: one self-contained page (HTML, CSS, JavaScript, fonts and images embedded; the only external request is the SheetJS library from cdnjs, loaded when a real Excel file is imported). Persian, RTL. This is the version to serve in production: v10, SHA-256 `b4336ed8d17f7acf842a76af74968b62f9a9f86cb6aead413f8d2899bb3fe0e7`, updated 2026-11-18. The previous version, v9, was `a46bde2454deece2e79138167284e5667d2bc493bb3afe1999edb60b23b05f01`. |
| `deploy/nginx-demo.mouneskhah.com.conf` | The nginx site that serves it: static files from `/var/www/demo`, Let's Encrypt certificate, `X-Robots-Tag: noindex, nofollow`, `Cache-Control: no-cache`. It includes `snippets/lajvard-api.conf`. |
| `deploy/nginx-lajvard-api.snippet.conf` | `/api/` on the same origin, proxied to the Lajvard backend at `127.0.0.1:4317`, with rate limits. The zones `lajvard_api` and `lajvard_login` are in `/etc/nginx/conf.d/lajvard.conf`. Only `/api/` is exposed, so the sandbox simulator is not. |

## The Lajvard server (v10)

The top-bar badge «ورود به سرور لاجورد» (also in the settings menu, for phones) signs in to the Lajvard backend on the same origin.

Signed in, finalizing an invoice sends the page's own invoice JSON to the server. The server does the following:

- checks it against the e-invoice instruction V7.9 with the full goods/service ID bank;
- gives the serial and the 22-character tax ID;
- signs and encrypts it;
- sends it to the Moadian system. The live demo server runs in sandbox mode, against an independent simulator of the system.

The server's errors appear with the instruction's table and row. Send status and the buyer's reaction come from the server, and the sync button syncs the server. Invoices issued this way are kept in the browser, so they survive a reload.

Signed out, or with `index.html` opened on its own, the demo runs entirely in the browser as before.

Users are created only by the operator on the server, with `bash /root/lajvard-deploy/install-vps.sh user <name>`. The backend lives at `E:\VeeJay\Lajvard-backend`; see its README.

## Run locally

Open `index.html` in a browser, or serve the folder: `python -m http.server 8080`.

To try it with the backend, run the backend with `STATIC_DIR` pointing at this folder:

```
MODE=sim LAJVARD_API_TOKEN=… STATIC_DIR=… npm start
```

Then open http://127.0.0.1:4317.

## Deploy

Copy `index.html` to `/var/www/demo/index.html` on the server, or run `bash install-vps.sh demo <file>`, which keeps the previous page in `/var/www/demo-backups`. There is no build step. Pushing to this repository does not update the live site by itself. After copying, `sha256sum /var/www/demo/index.html` on the server should print the checksum above.
