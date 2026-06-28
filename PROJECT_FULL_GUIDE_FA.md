# راهنمای کامل فنی پروژه پویا

## معرفی کلی پروژه: این پروژه دقیقاً چه کاری انجام می‌دهد؟

این مخزن یک سامانه مدیریت رستوران با نام «پویا» است. پروژه از یک Backend مبتنی بر Django REST Framework و یک Frontend مبتنی بر Vue 3/Vite تشکیل شده و برای اجرا به‌صورت برنامه دسکتاپ نیز از PyWebView استفاده می‌کند. کاربرد اصلی آن ثبت سفارش‌های دستی رستوران، مدیریت منو و موجودی روزانه، نمایش وضعیت سفارش برای آشپزخانه و مشتری، چاپ فاکتور حرارتی، و نمایش آمار/پیش‌بینی فروش است.

جریان کلی به این شکل است: کاربر وارد صفحه Login می‌شود، بر اساس نقش خود به Dashboard فروش، برد آشپزخانه، صفحه وضعیت سفارش یا صفحه آمار هدایت می‌شود، سپس سفارش‌ها از طریق API در Backend ذخیره شده و وضعیت آن‌ها توسط آشپزخانه تغییر می‌کند.

## تکنولوژی‌های استفاده‌شده

| بخش | تکنولوژی | محل مشاهده |
|---|---|---|
| Frontend | Vue 3، Vite، Vue Router، Element Plus، Axios، vue-i18n | `pouya/pywebview-vue-app/frontend/package.json` |
| نمودارها | Chart.js و vue-chartjs | `pouya/pywebview-vue-app/frontend/package.json` و `src/components/AppChart.vue` |
| Backend | Python، Django 5.2.4، Django REST Framework | `pouya/backend/requirements.txt` |
| احراز هویت | DRF TokenAuthentication، SessionAuthentication، BasicAuthentication | `pouya/backend/restaurant_backend/settings.py` |
| پایگاه داده | SQLite | `pouya/backend/restaurant_backend/settings.py` و `pouya/backend/db.sqlite3` |
| Desktop wrapper | PyWebView | `pouya/pywebview-vue-app/main.py` |
| چاپ | سرویس JavaScript برای PyWebView + ماژول Python چاپ حرارتی | `src/services/printerService.js` و `landscape_thermal_print.py` |
| زبان/RTL | فارسی، فونت Vazir، Element Plus RTL | `src/main.js` و `src/App.vue` |
| تحلیل داده | pandas/numpy و منطق آماری ساده | `pouya/backend/analytics/services.py` |

## ساختار فولدرها و نقش هر فولدر

| مسیر | نقش |
|---|---|
| `pouya/backend/` | پروژه Django Backend، پایگاه داده SQLite، اپ‌های Django، اسکریپت‌ها و مستندات قبلی |
| `pouya/backend/restaurant_backend/` | تنظیمات اصلی Django و URL routing اصلی |
| `pouya/backend/menu/` | مدل‌ها، APIها و serializerهای دسته‌بندی، آیتم منو و موجودی روزانه |
| `pouya/backend/orders/` | مدل‌ها و APIهای میز، سفارش و آیتم سفارش |
| `pouya/backend/users/` | login/logout/register/profile و permissionهای مبتنی بر گروه |
| `pouya/backend/analytics/` | endpointهای آمار و پیش‌بینی فروش |
| `pouya/backend/docs/` | مستندات و لاگ‌های قبلی پروژه |
| `pouya/pywebview-vue-app/` | wrapper دسکتاپ PyWebView و فایل‌های مربوط به چاپ |
| `pouya/pywebview-vue-app/frontend/` | اپ Vue 3/Vite |
| `pouya/pywebview-vue-app/frontend/src/views/` | صفحات اصلی: Login، Dashboard، KitchenOrders، Statistics، OrderStatus |
| `pouya/pywebview-vue-app/frontend/src/components/` | کامپوننت‌های reusable مثل برد وضعیت سفارش و نمودار |
| `pouya/pywebview-vue-app/frontend/src/services/` | سرویس چاپ فاکتور |
| `pouya/pywebview-vue-app/frontend/src/router/` | تعریف routeها و guardهای نقش‌محور |
| `pouya/pywebview-vue-app/frontend/src/assets/fonts/` | فونت محلی Vazir |
| `pouya/run_backend.bat` | اجرای Backend روی پورت 4040 در Windows |
| `pouya/run_pywebview.bat` | اجرای اپ دسکتاپ PyWebView در Windows |

