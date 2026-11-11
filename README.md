# demo-accounting

Interactive demo of **لاجورد (Lajvard) v2**: issuing and sending electronic invoices to the Iranian tax authority's Moudian system (دموی تعاملی لاجورد، صدور و ارسال صورتحساب الکترونیکی به سامانه مودیان).

Live: https://demo.mouneskhah.com (noindex)

## What is here

| File | What it is |
| --- | --- |
| `index.html` | The whole demo: one self-contained page (HTML, CSS, JavaScript, fonts and images embedded, no external requests). Persian, RTL. This is a byte-for-byte copy of the file served in production (SHA-256 `4935be71ec68e585cbbb16dd5626abc01bd4ee98810aae86b7d879be8d3b4a64`, last modified on the server 2026-11-10). |
| `deploy/nginx-demo.mouneskhah.com.conf` | The nginx site that serves it: static files from `/var/www/demo`, Let's Encrypt certificate, `X-Robots-Tag: noindex, nofollow`, `Cache-Control: no-cache`. |

## Run locally

Open `index.html` in a browser, or serve the folder: `python -m http.server 8080`.

## Deploy

Copy `index.html` to `/var/www/demo/index.html` on the server. No build step, nothing to restart.
