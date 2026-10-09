# آلور

آلور را برعکس بازار آگهی معمولی چیدم. خریدار نیازش را می‌نویسد و فروشنده روی همان درخواست قیمت می‌دهد. پیشنهادها کنار هم‌اند تا بتوان آن‌ها را مقایسه کرد. آگهی فروش را حذف نکردم؛ کنار درخواست می‌ماند و در جستجوی بازار دیده می‌شود. اگر کار به آگهی خلاصه می‌شد، آلور یک تابلوی دیگر بود.

سایت: [alwer.ir](https://alwer.ir)

## آنچه روی صفحهٔ اصلی گفتم

مسیر خریدار در [alwer.ir](https://alwer.ir) این است: ثبت درخواست، نوشتن نیاز، و اگر بخواهد دستیار همان سایت که متن آزاد را به فیلد ساخت‌یافته تبدیل می‌کند. بعد شهر و بودجه و جزئیات، بعد مقایسهٔ پیشنهادها. مسیر فروشنده: پیشنهاد قیمت روی درخواست مرتبط، آگهی فروش برای جستجوی بازار، و هشدار برای یک دسته و یک شهر. فیلد ساخت‌یافته را برای همین گذاشتم؛ پیشنهاد قیمت بدون فیلد مشترک قابل مقایسه نیست. همان صفحه دستیار را Alwer Intelligence می‌نامد.

سهمیهٔ ثبت رایگان ماهانه را روی ماه جلالی حساب می‌کنم، چون «اول ماه» برای کاربر ایرانی اول ماه میلادی نیست. سهمیه روی ساخت درخواست و پیشنهاد است، نه روی خواندن؛ دیدن بازار نباید از کیف پول کم کند.

## سه پوسته، یک API

کلاینت وب با Vite ساخته می‌شود: React ^18.3.1، React Router ^7.1.1، Tailwind ^3.4.17، Leaflet ^1.9.4 و `socket.io-client` ^4.8.1. نقشه را داخل بازار خواستم، نه لینک به یک سایت نقشهٔ جدا.

API با Express ^4.21.2 روی Node.js 20 به بالا نوشته شده. هنگام بالا آمدن، اول Postgres را چک می‌کنم، Redis را وصل می‌کنم، Socket.IO را به همان سرور HTTP می‌چسبانم، ورکرهای Bull را راه می‌اندازم و بعد گوش می‌دهم. Postgres با `pg` و استخر اتصال باز می‌شود و Redis با `ioredis` ^5.4.2. Bull ^4.16.5 را گذاشتم چون هشدار تطبیق و تطبیق مقابل نباید داخل درخواست HTTP بمانند. استخراج متن دستیار هم روی همین صف‌ها اجرا می‌شود.

اپ موبایل پوستهٔ Flutter است، نسخهٔ `1.0.13+79` با Dart `>=3.5.0 <4.0.0`. بازار و مسیرهای اصلی نیتیو هستند و WebView مسیر جایگزین است. `webview_flutter` ^4.10.0 و `speech_to_text` ^7.0.0 را برای همین گذاشتم: برنامه باید بازار را نشان بدهد و ورودی گفتاری روی خود دستگاه بماند، نه اینکه کل محصول یک مرورگر جدا باشد.

## درخواست و پیشنهاد

هر درخواست ساخته، ویرایش و تمدید می‌شود و کنارش پیشنهادها، ردیف‌های مرتبط و برآورد قیمت بازار می‌آید. فروشنده داشبورد پیشنهادهای خودش را دارد. قبول پیشنهاد را جدا از چت گذاشتم تا «این قیمت را برداشتم» یک رویداد مشخص باشد، نه یک جمله داخل گفتگو. فروشنده هم می‌تواند پیشنهادش را پس بگیرد.

جستجوی بازار محدودیت نرخ جداگانه دارد.

## گفتگو و تصویر

چت بین خریدار و فروشنده روی یک درخواست باز می‌شود. پیام خواندن و فرستادن، ویرایش و حذف، و پنهان کردن گفتگو را دارد. وضعیت «در حال تایپ» را روی Socket.IO گذاشتم تا هر ضربهٔ کلید یک درخواست HTTP نباشد.

تصویر با `multer` دریافت و با `sharp` روی سرور پردازش می‌شود.

## بقیهٔ همان فرآیند

Helmet و CORS و سقف ۱ مگابایت برای بدنهٔ JSON پیش از همهٔ مسیرها اعمال می‌شود. هشدار، اعلان، کیف پول، امتیازدهی، بلاگ، نقشه، نشان‌گذاری و پشتیبانی همه روی همان Express هستند، چون بازار یک API است، نه چند سرویس.

اعلان مرورگر را با `web-push` ^3.6.7 گذاشتم تا هشدار دسته و شهر فقط داخل برنامه نماند. محدودیت نرخ HTTP با `express-rate-limit` ^8.5.2 است و برای جایی که شمارش باید بین چند نمونه مشترک بماند `rate-limit-redis` را کنارش گذاشتم. آزمون سرتاسری با Playwright است.

## English

I arranged Alwer against a normal listing board. The buyer writes the need, and a seller prices that same request. Offers sit side by side so they can be compared. I did not remove sale listings; they stay parallel and show up in market search. If only listings remained, Alwer would be another board.

Site: [alwer.ir](https://alwer.ir)

### What I said on the homepage

The buyer path on [alwer.ir](https://alwer.ir): post a request, write the need, and if wanted use the on-site assistant that turns free text into structured fields. Then city, budget, and details, then compare offers. The seller path: a price offer on a related request, a sale listing for market search, and an alert for one category and one city. I added structured fields because a price offer is not comparable without a shared field. That page names the assistant Alwer Intelligence.

I count the monthly free quota on the Jalali month, because "the start of the month" for an Iranian user is not the start of the Gregorian month. The quota applies when a request or an offer is created, not when it is read. Looking at the market must not touch the wallet.

### Three shells, one API

The web client is built with Vite: React ^18.3.1, React Router ^7.1.1, Tailwind ^3.4.17, Leaflet ^1.9.4, and `socket.io-client` ^4.8.1. I wanted the map inside the market, not a link out to a separate map site.

The API is Express ^4.21.2 on Node.js 20 or later. On startup I check Postgres, connect Redis, attach Socket.IO to the same HTTP server, start the Bull workers, then listen. Postgres is opened with a `pg` pool, Redis with `ioredis` ^5.4.2. Bull is ^4.16.5 because match alerts and the opposite match must not stay inside the HTTP request. The assistant's text extraction runs on the same queues.

The mobile app is a Flutter shell, version `1.0.13+79`, Dart `>=3.5.0 <4.0.0`. The market and the main paths are native and WebView is the fallback. I added `webview_flutter` ^4.10.0 and `speech_to_text` ^7.0.0 for that: the app has to show the market, and speech input stays on the device, instead of the whole product being a separate browser.

### Request and offer

A request can be created, edited, and renewed, and it carries its offers, related rows, and a market price estimate. A seller has a dashboard of their own offers. I kept accepting an offer outside chat so "I took this price" is a distinct event, not a sentence in the thread. A seller can also withdraw an offer.

Market search has its own rate limit.

### Conversation and image

Chat between buyer and seller opens on a request. It covers reading and sending, editing and deleting, and hiding a conversation. I put the typing indicator on Socket.IO so each keystroke is not an HTTP request.

Images are received with `multer` and processed on the server with `sharp`.

### The rest of the same process

Helmet, CORS, and a 1mb JSON body limit apply before every router. Alerts, notifications, the wallet, ratings, the blog, the map, bookmarks, and support all live on the same Express process, because the market is one API, not several services.

Browser notifications use `web-push` ^3.6.7 so a category-and-city alert is not trapped inside the app. HTTP rate limits use `express-rate-limit` ^8.5.2, and I added `rate-limit-redis` beside it where the count has to be shared across processes. End-to-end tests use Playwright.

## پروژه‌های مرتبط

- [hamejoo-intro](https://github.com/mnhashemabadi/hamejoo-intro): همه‌جو را برای بازدیدکننده‌ای ساختم که با یک عبارت آگهی را یک‌جا ببیند و برای جزئیات به سایت منبع برود.
- [kasbafzar-intro](https://github.com/mnhashemabadi/kasbafzar-intro): کسب‌افزار را برای کسی ساختم که فروش و مشتری و هزینه را ثبت کند و فاکتور را با لینک پرداخت برای مشتری بفرستد.
- [azadchi-intro](https://github.com/mnhashemabadi/azadchi-intro): آزادچی را برای آگهی و جستجو در مناطق آزاد ساختم؛ گفتگو با طرف معامله داخل خود آزادچی می‌ماند.
- [afzi-intro](https://github.com/mnhashemabadi/afzi-intro): افزی را برای کوتاه کردن یک نشانی http یا https ساختم؛ باز کردن لینک کوتاه همان صفحه را باز می‌کند.
- [alweryar-intro](https://github.com/mnhashemabadi/alweryar-intro): آلوریار را برای همکاری در بررسی آگهی و همکاری در فروش آلور ساختم.
- [alwerchi-intro](https://github.com/mnhashemabadi/alwerchi-intro): آلورچی را برای فروشگاهی ساختم که کالا و موجودی‌اش در بازار آلور دیده شود و خریدار در آلور بماند.

## Related

- [hamejoo-intro](https://github.com/mnhashemabadi/hamejoo-intro): I built Hamejoo for a visitor who types one phrase, sees listings in one place, and opens the original site for the listing itself.
- [kasbafzar-intro](https://github.com/mnhashemabadi/kasbafzar-intro): I built Kasbafzar for someone who records sales, customers, and expenses, and sends the customer an invoice with a payment link.
- [azadchi-intro](https://github.com/mnhashemabadi/azadchi-intro): I built Azadchi for listings and search inside free zones; the conversation with the other party stays in Azadchi.
- [afzi-intro](https://github.com/mnhashemabadi/afzi-intro): I built Afzi to shorten an http or https address; opening the short link opens that same page.
- [alweryar-intro](https://github.com/mnhashemabadi/alweryar-intro): I built Alweryar for collaboration on listing review and on sales for Alwer.
- [alwerchi-intro](https://github.com/mnhashemabadi/alwerchi-intro): I built Alwerchi for a shop whose goods and stock show on the Alwer market while the buyer stays on Alwer.

یادداشت مهندسی کوتاه‌تر: [docs/engineering.md](docs/engineering.md)

Shorter engineering note: [docs/engineering.md](docs/engineering.md)
