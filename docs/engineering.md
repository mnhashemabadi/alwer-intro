# Engineering note

## Overview

Alwer is a classifieds marketplace. A buyer posts a request and compares price offers. A seller posts a listing. The same site searches the market.

## Stack

Web client, declared in `alwer-client/package.json`:

- React ^18.3.1
- Vite ^6.0.7
- Tailwind CSS ^3.4.17
- React Router ^7.1.1
- Leaflet ^1.9.4
- Socket.IO client ^4.8.1

API, declared in `alwer-server/package.json`. Engines field: Node.js >= 20.

- Express ^4.21.2
- PostgreSQL client `pg` ^8.13.1
- Redis client `ioredis` ^5.4.2
- Bull ^4.16.5
- Socket.IO ^4.8.1
- jsonwebtoken ^9.0.2
- bcryptjs ^2.4.3
- otplib ^13.4.1
- sharp ^0.34.5
- multer ^1.4.5-lts.1
- web-push ^3.6.7
- Helmet ^8.0.0
- express-rate-limit ^8.5.2

PostgreSQL is opened with `pg.Pool` in `alwer-server/src/infrastructure/database/postgresql.js`. Redis is opened with `ioredis` in `alwer-server/src/infrastructure/database/redis.js`.

Mobile app, declared in `alwer_app/pubspec.yaml`:

- Flutter
- Dart SDK >=3.5.0 <4.0.0
- webview_flutter ^4.10.0
- speech_to_text ^7.0.0

End-to-end tests: Playwright, devDependency `@playwright/test` ^1.61.1 in `alwer-server/package.json`.

## Specialties

- Offer and listing marketplace, served by the Express API and the React client above
- Realtime updates: Socket.IO (`socket.io` on the server, `socket.io-client` on the web client)
- Maps: Leaflet on the web client, and Leaflet assets in the Flutter app
- Authentication: jsonwebtoken, bcryptjs, and otplib
- Background jobs: Bull on Redis
- Image upload: multer and sharp
- Web push: web-push
- On-device speech input: `speech_to_text` in `alwer_app/lib/core/native_stt/device_speech.dart`
- Request matching: PostgreSQL pgvector embeddings. Extension and column are created in `alwer-server/database/migrations/020_pgvector_request_embeddings.sql`. Matching code is in `alwer-server/src/modules/matching/matchEmbedding.js`.

## Boundaries

This note covers the Alwer marketplace applications named above. It does not describe hosting or credentials.
