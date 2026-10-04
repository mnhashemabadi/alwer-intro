# آلور

من آلور را برعکس بازار آگهی معمولی چیدم. خریدار اول نیاز را می‌نویسد. فروشنده روی همان درخواست قیمت می‌دهد. پیشنهادها کنار هم‌اند تا مقایسه شوند. آگهی فروش را حذف نکردم؛ موازی است و در جستجوی بازار دیده می‌شود. اگر فقط آگهی می‌ماند، آلور یک تابلوی دیگر می‌شد.

سایت: [alwer.ir](https://alwer.ir)

## آنچه روی صفحهٔ اصلی گفتم

مسیر خریدار روی [alwer.ir](https://alwer.ir): ثبت درخواست، نوشتن نیاز، و اگر بخواهد دستیار همان سایت که متن آزاد را به فیلد ساخت‌یافته تبدیل می‌کند. بعد شهر و بودجه و جزئیات، بعد مقایسهٔ پیشنهادها. مسیر فروشنده: پیشنهاد قیمت روی درخواست مرتبط، آگهی فروش برای جستجوی بازار، و هشدار برای یک دسته و یک شهر. فیلد ساخت‌یافته را برای همین گذاشتم؛ پیشنهاد قیمت بدون فیلد مشترک قابل مقایسه نیست. همان صفحه دستیار را Alwer Intelligence می‌نامد و می‌گوید پردازش روی زیرساخت آلور است. نام مدل را اینجا نمی‌آورم چون صفحهٔ عمومی هم نام مدل ننوشته است.

ده ثبت اول هر ماه، درخواست خرید یا آگهی فروش، رایگان است و سهمیه اول ماه از نو پر می‌شود. بعد از آن هر ثبت ۱۰٬۰۰۰ تومان از کیف پول کم می‌شود. روی خود معامله کارمزد نیست. این جمله روی همان صفحه است. در کد، `FREE_REQUEST_LIMIT` برابر ۱۰ و `REQUEST_FEE_AMOUNT` برابر ۱۰۰۰۰ است. `FREE_QUOTE_LIMIT` هم ۱۰ است و `QUOTE_FEE_AMOUNT` برابر ۵۰۰۰. پنجرهٔ سهمیه را با `jalaliPeriod.js` روی ماه جلالی بستم (`periodType` پیش‌فرض `monthly` در کاتالوگ سهمیه)، چون «اول ماه» برای کاربر ایرانی اول ماه میلادی نیست.

## سه پوسته، یک API

کلاینت وب `alwer-client` است: Vite، React ^18.3.1، React Router ^7.1.1، Tailwind ^3.4.17، Leaflet ^1.9.4، و `socket.io-client` ^4.8.1. نقشه را داخل بازار خواستم، نه لینک به یک سایت نقشهٔ جدا.

API `alwer-server` است. Express ^4.21.2 روی Node.js با `engines` برابر `>=20`. در `src/server.js` اول Postgres را چک می‌کنم، Redis را وصل می‌کنم، Socket.IO را به همان سرور HTTP می‌چسبانم، ورکرهای Bull را راه می‌اندازم، بعد گوش می‌دهم. Postgres یک `pg.Pool` در `src/infrastructure/database/postgresql.js` است با حداکثر ۲۰ اتصال. Redis کلاینت `ioredis` ^5.4.2 است. Bull ^4.16.5 است چون هشدار تطبیق و تطبیق مقابل نباید داخل درخواست HTTP بمانند. ورکر استخراج متن وقتی صف هوش مصنوعی روشن باشد شروع می‌شود. ورکر ذخیرهٔ embedding وقتی `embedPersistEnabled` باشد شروع می‌شود.

اپ `alwer_app` پوستهٔ Flutter است، نسخهٔ `1.0.13+79`، Dart `>=3.5.0 <4.0.0`. توضیح pubspec این است که بازار و مسیرهای اصلی نیتیو‌اند و WebView برای مسیر fallback. `webview_flutter` ^4.10.0 و `speech_to_text` ^7.0.0 را برای همین گذاشتم: برنامه باید بازار را نشان بدهد و ورودی گفتاری روی دستگاه بماند، نه اینکه کل محصول یک مرورگر جدا باشد.

## درخواست و پیشنهاد

ساخت درخواست `POST /api/requests` است با `authMiddleware` و `checkRequestQuota`. فهرست `GET /api/requests` است. مال خود کاربر `GET /api/requests/mine`. یک درخواست `GET /api/requests/:id` با احراز اختیاری. به‌روزرسانی `PUT`، حذف `DELETE`. ردیف‌های مرتبط `GET /api/requests/:id/related` و `GET /api/requests/:id/matches`. برآورد بازار `GET /api/requests/:id/market-estimate`. تماس `POST /api/requests/:id/contact`. تمدید `POST /api/requests/:id/renew`. سهمیه را روی ساخت گذاشتم نه روی خواندن؛ دیدن بازار نباید کیف پول را کم کند.

پیشنهادها در `quote.routes.js` همگی پشت `authMiddleware` هستند. `POST /api/requests/:id/quotes` علاوه بر آن `checkQuoteQuota` دارد. `GET /api/requests/:id/quotes` پیشنهادهای همان درخواست است. `GET /api/quotes/mine` و `GET /api/quotes/seller-dashboard` برای فروشنده است. `PATCH /api/quotes/:id` ویرایش است. `POST /api/quotes/:id/accept` قبول خریدار است و `POST /api/quotes/:id/withdraw` پس گرفتن فروشنده. قبول را جدا از چت گذاشتم تا «این قیمت را برداشتم» یک رویداد مشخص باشد، نه یک جمله داخل گفتگو.

جستجوی بازار `GET /api/search` است و `searchRateLimit` دارد. `GET /api/search/external` هم پشت همان محدودیت است.

## ورود، گفتگو، تصویر

`/api/auth` این‌ها را دارد: `POST /register`، `POST /verify`، `POST /totp/verify`، `POST /totp/recover`، `POST /logout`، `GET /me`. ثبت و تأیید و TOTP با `authRateLimit` هستند. خروج و `me` با `authSessionRateLimit` و `authMiddleware`. تأیید و بازیابی TOTP از `totpPendingMiddleware` رد می‌شوند. توکن را در `authMiddleware` از هدر `Authorization` می‌خوانم. کتابخانه‌های این مسیر از `alwer-server/package.json`: `jsonwebtoken` ^9.0.2، `bcryptjs` ^2.4.3، `otplib` ^13.4.1. رمز یک‌بارمصرف را ۱۲۰ ثانیه در Redis نگه می‌دارم (`OTP_TTL_SECONDS`).

چت HTTP در `chat.routes.js` همه با احراز هویت است: فهرست گفتگو، شروع گفتگو روی یک درخواست، خواندن و فرستادن پیام، ویرایش و حذف پیام، پنهان و آشکار کردن گفتگو. Socket.IO روی همان سرور به `connection`، `chat:join`، `chat:leave`، `chat:typing`، `chat:typing_stop` و `disconnect` گوش می‌دهد و `chat_typing` و `chat_typing_stop` را می‌فرستد. دست‌دادن توکن را از `socket.handshake.auth.token` یا هدر `Authorization` می‌خواند. تایپ را روی سوکت گذاشتم تا هر ضربه یک `POST` نباشد.

آپلود تصویر `POST /api/uploads` با `authMiddleware` و `upload.single('image')` است. `multer` فایل را می‌گیرد و `sharp` کتابخانهٔ تصویر سرور است. `POST /api/uploads/review` محدودیت نرخ مسیر هوش مصنوعی را دارد.

## بقیهٔ همان فرآیند

در `app.js` کلاه ایمنی Helmet، CORS، و بدنهٔ JSON با سقف ۱ مگابایت را قبل از مسیرها گذاشتم. این مسیرها روی همان Express هستند چون بازار یک API است، نه چند سرویس: `/api/alerts`، `/api/notifications`، `/api/wallet`، `/api/affiliate`، `/api/pricing`، `/api/reports`، `/api/ratings`، `/api/blog`، `/api/pages`، `/api/seo`، `/api/meta`، `/api/vehicles`، `/api/electronics`، `/api/users`، `/api/ai`، `/api/map`، `/api/bookmarks`، `/api/ads`، `/api/support`. مسیر مدیریت جدا سوار می‌شود. مسیر ناشناس به هندلر خطا در ته `app.js` می‌افتد.

`/api/content-review` را برای بررسی آگهی همکاران سوار کردم. سایت همکاران [alweryar.ir](https://alweryar.ir) است. فروشگاه‌هایی که کالا را به همین بازار می‌آورند [alwerchi.ir](https://alwerchi.ir) است. جزئیات آن دو را از روی خودشان می‌گویم، نه از روی این API.

اعلان مرورگر را با `web-push` ^3.6.7 گذاشتم تا هشدار دسته و شهر فقط داخل برنامه نماند. محدودیت نرخ HTTP با `express-rate-limit` ^8.5.2 است و برای مسیرهایی که باید بین چند نمونه مشترک بماند `rate-limit-redis` را کنارش گذاشتم.

## pgvector، خاموش تا بردار باشد

تطبیق واژگانی مسیر امن است. برای کوتاه‌کردن نامزدها ستون `requests.embedding` را از نوع `vector(768)` در مهاجرت `database/migrations/020_pgvector_request_embeddings.sql` اضافه کردم. `matchPgvectorEnabled` در `src/config/intelligence.js` با متغیر `ALWER_MATCH_PGVECTOR` پیش‌فرض خاموش است؛ همان فایل می‌گوید تا وقتی embedding هست روشن نشود و مسیر واژگانی شبکهٔ ایمنی بماند. اگر کوتاه‌فهرست برداری روشن باشد، ترکیب با مسیر واژگانی پیش‌فرض روشن است مگر `ALWER_MATCH_HYBRID` صفر شود. نام مدل تعبیه را اینجا نمی‌نویسم.

کیف پول شارژ ثبت را بعد از سهمیهٔ رایگان کم می‌کند. نام درگاه را از روی بستهٔ سرور به این معرفی نیاوردم.

## English

I arranged Alwer against a normal listing board. The buyer writes the need first. A seller prices that same request. Offers sit side by side so they can be compared. I did not remove sale listings; they stay parallel and show up in market search. If only listings remained, Alwer would be another board.

Site: [alwer.ir](https://alwer.ir)

### What I said on the homepage

The buyer path on [alwer.ir](https://alwer.ir): post a request, write the need, and if wanted use the on-site assistant that turns free text into structured fields. Then city, budget, and details, then compare offers. The seller path: a price offer on a related request, a sale listing for market search, and an alert for one category and one city. I added structured fields because a price offer is not comparable without a shared field. That page names the assistant Alwer Intelligence and says the processing runs on Alwer's own infrastructure. I am not naming a model here, because the public page does not name one either.

The first ten posts of each month, buy request or sale listing, are free, and the quota refills at the start of the month. After that, each post is 10,000 tomans from the wallet. The deal itself has no commission. That sentence is on the same page. In code, `FREE_REQUEST_LIMIT` is 10 and `REQUEST_FEE_AMOUNT` is 10000. `FREE_QUOTE_LIMIT` is also 10 and `QUOTE_FEE_AMOUNT` is 5000. I closed the quota window on the Jalali month in `jalaliPeriod.js` (`periodType` defaults to `monthly` in the quota catalog), because "the start of the month" for an Iranian user is not the first of the Gregorian month.

### Three shells, one API

The web client is `alwer-client`: Vite, React ^18.3.1, React Router ^7.1.1, Tailwind ^3.4.17, Leaflet ^1.9.4, and `socket.io-client` ^4.8.1. I wanted the map inside the market, not a link out to a separate map site.

The API is `alwer-server`. Express ^4.21.2 on Node.js, `engines` `>=20`. In `src/server.js` I check Postgres, connect Redis, attach Socket.IO to the same HTTP server, start the Bull workers, then listen. Postgres is a `pg.Pool` in `src/infrastructure/database/postgresql.js` with a max of 20. Redis is `ioredis` ^5.4.2. Bull is ^4.16.5 because match alerts and the opposite match must not stay inside the HTTP request. The text-extraction worker starts when the AI queue is enabled. The embedding worker starts when `embedPersistEnabled` is set.

The app `alwer_app` is a Flutter shell, version `1.0.13+79`, Dart `>=3.5.0 <4.0.0`. The pubspec says the market and the main paths are native and WebView is the fallback. I added `webview_flutter` ^4.10.0 and `speech_to_text` ^7.0.0 for that: the app has to show the market, and speech input stays on the device, instead of the whole product being a separate browser.

### Request and offer

Creating a request is `POST /api/requests` with `authMiddleware` and `checkRequestQuota`. The list is `GET /api/requests`. The caller's own list is `GET /api/requests/mine`. One request is `GET /api/requests/:id` with optional auth. Update is `PUT`, delete is `DELETE`. Related rows are `GET /api/requests/:id/related` and `GET /api/requests/:id/matches`. The market estimate is `GET /api/requests/:id/market-estimate`. Contact is `POST /api/requests/:id/contact`. Renew is `POST /api/requests/:id/renew`. I put the quota on create, not on read. Looking at the market must not touch the wallet.

Quotes in `quote.routes.js` all sit behind `authMiddleware`. `POST /api/requests/:id/quotes` also runs `checkQuoteQuota`. `GET /api/requests/:id/quotes` lists offers on that request. `GET /api/quotes/mine` and `GET /api/quotes/seller-dashboard` are for the seller. `PATCH /api/quotes/:id` edits. `POST /api/quotes/:id/accept` is the buyer accepting, and `POST /api/quotes/:id/withdraw` is the seller taking it back. I kept accept outside chat so "I took this price" is a distinct event, not a sentence in the thread.

Market search is `GET /api/search` behind `searchRateLimit`. `GET /api/search/external` uses the same limit.

### Sign-in, conversation, image

`/api/auth` exposes `POST /register`, `POST /verify`, `POST /totp/verify`, `POST /totp/recover`, `POST /logout`, and `GET /me`. Register, verify, and TOTP use `authRateLimit`. Logout and `me` use `authSessionRateLimit` and `authMiddleware`. TOTP verify and recover also pass `totpPendingMiddleware`. I read the token in `authMiddleware` from the `Authorization` header. Libraries on that path, from `alwer-server/package.json`: `jsonwebtoken` ^9.0.2, `bcryptjs` ^2.4.3, `otplib` ^13.4.1. I keep the one-time code in Redis for 120 seconds (`OTP_TTL_SECONDS`).

Chat HTTP in `chat.routes.js` all requires auth: list conversations, start one on a request, read and send messages, edit and delete a message, hide and unhide a conversation. Socket.IO on the same server listens for `connection`, `chat:join`, `chat:leave`, `chat:typing`, `chat:typing_stop`, and `disconnect`, and emits `chat_typing` and `chat_typing_stop`. The handshake reads a token from `socket.handshake.auth.token` or the `Authorization` header. I put typing on the socket so each keystroke is not a `POST`.

Image upload is `POST /api/uploads` with `authMiddleware` and `upload.single('image')`. `multer` takes the file and `sharp` is the server image library. `POST /api/uploads/review` uses the AI rate limit.

### The rest of the same process

In `app.js` I mount Helmet, CORS, and a JSON body limit of 1mb before the routers. These routes live on the same Express process because the market is one API, not several services: `/api/alerts`, `/api/notifications`, `/api/wallet`, `/api/affiliate`, `/api/pricing`, `/api/reports`, `/api/ratings`, `/api/blog`, `/api/pages`, `/api/seo`, `/api/meta`, `/api/vehicles`, `/api/electronics`, `/api/users`, `/api/ai`, `/api/map`, `/api/bookmarks`, `/api/ads`, `/api/support`. Admin routes mount separately. An unknown path falls through to the error handler at the bottom of `app.js`.

I mounted `/api/content-review` for collaborator review of listings. The collaborator site is [alweryar.ir](https://alweryar.ir). Shops that bring goods into this same market are [alwerchi.ir](https://alwerchi.ir). I describe those two from their own sites, not from this API.

Browser notifications use `web-push` ^3.6.7 so a category-and-city alert is not trapped inside the app. HTTP rate limits use `express-rate-limit` ^8.5.2, and I added `rate-limit-redis` beside it where the limit has to be shared across processes.

### pgvector, off until the vectors exist

Lexical matching is the safe path. For a shorter candidate list I added `requests.embedding` as `vector(768)` in `database/migrations/020_pgvector_request_embeddings.sql`. `matchPgvectorEnabled` in `src/config/intelligence.js` reads `ALWER_MATCH_PGVECTOR` and defaults to off. That file says to keep it off until embeddings exist, and to leave the lexical path as the safety net. If the vector shortlist is on, mixing it with the lexical path defaults to on unless `ALWER_MATCH_HYBRID` is set to zero. I am not writing an embedding model name here.

The wallet deducts the post charge after the free quota. I am not bringing a gateway name into this introduction from the server package.

## پروژه‌های مرتبط

- [hamejoo-intro](https://github.com/mnhashemabadi/hamejoo-intro): همه‌جو را برای بازدیدکننده‌ای گذاشتم که با یک عبارت آگهی را یک‌جا ببیند و برای جزئیات به سایت منبع برود.
- [kasbafzar-intro](https://github.com/mnhashemabadi/kasbafzar-intro): کسب‌افزار را برای کسی گذاشتم که فروش و مشتری و هزینه را ثبت کند و فاکتور را با لینک پرداخت برای مشتری بفرستد.
- [azadchi-intro](https://github.com/mnhashemabadi/azadchi-intro): آزادچی را برای آگهی و جستجو در مناطق آزاد گذاشتم؛ گفتگو با طرف معامله داخل خود آزادچی می‌ماند.
- [afzi-intro](https://github.com/mnhashemabadi/afzi-intro): افزی را برای کوتاه کردن یک نشانی http یا https گذاشتم؛ باز کردن لینک همان صفحه را باز می‌کند.
- [alweryar-intro](https://github.com/mnhashemabadi/alweryar-intro): آلوریار را برای همکاری در بررسی آگهی و همکاری در فروش آلور گذاشتم.
- [alwerchi-intro](https://github.com/mnhashemabadi/alwerchi-intro): آلورچی را برای فروشگاهی گذاشتم که کالا و موجودی‌اش در بازار آلور دیده شود و خریدار در آلور بماند.

## Related

- [hamejoo-intro](https://github.com/mnhashemabadi/hamejoo-intro): I built Hamejoo for a visitor who types one phrase, sees listings in one place, and opens the original site for the listing itself.
- [kasbafzar-intro](https://github.com/mnhashemabadi/kasbafzar-intro): I built Kasbafzar for someone who records sales, customers, and expenses, and sends the customer an invoice with a payment link.
- [azadchi-intro](https://github.com/mnhashemabadi/azadchi-intro): I built Azadchi for listings and search inside free zones; the conversation with the other party stays in Azadchi.
- [afzi-intro](https://github.com/mnhashemabadi/afzi-intro): I built Afzi to shorten an http or https address; opening the short link opens that same page.
- [alweryar-intro](https://github.com/mnhashemabadi/alweryar-intro): I built Alweryar for collaboration on listing review and on sales for Alwer.
- [alwerchi-intro](https://github.com/mnhashemabadi/alwerchi-intro): I built Alwerchi for a shop whose goods and stock show on the Alwer market while the buyer stays on Alwer.

یادداشت مهندسی کوتاه‌تر: [docs/engineering.md](docs/engineering.md)

Shorter engineering note: [docs/engineering.md](docs/engineering.md)
