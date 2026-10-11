# CivilService Zone — deploy package

This project keeps the supplied `index.html` and visual theme, and adds a Node/Express API for accounts, marketplace products, orders, provider listings, bookings, owner dashboard and Gemini AI help.

## GitHub + Render setup
1. Upload the contents of this ZIP to a GitHub repository (the `package.json` and `server.js` must be in the repository root).
2. In Render create **New Web Service** and connect the repository.
3. **Root Directory:** leave blank if files are in repository root. If you upload this folder inside a parent repository, set Root Directory to `csz_project`.
4. **Build Command:** `npm install`
5. **Start Command:** `npm start`
6. Add Render Environment variables:
   - `GEMINI_API_KEY` = your key from Google AI Studio
   - `OWNER_KEY` = `My life maa` (you can choose a different private key)
   - `JWT_SECRET` = a long random secret (Render can generate one)
   - `GEMINI_MODEL` = `gemini-2.5-flash`
7. For persistent customer data, attach a Render disk and set `DATA_DIR=/var/data`. Without a persistent disk, JSON data can reset on some redeploys/instances.

Never place the real Gemini key in `index.html`, GitHub, or a public screenshot. The AI request is sent through `/api/ai/ask` on the server.

## Current scope / important notes
- The supplied dashboard HTML is retained and Gemini error instructions are updated.
- The API enforces one role per mobile number and separates Customer, Provider and Seller login roles.
- Product photo data is stored with JSON records; keep images small. For production-scale use, move photos to object storage and use a managed database.
- COD orders and booking/status changes are supported. Online payments, SMS OTP, identity verification, and production-grade referral coupon accounting are not configured.
- The Owner key is an access gate, not a substitute for changing `JWT_SECRET`, rate limiting, backups and a database before public commercial launch.
- A conceptual AI house-plan brief is not an approved construction drawing. Get local code and structural design checked by a qualified engineer/architect.

## Android app
`android-wrapper/` contains a minimal Android Studio WebView project source pointing at the deployed website. Set `WEBSITE_URL` in `MainActivity.java` to your Render URL, then build an APK from Android Studio. An APK is not precompiled here because this environment does not have the Android SDK/Gradle build toolchain installed.


## Targeted fixes in this ZIP (2026-10-11)
- Fixed the global image-to-data helper so legacy `imgToData` calls and Seller/Provider photo uploads work (JPG/PNG/WEBP, max 4 MB).
- Routed Room Search, Transportation, Provider and My Bookings pages to the server-backed functions; booking records now show for the customer and provider.
- Updated marketplace login to share the server auth token with the Seller/Provider/Customer flows, so listing, booking and material endpoints receive the correct token.
- Replaced OpenAI API calls with the Gemini `generateContent` API using `GEMINI_API_KEY` and `GEMINI_MODEL`. `/api/health` reports whether the key variable is present (not whether the key is valid).
- Ran server and embedded-script syntax checks, plus a local API flow test for seller product upload/visibility and provider/customer bookings.

### Render AI configuration
The backend reads `GEMINI_API_KEY` from Render Environment at runtime. Add that exact key name with your own Gemini API key in the Render service Environment settings, save/deploy, then check `/api/health` for `aiConfigured: true`. Never put the secret in `server.js`, `index.html`, or GitHub.