## مسیر اجرای برنامه از entry point تا صفحات اصلی

### Frontend تحت مرورگر/توسعه

1. `index.html` در `pouya/pywebview-vue-app/frontend/` اپ را mount می‌کند.
2. `src/main.js` اپ Vue را می‌سازد، Router، Element Plus با locale فارسی و i18n را نصب می‌کند.
3. `src/App.vue` فقط `router-view` را نمایش می‌دهد و کلید F11 را برای fullscreen در حالت مرورگر مدیریت می‌کند.
4. `src/router/index.js` بر اساس route و نقش کاربر صفحه مناسب را نمایش می‌دهد.

### اجرای دسکتاپ با PyWebView

1. `pouya/pywebview-vue-app/main.py` مسیر `frontend/dist` را پیدا می‌کند.
2. یک HTTP server محلی برای فایل‌های build‌شده Vue بالا می‌آورد.
3. یک window با عنوان «پویا - سیستم مدیریت رستوران» می‌سازد.
4. API پایتونی `Api` را به JavaScript وصل می‌کند تا چاپ از داخل frontend قابل صدا زدن باشد.

### Backend

1. `manage.py` پروژه Django را اجرا می‌کند.
2. `restaurant_backend/settings.py` اپ‌ها، DB، CORS و DRF را تنظیم می‌کند.
3. `restaurant_backend/urls.py` routeهای REST را با `DefaultRouter` و endpointهای auth/analytics تعریف می‌کند.

## توضیح routing / pages / navigation

Routeهای اصلی در `pouya/pywebview-vue-app/frontend/src/router/index.js` تعریف شده‌اند:

| مسیر | صفحه | نقش‌های مجاز |
|---|---|---|
| `/` | redirect به `/login` | عمومی |
| `/login` | `Login.vue` | بدون نیاز به auth |
| `/dashboard` | `Dashboard.vue` | `admin`, `chef`, `cashier` |
| `/kitchen` | `KitchenOrders.vue` | `chef`, `admin` |
| `/statistics` | `Statistics.vue` | فقط `admin` |
| `/order-status` | `OrderStatus.vue` | `customer`, `chef`, `cashier`, `admin` |

Guard سراسری routeها از `localStorage` مقدارهای `isAuthenticated`, `role`, `roles` را می‌خواند. اگر کاربر لاگین نکرده باشد به `/login` می‌رود. اگر نقش لازم را نداشته باشد، تابع `homeForRoles` صفحه پیش‌فرض نقش را انتخاب می‌کند.

## توضیح کامپوننت‌ها و صفحات مهم

| فایل | نقش |
|---|---|
| `src/views/Login.vue` | فرم ورود؛ ارسال username/password به `/auth/login/`؛ ذخیره token و نقش‌ها در `localStorage` |
| `src/views/Dashboard.vue` | صفحه اصلی فروش: نمایش غذاها، افزودن مشتری/سفارش، محاسبه مبلغ و مالیات، ثبت سفارش، چاپ فاکتور، مشاهده سفارش‌های قبلی امروز |
| `src/views/KitchenOrders.vue` | برد آشپزخانه با ستون‌های pending/preparing/ready؛ تغییر وضعیت سفارش؛ polling هر ۱۵ ثانیه |
| `src/views/Statistics.vue` | داشبورد مدیریتی آمار و پیش‌بینی، نمودار درآمد/سفارش، غذاهای پرفروش، ساعات شلوغ، پیشنهاد تأمین موجودی |
| `src/views/OrderStatus.vue` | صفحه وضعیت سفارش‌ها برای مشتری یا کارکنان |
| `src/components/OrderStatusBoard.vue` | دریافت لیست کم‌حجم سفارش‌های امروز از API و نمایش وضعیت |
| `src/components/AppChart.vue` | wrapper عمومی برای نمودارهای Bar/Line با Chart.js |
| `src/services/printerService.js` | چاپ فاکتور از طریق PyWebView و fallback چاپ مرورگر |

