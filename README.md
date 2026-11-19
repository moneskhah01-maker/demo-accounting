# demo-accounting

Interactive demo of **لاجورد (Lajvard) v2**: issuing and sending electronic invoices to the Iranian tax authority's Moudian system (دموی تعاملی لاجورد، صدور و ارسال صورتحساب الکترونیکی به سامانه مودیان).

Live: https://demo.mouneskhah.com (noindex)

## What is here

| File | What it is |
| --- | --- |
| `index.html` | The whole demo: one self-contained page (HTML, CSS, JavaScript, fonts and images embedded; the only external request is the SheetJS library from cdnjs, loaded when a real Excel file is imported). Persian, RTL. This is the version to serve in production: v12, SHA-256 `109db0b079c0f0bda131cef2df109eb6e22ff1a41632facd32ed57e023ae2d4a`, updated 2026-11-19. The previous versions were v11 `ee57372adc09ceec5dadeb08149a96691d47b66fd42d3e1e31ef2706f1333851` and v10 `b4336ed8d17f7acf842a76af74968b62f9a9f86cb6aead413f8d2899bb3fe0e7`. |
| `deploy/nginx-demo.mouneskhah.com.conf` | The nginx site that serves it: static files from `/var/www/demo`, Let's Encrypt certificate, `X-Robots-Tag: noindex, nofollow`, `Cache-Control: no-cache`. It includes `snippets/lajvard-api.conf`. |
| `deploy/nginx-lajvard-api.snippet.conf` | `/api/` on the same origin, proxied to the Lajvard backend at `127.0.0.1:4317`, with rate limits. The zones `lajvard_api` and `lajvard_login` are in `/etc/nginx/conf.d/lajvard.conf`. Only `/api/` is exposed, so the sandbox simulator is not. |

## v12 (2026-11-19)

This version makes the changes the client asked for on 28 Aban 1405.

- **Moadian settings without a password or a certificate.**
  - The connection needs only two things: the memory ID and the private key file, whose public key is registered for that memory ID in the taxpayer portal.
  - This is the official no-certificate web service (`/req/api/self-tsp`), the method of the official «بدون گواهی امضا» SDK.
  - A password field appears only if the chosen key file is itself encrypted.
  - **Settings** and the profile card «تنظیمات سامانه مودیان» open the same editor.
  - Two options are folded away in the editor: making a new key on the server (its public key is shown for the portal), and a signing certificate (key and certificate, or PFX) for anyone who has one.
  - The connection test shows its three steps: the system's key and clock, authentication with the key, and the memory ID inquiry.
- **«سامانه مودیان» is the send queue.**
  - Issued invoices wait here as «آماده ارسال», with their Article 9 deadline.
  - They are sent one at a time, as a selection, or all together. Results appear under «در حال ارسال», «ثبت‌شده» (with the tax ID and the system's tracking number) and «رد یا متوقف».
  - Invoices another program already sent are imported here from the portal's Excel export.
  - «همه صورتحساب‌ها» stays the full list. In Settings, «ارسال پس از صدور» chooses between collecting here and sending at once.
- **«دریافت‌ها»** records receipts of credit and cash/credit invoices after they are issued: amount, date, method and reference. The balance due updates at once, and no receipt may exceed it.
- **Goods** defined on the page are saved on the server too. When signed in, an ID is checked against the server's full goods bank of 966,886 IDs.
- **«همه صورتحساب‌ها» columns line up again.** Each row's entry-animation class `in` was also the global input-field class (a flex box 40 px tall, with a border). The rows stopped being grids, so their cells no longer sat under the header. The animation class is now `rin`; the rows of a new invoice had the same fault.
- Texts that spoke of a signing certificate (reminders, signing steps) now speak of the private key.

## Settings (v11)

«تنظیمات» is a page of its own. It is about the system: the theme is only one card of it.

- **اتصال به سامانه مودیان:** the one place where the keys are entered. These are the memory ID from the taxpayer portal, the private key and the signing certificate of a licensed intermediate CA. The technical instruction of Ordibehesht 1405 puts that certificate in x5c on every token and invoice. The keys can arrive three ways:
  1. key and certificate files;
  2. one PFX/P12 file with its password;
  3. a key made on the server with a certificate request (CSR) for the CA, after which the issued certificate is uploaded.

  The card shows the certificate details (national ID, validity, issuer), the public key for the portal and the last connection test. In the sandbox there is also «ثبت در کارپوشه آزمایشی».
- **سرور لاجورد:** the sign-in state.
- **ارسال و همگام‌سازی:** the server's own sync interval, Article 9 deadline, retries and reference windows.
- **مودی و مجوز:** the locked identity, VAT liability (saved on the server) and the licensed patterns.
- **بانک شناسه کالا و واحدها**
- **ظاهر**

The profile page shows the keys as a summary; its button opens this page.

## The Lajvard server (v10)

The top-bar badge «ورود به سرور لاجورد» signs in to the Lajvard backend on the same origin. On phones, where the badge is hidden, sign in from the Settings page.

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
