# AeroDocs v1.008 — Pilot & Family Expiry Tracker (PWA)

## Files
| File | Purpose |
|---|---|
| index.html | App (vanilla JS, single file, localStorage) |
| manifest.webmanifest | PWA manifest (name, icons, standalone) |
| sw.js | Service worker, offline cache `aerodocs-v1.001` |
| icon-180/192/512(-maskable).png | Home Screen / PWA icons |
| resend-relay.php | Optional server-side relay for Resend email |
| tesseract*.js, worker.min.js | Tesseract.js 5.1.1 OCR engine (Apache-2.0), self-hosted |
| *.traineddata.gz | English + Arabic language data (best_int) |
| pdf*.min.js | pdf.js 3.11.174 (Apache-2.0) for PDF preview and text |
| .htaccess | MIME types, no-cache for sw.js/index, no gzip re-encoding of .gz |

## Host (required for PWA install + offline)
1. cPanel → File Manager → `public_html/aerodocs/` → upload the zip → Extract. Keep the `lib/` folder and the hidden `.htaccess` file.
2. Open `https://<your-domain>/aerodocs/` in Safari. HTTPS is mandatory for the service worker.

## Install on iPhone
1. Safari → open the URL above.
2. Share → Add to Home Screen → Add.
3. Launch from the icon. Data lives in the Home Screen app's own storage (separate from Safari). Load or import data there.

## Scan & files (v1.008)
- Scan → Take photo, or choose a photo / PDF. OCR runs on the phone; nothing is uploaded.
- Passports, visas, ID/resident cards: expiry, number and issuing state read from the MRZ with ICAO 9303 check digits.
- Other documents: type from keywords (English/Arabic), expiry from the date nearest an expiry label. Every date found is offered as a tap-to-select chip.
- PDFs with a text layer are read directly (no OCR).
- Scanning a document type already tracked offers "Update" (renewal; old expiry kept in history) or "Add as new".
- Files live in IndexedDB on the device. Backup export can include them.
- Settings → Prepare offline scanning caches the engine (~7 MB English, +1.7 MB Arabic).

## Calendar behaviour
- Apple (.ics): all-day event on expiry date, alarms at threshold + selected leads (default 30/14/7 d) + expiry day, at the set alarm time (09:00). Optional RENEW event on threshold date and WINDOW OPENS event on revalidation-window date.
  - Safari: tap → download → tap the download → Add All.
  - Home Screen app: tap → share sheet → Save to Files (or Mail to self) → open → Add to Calendar.
- Google: link opens a pre-filled all-day RENEW event (threshold date, or today if already inside the window). Google URLs cannot carry reminders; the event uses your calendar's default all-day notifications. Set them in Google Calendar → Settings → your calendar → All-day event notifications (e.g. 1 day before, 09:00).
- Bulk: master .ics with every document. Google import: calendar.google.com → Settings → Import & export → Import. Google may drop VALARMs on import; rely on default notifications.

## Email alerts
- EmailJS: browser-safe public key. Template: To = `{{to_email}}`, Subject = `{{subject}}`, Body = `{{message}}`.
- Resend: edit and upload `resend-relay.php`; enter its URL + token in the app. Relay only sends to `$ALLOWED_TO`.
- Checks run when the app opens (max every 6 h) or on "Run check now". iOS does not run web apps in the background; calendar alarms are the primary alert path.

## Updating
Bump `VER` in index.html and `CACHE` in sw.js together (lib cache is versioned separately), re-upload, reopen the app twice.