## توضیح state management

در پروژه state management متمرکز مثل Pinia یا Vuex وجود ندارد. وضعیت برنامه عمدتاً با Composition API در هر view مدیریت می‌شود (`ref`, `reactive`, `computed`). وضعیت احراز هویت و نقش‌ها در `localStorage` نگهداری می‌شود. این روش ساده است، اما برای پروژه بزرگ‌تر ممکن است باعث تکرار کد logout، sync نشدن state بین صفحات، و سخت‌تر شدن تست شود.

## توضیح API calls / backend / services

Frontend همه callهای API را در `pouya/pywebview-vue-app/frontend/src/api.js` متمرکز کرده است. `axios.create` با baseURL برابر `http://localhost:4040/api` ساخته شده و interceptor درخواست، token را با قالب `Authorization: Token <token>` اضافه می‌کند.

گروه endpointهای frontend:

| namespace | نمونه endpoint | کاربرد |
|---|---|---|
| `auth` | `/auth/login/`, `/auth/logout/`, `/auth/profile/` | ورود، خروج، پروفایل |
| `categories` | `/categories/` | CRUD دسته‌بندی |
| `menu` | `/menu-items/`, `/menu-items/with_inventory/` | غذاها و موجودی امروز |
| `inventory` | `/inventory/`, `/inventory/bulk_update/` | مدیریت موجودی روزانه |
| `tables` | `/tables/` | مدیریت میزها |
| `orders` | `/orders/`, `/orders/today/`, `/orders/status_board/`, `/orders/{id}/update_status/` | سفارش‌ها و وضعیت |
| `orderItems` | `/order-items/` | آیتم‌های سفارش |
| `analytics` | `/analytics/statistics/`, `/analytics/predictions/` | آمار و پیش‌بینی |

در Backend، `restaurant_backend/urls.py` برای مدل‌های اصلی از `DefaultRouter` استفاده می‌کند و endpointهای auth و analytics را جداگانه تعریف کرده است.

## توضیح database یا data model

پایگاه داده SQLite است و فایل فعلی در `pouya/backend/db.sqlite3` قرار دارد. مدل‌های اصلی:

### `menu.models`

| مدل | فیلدهای مهم | توضیح |
|---|---|---|
| `Category` | `name`, `description` | دسته‌بندی غذاها |
| `MenuItem` | `name`, `product_code`, `price_without_tax`, `vat_rate`, `price_with_tax`, `category`, `is_available`, `is_active` | آیتم منو؛ قیمت با مالیات در `save` محاسبه می‌شود |
| `DailyInventory` | `menu_item`, `quantity_available`, `date` | موجودی روزانه؛ برای هر غذا و تاریخ unique است |

### `orders.models`

| مدل | فیلدهای مهم | توضیح |
|---|---|---|
| `Table` | `number`, `name`, `capacity`, `status`, `is_active` | میز رستوران |
| `Order` | `order_number`, `customer_name`, `customer_phone`, `total_amount`, `status`, `created_by`, `table` | سفارش؛ شماره سفارش بر اساس تاریخ جلالی و شمارنده روز ساخته می‌شود |
| `OrderItem` | `order`, `menu_item`, `quantity`, `unit_price`, `total_price`, `notes`, `product_info` | ردیف سفارش؛ قیمت کل در `save` محاسبه می‌شود |

## توضیح authentication / authorization

احراز هویت در Backend با DRF TokenAuthentication انجام می‌شود. کاربر با `POST /api/auth/login/` وارد می‌شود و token دریافت می‌کند. Frontend این token را در `localStorage` ذخیره کرده و در header درخواست‌های بعدی قرار می‌دهد.

مجوزها در `pouya/backend/users/permissions.py` بر اساس گروه‌های Django تعریف شده‌اند:

