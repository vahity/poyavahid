# خلاصه سریع پروژه پویا

## پروژه چیست؟

«پویا» یک سامانه مدیریت رستوران فارسی است. با آن می‌توان منو و موجودی غذاها را مدیریت کرد، سفارش ثبت کرد، وضعیت سفارش‌ها را در آشپزخانه تغییر داد، وضعیت سفارش را به مشتری نشان داد، فاکتور چاپ کرد و آمار/پیش‌بینی فروش را دید.

## با چه تکنولوژی‌هایی ساخته شده؟

| بخش | تکنولوژی |
|---|---|
| Frontend | Vue 3، Vite، Vue Router، Element Plus، Axios |
| Backend | Django 5.2.4، Django REST Framework |
| Auth | DRF Token Authentication و گروه‌های Django |
| Database | SQLite |
| Desktop | PyWebView |
| Charts/Analytics | Chart.js، vue-chartjs، pandas/numpy |
| Styling | CSS scoped، Element Plus، فونت Vazir، RTL |

## مهم‌ترین فایل‌ها کدام‌اند؟

| فایل | اهمیت |
|---|---|
| `pouya/backend/restaurant_backend/settings.py` | تنظیمات اصلی Django، دیتابیس، CORS و DRF |
| `pouya/backend/restaurant_backend/urls.py` | مسیرهای API |
| `pouya/backend/menu/models.py` | مدل‌های دسته‌بندی، غذا و موجودی |
| `pouya/backend/orders/models.py` | مدل‌های میز، سفارش و آیتم سفارش |
| `pouya/backend/users/permissions.py` | نقش‌ها و سطح دسترسی |
| `pouya/backend/users/views.py` | login/logout/register/profile |
| `pouya/backend/analytics/services.py` | محاسبه آمار و پیش‌بینی |
| `pouya/pywebview-vue-app/frontend/src/main.js` | شروع اپ Vue |
| `pouya/pywebview-vue-app/frontend/src/router/index.js` | routeها و guard نقش‌محور |
| `pouya/pywebview-vue-app/frontend/src/api.js` | تمام callهای API |
| `pouya/pywebview-vue-app/frontend/src/views/Dashboard.vue` | صفحه اصلی فروش و ثبت سفارش |
| `pouya/pywebview-vue-app/frontend/src/views/KitchenOrders.vue` | برد آشپزخانه |
| `pouya/pywebview-vue-app/frontend/src/views/Statistics.vue` | صفحه آمار و پیش‌بینی |
| `pouya/pywebview-vue-app/main.py` | اجرای دسکتاپ با PyWebView |

## چطور اجرا می‌شود؟

### Backend

```bash
cd pouya/backend
python -m venv venv
source venv/bin/activate  # Linux/macOS
# یا در Windows: venv\Scripts\activate
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver 0.0.0.0:4040
```

### Frontend توسعه

```bash
cd pouya/pywebview-vue-app/frontend
npm install
npm run dev
```

### Desktop/PyWebView

```bash
cd pouya/pywebview-vue-app/frontend
npm run build
cd ..
python main.py
```

## مهم‌ترین جریان کاری برنامه چیست؟

1. کاربر وارد سیستم می‌شود.
2. بر اساس نقش به صفحه مناسب هدایت می‌شود.
3. صندوقدار/مدیر در Dashboard غذاها را انتخاب و سفارش ثبت می‌کند.
4. Backend سفارش و آیتم‌ها را ذخیره می‌کند.
5. آشپز در KitchenOrders وضعیت سفارش را تغییر می‌دهد.
6. مشتری یا کارکنان در OrderStatus وضعیت سفارش را می‌بینند.
7. مدیر در Statistics آمار و پیش‌بینی فروش را بررسی می‌کند.

## سه نکته مهم برای ارائه به استاد

1. پروژه full-stack است: Vue در frontend و Django REST Framework در backend با API به هم وصل شده‌اند.
2. دسترسی‌ها نقش‌محور هستند: admin، chef، cashier و customer هرکدام مسیرها و APIهای مجاز متفاوت دارند.
3. پروژه فقط وب نیست؛ با PyWebView می‌تواند به شکل برنامه دسکتاپ اجرا شود و چاپ فاکتور حرارتی انجام دهد.
