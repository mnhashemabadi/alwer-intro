# Engineering note

## Overview

Alwer is a classifieds marketplace. A buyer posts a request and compares price offers. A seller posts a listing. The same site searches the market.

## Stack

Web client:

- React ^18.3.1
- Vite ^6.0.7
- Tailwind CSS ^3.4.17
- React Router ^7.1.1
- Leaflet ^1.9.4
- Socket.IO client ^4.8.1

API, Node.js 20 or later:

- Express ^4.21.2
- PostgreSQL client `pg` ^8.13.1
- Redis client `ioredis` ^5.4.2
- Bull ^4.16.5
- Socket.IO ^4.8.1
- sharp ^0.34.5
- multer ^1.4.5-lts.1
- web-push ^3.6.7
- Helmet ^8.0.0
- express-rate-limit ^8.5.2

Mobile app:

- Flutter
- Dart SDK >=3.5.0 <4.0.0
- webview_flutter ^4.10.0
- speech_to_text ^7.0.0

End-to-end tests: Playwright (`@playwright/test` ^1.61.1).

## Specialties

- Request-and-offer marketplace, served by the Express API and the React client
- Realtime chat: Socket.IO on the server and the web client
- Maps: Leaflet on the web client and inside the Flutter app
- Background jobs: Bull on Redis
- Image upload and processing: multer and sharp
- Web push: web-push
- On-device speech input: `speech_to_text`