| نقش | دسترسی کلی |
|---|---|
| `admin` | دسترسی کامل |
| `chef` | مدیریت سفارش‌ها و موجودی روزانه، خواندن منو |
| `cashier` | مدیریت سفارش‌ها و فاکتور/فروش |
| `customer` | مشاهده وضعیت سفارش |

در پروژه به‌صورت قطعی مشخص نیست که کاربران و گروه‌های اولیه دقیقاً چگونه در محیط production ساخته می‌شوند؛ اسکریپت seed برای داده نمونه وجود دارد، اما مستندات کامل provisioning کاربرها در root پروژه دیده نشد.

## توضیح فایل‌های config و environment variables

| فایل | توضیح |
|---|---|
| `pouya/backend/restaurant_backend/settings.py` | تنظیمات Django، SQLite، CORS، DRF، locale فارسی، timezone تهران |
| `pouya/backend/restaurant_backend/urls.py` | routeهای Backend |
| `pouya/pywebview-vue-app/frontend/vite.config.js` | تنظیمات Vite با plugin Vue |
| `pouya/pywebview-vue-app/frontend/package.json` | dependencyها و scriptهای frontend |
| `pouya/backend/requirements.txt` | dependencyهای Python |
| `pouya/run_backend.bat` | اجرای Backend با venv ویندوزی روی پورت 4040 |
| `pouya/run_pywebview.bat` | اجرای PyWebView با venv ویندوزی |

فایل `.env` مشخصی در بررسی مخزن دیده نشد. همچنین `SECRET_KEY` و `DEBUG=True` در `settings.py` hardcoded هستند که برای production ریسک امنیتی محسوب می‌شود.

## روش نصب و اجرا به صورت مرحله‌به‌مرحله

### Backend

```bash
cd pouya/backend
python -m venv venv
# Windows:
venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver 0.0.0.0:4040
```

در ویندوز، فایل `pouya/run_backend.bat` همین اجرای Backend را با venv موجود انجام می‌دهد.

### Frontend در حالت توسعه

```bash
cd pouya/pywebview-vue-app/frontend
npm install
npm run dev
```

نکته: baseURL در frontend روی `http://localhost:4040/api` تنظیم شده است، بنابراین Backend باید روی پورت 4040 اجرا شود.

### اجرای Desktop/PyWebView

```bash
cd pouya/pywebview-vue-app/frontend
npm install
npm run build
cd ..
python -m venv venv
venv\Scripts\activate  # Windows
pip install -r requirements.txt
python main.py
```

در ویندوز می‌توان از `pouya/run_pywebview.bat` استفاده کرد، به شرط آن‌که venv قبلاً ساخته شده باشد و frontend build شده باشد.

## روش build گرفتن و deploy کردن

### Frontend

در `package.json` script زیر وجود دارد:

```bash
npm run build
```

خروجی build در `pouya/pywebview-vue-app/frontend/dist` تولید می‌شود و `main.py` PyWebView همین فولدر را serve می‌کند.

### Backend

روش production deployment به‌صورت قطعی در پروژه مشخص نیست. چون `DEBUG=True`، `ALLOWED_HOSTS=['*']` و SQLite استفاده شده، تنظیمات فعلی بیشتر مناسب توسعه/محیط داخلی است. برای deploy واقعی باید حداقل `SECRET_KEY`, `DEBUG`, `ALLOWED_HOSTS`, دیتابیس production، static/media serving و HTTPS به‌درستی تنظیم شوند.

## user flow اصلی پروژه

1. کاربر وارد `/login` می‌شود.
2. username/password را وارد می‌کند.
3. Backend token و اطلاعات نقش را برمی‌گرداند.
4. Frontend اطلاعات auth را در `localStorage` ذخیره می‌کند.
5. بر اساس نقش:
   - مدیر به Dashboard و Statistics دسترسی دارد.
   - آشپز می‌تواند Kitchen board و سفارش‌ها را ببیند/تغییر دهد.
   - صندوقدار سفارش ثبت می‌کند و فاکتور چاپ می‌کند.
   - مشتری وضعیت سفارش‌ها را می‌بیند.
