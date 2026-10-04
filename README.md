# آلور

آلور بازار نیازمندی است. به‌جای شروع از گشتن بین آگهی‌ها، درخواست خرید ثبت می‌شود. فروشنده‌ها پیشنهاد قیمت می‌فرستند و پیشنهادها کنار هم دیده می‌شوند. آگهی فروش و جستجوی بازار هم روی همان سایت است.

سایت: [alwer.ir](https://alwer.ir)

## رفتار عمومی

صفحهٔ اصلی [alwer.ir](https://alwer.ir) مسیر خریدار را این‌طور می‌نویسد: ثبت درخواست خرید، نوشتن نیاز (و در صورت استفاده، دستیار برای تبدیل متن آزاد به فیلد ساخت‌یافته)، دقیق کردن شهر و بودجه و جزئیات، سپس مقایسهٔ پیشنهادها. مسیر فروشنده: پیشنهاد قیمت روی درخواست خرید مرتبط، ثبت آگهی فروش تا در جستجوی بازار دیده شود، و ساختن هشدار برای دسته و شهر. همان صفحه می‌گوید ده ثبت اول هر ماه رایگان است و بعد از آن هر ثبت از کیف پول کم می‌شود و روی خود معامله کارمزد گرفته نمی‌شود؛ مبلغ بعد از سهمیهٔ رایگان در همان صفحه ۱۰٬۰۰۰ تومان برای هر ثبت آمده است. صفحه به ثبت درخواست، جستجو، مرکز راهنما، دریافت برنامه، وبلاگ، درباره، و مجوزها پیوند می‌دهد.

## English

Alwer is a classifieds marketplace. A buyer posts a request. Sellers send price offers, and those offers are compared side by side. A seller can also post a sale listing. Search of the market is on the same site.

Site: [alwer.ir](https://alwer.ir)

The public homepage describes the buyer path as posting a request, optionally using the on-site assistant to turn free text into structured fields, setting city, budget, and details, then comparing offers. The seller path is a price offer on a related buy request, a sale listing that appears in market search, and an alert for a category and a city. The same page states that the first ten posts of each month are free, that a later post is charged from the wallet, and that the deal itself has no commission. The amount shown there after the free quota is 10,000 tomans per post. The page links new-request, search, help, download, blog, about, and licenses.

## Parts

The web client is `alwer-client`, built with Vite and React. Routing uses React Router. The API is `alwer-server`, Express on Node.js (engines field `>=20`). The mobile shell is `alwer_app`, Flutter.

`alwer-server/src/server.js` checks PostgreSQL, connects Redis, attaches Socket.IO to the HTTP server, starts Bull workers, and listens. PostgreSQL is a `pg.Pool` in `alwer-server/src/infrastructure/database/postgresql.js` (pool max 20). Redis is `ioredis` in `alwer-server/src/infrastructure/database/redis.js` (`maxRetriesPerRequest: null`, `lazyConnect: true`).

`alwer-server/src/app.js` mounts Helmet, CORS, a JSON body limit of 1mb, and these routers:

- `/api/auth`
- `/api/requests`
- `/api/search` and `/api/search/external`, both behind the search rate limit
- `/api/alerts`
- `/api/notifications`
- `/api/chat`
- `/api/wallet`
- `/api/affiliate`
- `/api/identity`
- `/api/pricing`
- `/api/reports`
- `/api/ratings`
- `/api/blog`
- `/api/pages`
- `/api/seo`
- `/api/meta`
- `/api/vehicles`
- `/api/electronics`
- `/api/uploads`
- `/api/users`
- `/api/ai`
- `/api/map`
- `/api/bookmarks`
- `/api/ads`
- `/api/support`
- `/api/content-review`
- quote routes mounted at `/api` (paths are under `/quotes` and `/requests/:id/quotes`)

Admin routes are mounted separately. An unknown path falls through to the error handler at the bottom of `app.js`.

## Buy request and price offer

Creating a request is `POST /api/requests` with `authMiddleware` and `checkRequestQuota` (`alwer-server/src/modules/request/request.routes.js`). Listing is `GET /api/requests`. The owner list is `GET /api/requests/mine`. One request is `GET /api/requests/:id` with optional auth. Update is `PUT /api/requests/:id`. Delete is `DELETE /api/requests/:id`. Related rows are `GET /api/requests/:id/related` and `GET /api/requests/:id/matches`. A market estimate is `GET /api/requests/:id/market-estimate`. Contact is `POST /api/requests/:id/contact`. Renew is `POST /api/requests/:id/renew`.

Quotes live in `alwer-server/src/modules/quote/quote.routes.js`, all behind `authMiddleware`:

- `POST /api/requests/:id/quotes` also runs `checkQuoteQuota`
- `GET /api/requests/:id/quotes`
- `GET /api/quotes/mine`
- `GET /api/quotes/seller-dashboard`
- `PATCH /api/quotes/:id`
- `POST /api/quotes/:id/accept`
- `POST /api/quotes/:id/withdraw`

Market search is `GET /api/search` (`search.routes.js` delegates to `search.controller.list`), behind `searchRateLimit`.

## Auth, chat, upload

`/api/auth` (`auth.routes.js`) exposes `POST /register`, `POST /verify`, `POST /totp/verify`, `POST /totp/recover`, `POST /logout`, and `GET /me`. Register, verify, and TOTP routes use `authRateLimit`. Logout and `me` use `authSessionRateLimit` and `authMiddleware`. TOTP verify and recover also pass `totpPendingMiddleware`. Tokens are checked in `authMiddleware` inside `alwer-server/src/infrastructure/websocket/socket.js` from the `Authorization` header. Libraries on that path, from `alwer-server/package.json`, are `jsonwebtoken`, `bcryptjs`, and `otplib`.

Chat HTTP routes in `chat.routes.js` all require auth: list conversations, start a conversation on a request, list and send messages, edit and delete a message, hide and unhide a conversation. Socket.IO on the same server file listens for `connection`, `chat:join`, `chat:leave`, `chat:typing`, `chat:typing_stop`, and `disconnect`, and emits `chat_typing` and `chat_typing_stop`. The handshake reads a token from `socket.handshake.auth.token` or the `Authorization` header. The web client depends on `socket.io-client`.

Image upload is `POST /api/uploads` with `authMiddleware` and `upload.single('image')` (`upload.routes.js`). `multer` accepts the file and `sharp` is the image library declared on the server. A second route, `POST /api/uploads/review`, uses the AI rate limit.

## Jobs and matching

`server.js` always starts three Bull workers: match alerts, match opposite, and pilot liquidity. When `aiConfig.queueEnabled` is set, it starts the AI extraction worker with `aiConfig.queueConcurrency`. When `aiConfig.embedPersistEnabled` is set, it starts the request-embedding worker with concurrency 2. Bull and Redis are the queue pair (`bull` and `ioredis` in the server package).

`alwer-server/database/migrations/020_pgvector_request_embeddings.sql` creates the `vector` extension, adds `requests.embedding` as `vector(768)`, plus `embedding_model` and `embedding_updated_at`, and adds an HNSW index on `vector_cosine_ops` limited to rows that have an embedding, `category` in `vehicles` or `real-estate`, and `status` `active`. `alwer-server/src/modules/matching/matchEmbedding.js` reranks candidates with cosine similarity when match embeddings are enabled, and returns the original candidates if the runtime is unavailable.

Map data is served under `/api/map`. The web client depends on Leaflet. The Flutter app ships Leaflet assets (`assets/map/leaflet.css` and `assets/map/leaflet.js` in `alwer_app/pubspec.yaml`).

Web push is the `web-push` dependency. The Flutter app does not enable Firebase Cloud Messaging in `pubspec.yaml`; those lines are comments.

## Web and mobile surface

The client tree includes pages for home, a new request, request detail, search hubs, market map, chat, quotes, alerts, bookmarks, wallet, affiliate, notifications, blog, help, download, about, privacy, terms, licenses, profile, and verification. Voice input on the web is under `alwer-client/src/hooks/useVoiceInput.js` and `alwer-client/src/intelligence/speech/`. The Flutter shell (`alwer_app`, Dart SDK `>=3.5.0 <4.0.0`) uses `webview_flutter` for the fallback web view and `speech_to_text` in `alwer_app/lib/core/native_stt/device_speech.dart`. Category and city data are packaged as assets, including `assets/alwer-categories.json` and `assets/geo/iran-cities.json`.

End-to-end tests are declared as devDependency `@playwright/test` on the server package.

## Libraries

Web client, `alwer-client/package.json`:

- react ^18.3.1, react-dom ^18.3.1
- react-router-dom ^7.1.1
- vite ^6.0.7 and @vitejs/plugin-react ^4.3.4
- tailwindcss ^3.4.17, postcss ^8.4.49, autoprefixer ^10.4.20
- leaflet ^1.9.4
- socket.io-client ^4.8.1
- lucide-react ^1.21.0
- @fontsource/vazirmatn ^5.2.6
- @uiw/react-md-editor ^4.1.1
- dev: sharp ^0.35.3, @resvg/resvg-js ^2.6.2

API, `alwer-server/package.json` (Node.js >= 20):

- express ^4.21.2
- pg ^8.13.1
- ioredis ^5.4.2
- bull ^4.16.5
- socket.io ^4.8.1
- jsonwebtoken ^9.0.2
- bcryptjs ^2.4.3
- otplib ^13.4.1
- sharp ^0.34.5
- multer ^1.4.5-lts.1
- web-push ^3.6.7
- helmet ^8.0.0
- express-rate-limit ^8.5.2
- rate-limit-redis ^5.0.0
- cors ^2.8.5
- axios ^1.18.1
- cheerio ^1.2.0
- gray-matter ^4.0.3
- marked ^18.0.5
- uuid ^11.0.5
- firebase-admin ^13.10.0
- dotenv ^16.4.7
- dev: @playwright/test ^1.61.1, simple-icons ^16.27.0

`firebase-admin` is declared on the server package. The Flutter pubspec does not depend on Firebase.

Mobile, `alwer_app/pubspec.yaml` version 1.0.13+79:

- Flutter and flutter_localizations
- Dart SDK >=3.5.0 <4.0.0
- webview_flutter ^4.10.0, plus the Android, web, and platform-interface packages pinned there
- speech_to_text ^7.0.0
- url_launcher ^6.3.2
- flutter_screenutil ^5.9.3
- web ^1.1.1
- flutter_lints ^5.0.0 in dev_dependencies

## Boundaries

Hosting and credentials are omitted. Payment gateway product names are not declared in the server `package.json` dependencies listed above.

## پروژه‌های مرتبط

- [hamejoo-intro](https://github.com/mnhashemabadi/hamejoo-intro): همه‌جو نتیجهٔ جستجوی آگهی را یک‌جا نشان می‌دهد و جزئیات آگهی روی منبع اصلی می‌ماند.
- [kasbafzar-intro](https://github.com/mnhashemabadi/kasbafzar-intro): کسب‌افزار پیشخوان فروش و گزارش مالی است؛ فروش، مشتری و هزینه ثبت می‌شود و فاکتور می‌تواند لینک پرداخت داشته باشد.
- [azadchi-intro](https://github.com/mnhashemabadi/azadchi-intro): آزادچی بازار نیازمندی مناطق آزاد و ویژهٔ اقتصادی است؛ آگهی در همان محدوده جستجو می‌شود و گفتگو داخل همان محصول است.
- [afzi-intro](https://github.com/mnhashemabadi/afzi-intro): افزی یک نشانی http یا https را به لینک کوتاه تبدیل می‌کند و باز کردن آن لینک به همان صفحه می‌رود.
- [alweryar-intro](https://github.com/mnhashemabadi/alweryar-intro): آلوریار شبکهٔ همکاران آلور برای بررسی آگهی و همکاری در فروش است.
- [alwerchi-intro](https://github.com/mnhashemabadi/alwerchi-intro): آلورچی خانهٔ فروشگاه‌هایی است که کالا و موجودی‌شان در بازار آلور دیده می‌شود و خریدار در آلور می‌ماند.

## Related

- [hamejoo-intro](https://github.com/mnhashemabadi/hamejoo-intro): Hamejoo shows listing search results in one place, and the listing detail stays on the original source.
- [kasbafzar-intro](https://github.com/mnhashemabadi/kasbafzar-intro): Kasbafzar is a sales desk and a financial report: sales, customers, and expenses are recorded, and an invoice can carry a payment link.
- [azadchi-intro](https://github.com/mnhashemabadi/azadchi-intro): Azadchi is a classifieds market for free zones and special economic zones: listings are searched in that area, and the conversation stays in the product.
- [afzi-intro](https://github.com/mnhashemabadi/afzi-intro): Afzi turns an http or https address into a short link, and opening that link goes to the same page.
- [alweryar-intro](https://github.com/mnhashemabadi/alweryar-intro): Alweryar is Alwer's collaborator network for listing review and for sales collaboration.
- [alwerchi-intro](https://github.com/mnhashemabadi/alwerchi-intro): Alwerchi is the home of shops whose goods and stock appear on the Alwer marketplace while the buyer stays on Alwer.

یادداشت مهندسی کوتاه‌تر: [docs/engineering.md](docs/engineering.md)

Shorter engineering note: [docs/engineering.md](docs/engineering.md)

