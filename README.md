# demo-accounting

Interactive demo of **لاجورد (Lajvard) v2**: issuing and sending electronic invoices to the Iranian tax authority's Moudian system (دموی تعاملی لاجورد، صدور و ارسال صورتحساب الکترونیکی به سامانه مودیان).

Live: https://demo.mouneskhah.com (noindex)

## What is here

| File | What it is |
| --- | --- |
| `index.html` | The whole demo: one self-contained page (HTML, CSS, JavaScript, fonts and images embedded; the only external request is the SheetJS library from cdnjs, loaded when a real Excel file is imported). Persian, RTL. This is the version to serve in production (SHA-256 `a46bde2454deece2e79138167284e5667d2bc493bb3afe1999edb60b23b05f01`, updated 2026-11-15; the previous version was `ba74474af2167279022fe47ff4fcb011ea283949fd2e930beb3210c24a3b8e5d`). |
| `deploy/nginx-demo.mouneskhah.com.conf` | The nginx site that serves it: static files from `/var/www/demo`, Let's Encrypt certificate, `X-Robots-Tag: noindex, nofollow`, `Cache-Control: no-cache`. |

## Run locally

Open `index.html` in a browser, or serve the folder: `python -m http.server 8080`.

## Deploy

Copy `index.html` to `/var/www/demo/index.html` on the server. No build step, nothing to restart. Pushing to this repository does not update the live site by itself. After copying, `sha256sum /var/www/demo/index.html` on the server should print the checksum above.