6. در Dashboard، کاربر غذاها را با موجودی روز می‌بیند، آیتم‌ها را به سفارش اضافه می‌کند، سفارش را ثبت می‌کند و در صورت نیاز فاکتور چاپ می‌کند.
7. آشپز در KitchenOrders وضعیت سفارش را از pending به preparing، ready و completed تغییر می‌دهد.
8. صفحه OrderStatus وضعیت سفارش‌های امروز را نمایش می‌دهد.
9. مدیر در Statistics آمار فروش و پیش‌بینی‌ها را بررسی می‌کند.

## developer flow

برای توسعه‌دهنده جدید پیشنهاد می‌شود:

1. از `pouya/backend/restaurant_backend/urls.py` شروع کند تا APIهای موجود را ببیند.
2. مدل‌ها را در `menu/models.py` و `orders/models.py` بخواند.
3. serializerها را در `menu/serializers.py` و `orders/serializers.py` بررسی کند تا payloadهای API را بفهمد.
4. `users/permissions.py` را بخواند تا نقش‌ها و محدودیت‌ها مشخص شود.
5. در frontend، ابتدا `src/router/index.js` و `src/api.js` را بخواند.
6. سپس هر صفحه را جداگانه بررسی کند: `Login.vue`, `Dashboard.vue`, `KitchenOrders.vue`, `Statistics.vue`, `OrderStatus.vue`.
7. اگر روی چاپ کار می‌کند، `src/services/printerService.js` و `pywebview-vue-app/main.py` و `landscape_thermal_print.py` را بررسی کند.

## نقاط ضعف، ریسک‌ها، باگ‌های احتمالی، missing documentation و پیشنهادهای بهبود

| مورد | ریسک/توضیح | پیشنهاد |
|---|---|---|
| `DEBUG=True` و `SECRET_KEY` hardcoded | ناامن برای production | انتقال به environment variables |
| `ALLOWED_HOSTS=['*']` و CORS باز | ریسک امنیتی | محدود کردن host/origin در production |
| SQLite | مناسب برای توسعه/محیط کوچک، نه بار بالا | PostgreSQL یا دیتابیس production |
| نبود state manager | تکرار localStorage/logout و state پراکنده | استفاده از Pinia برای auth و session |
| نبود تست جدی | فایل‌های tests تقریباً خالی یا نامشخص هستند | افزودن unit/integration tests برای API و frontend |
| API frontend hardcoded روی localhost:4040 | مشکل در deploy چندمحیطی | استفاده از env مثل `VITE_API_BASE_URL` |
| route guard مبتنی بر localStorage | امکان stale data در client | اعتبارسنجی profile/token در شروع اپ |
| register عمومی | اگر برای production فعال بماند، ریسک ایجاد کاربر ناخواسته دارد | محدود کردن ثبت‌نام یا admin-only کردن آن |
| اسناد deploy ناقص | روش production دقیق مشخص نیست | افزودن guide رسمی deployment |
| وجود فایل‌های build/cache/db در repo | احتمال سنگین شدن repo و افشای داده | بازبینی `.gitignore` و سیاست نگهداری داده |

## خلاصه نهایی پروژه

این پروژه یک سیستم مدیریت رستوران فارسی و راست‌به‌چپ است که Backend آن با Django REST Framework و Frontend آن با Vue 3 ساخته شده است. سامانه امکان مدیریت منو، موجودی روزانه، میزها، ثبت سفارش، تغییر وضعیت سفارش، نمایش وضعیت سفارش برای مشتری/کارکنان، چاپ رسید و مشاهده آمار فروش را فراهم می‌کند.

معماری پروژه به‌صورت client/server است: Vue از طریق Axios به APIهای Django روی `localhost:4040/api` وصل می‌شود. PyWebView برای تبدیل frontend build‌شده به یک برنامه دسکتاپ و دسترسی به چاپگر حرارتی استفاده می‌شود.

پروژه برای یک محیط داخلی رستوران مناسب طراحی شده، اما برای production عمومی نیازمند سخت‌سازی امنیتی، تنظیم env، مستندسازی deploy، تست‌های بیشتر و احتمالاً مهاجرت دیتابیس از SQLite به دیتابیس production است.
