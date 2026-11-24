# 🛒 Sweet Crumbs — Ecommerce Store

A full-stack ecommerce web application (bakery store) built with **Flask + SQLite**, featuring a staff/admin portal, a customer-facing shop, a shopping cart, and **Khalti** online payment integration.

> Ecommerce Project

---

## ✨ Features

### Staff / Admin Portal
- 🔐 **Authentication** — staff login/logout with hashed passwords
- 👥 **Role-based access control** — `admin`, `manager`, `staff` with per-module permissions
- 📊 **Dashboard** — live stats (products, customers, orders, revenue, pending orders, low stock) and recent orders
- 🍞 **Products** — CRUD with image upload (drag & drop + preview), search and category filter
- 👤 **Customers** — CRUD with search, email/phone validation
- 🧾 **Orders** — list & filter by status, detail view, status updates, delete (restores stock)
- 🛒 **Cart & Checkout** — multi-item cart with stock-aware quantities and Khalti payment
- 👨‍💼 **Staff management** — admin-only user CRUD with role assignment and activation
- 🔑 **Profile** — change own password

### Customer Portal
- 📝 **Registration / Login** — customer accounts with hashed passwords
- 🛍️ **Shop** — browse products by category
- 🛒 **Cart** — add, update, remove items; stock limits enforced
- 💳 **Checkout** — pay online via Khalti and view order history & details

### Payments
- 💰 **Khalti ePayment** integration (initiate + callback verification)
- 📦 **Stock reservation** on checkout; automatic stock restore on cancellation/failure
- 🔁 **Idempotent** payment callbacks and manual payment verification

---

## 🧱 Tech Stack

| Layer | Technology |
|-------|------------|
| Backend | Python, Flask |
| Database | SQLite (auto-created on first run) |
| Auth | Werkzeug password hashing, Flask sessions |
| Payments | Khalti ePayment API |
| Frontend | Jinja2 templates, HTML/CSS, vanilla JS |
| Config | python-dotenv (`.env`) |
| HTTP client | requests |

---

## 📂 Project Structure

```
ecommerce/
├── app.py                     ← Flask backend (all routes & DB logic)
├── requirements.txt
├── .env                       ← Environment configuration (not committed)
├── bakery.db                  ← SQLite database (auto-created, gitignored)
├── static/
│   ├── css/style.css          ← Warm artisan theme
│   ├── js/main.js             ← Image drag-drop & preview
│   └── uploads/               ← Product images (gitignored)
└── templates/
    ├── base.html              ← Layout with sidebar navigation
    ├── index.html             ← Dashboard
    ├── login.html             ← Staff login
    ├── profile.html           ← Change password
    ├── products.html          ← Product list + search/filter
    ├── product_form.html      ← Add / Edit product (image upload)
    ├── customers.html         ← Customer list + search
    ├── customer_form.html     ← Add / Edit customer
    ├── orders.html            ← Orders list + status filter
    ├── order_detail.html      ← Order detail + status update
    ├── order_form.html        ← (legacy) new order form
    ├── cart.html              ← Cart + checkout
    ├── users.html             ← Staff list
    ├── user_form.html         ← Add / Edit staff
    ├── customer_login.html    ← Customer login
    ├── customer_register.html ← Customer registration
    ├── customer_shop.html     ← Customer-facing shop
    └── customer_order_detail.html ← Customer order detail
```

---

## 🗄️ Database Schema

| Table | Description |
|-------|-------------|
| `staff` | Staff accounts (name, username, password hash, role, active, created_at) |
| `category` | Product categories (Bread, Pastry, Cake, Cookie, Drink) |
| `product` | Bakery items (name, description, price, stock_qty, category_id, image_path) |
| `customer` | Customer records (+ auth fields: password_hash, active) |
| `order` | Order header (customer_id, total_price, status, order_date, payment_status, payment_method, payment_pidx, payment_txn_id, stock_restored) |
| `order_item` | Line items linking order ↔ product (quantity, unit_price) |

---

## 🔐 Roles & Permissions

| Role | Modules |
|------|---------|
| `admin` | dashboard, products, customers, orders, users |
| `manager` | dashboard, products, customers, orders |
| `staff` | dashboard, orders |

---

## ⚙️ Setup & Run

### 1. Clone the repository
```bash
git clone https://github.com/Samrajya871/ecommerce.git
cd ecommerce
```

### 2. Create a virtual environment (recommended)
```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# Linux / macOS
source .venv/bin/activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Configure environment
Create a `.env` file in the project root (see [Environment Variables](#-environment-variables)).

### 5. Run the app
```bash
python app.py
```

### 6. Open in browser
```
http://127.0.0.1:5000
```

The SQLite database and default seed data (categories + staff accounts) are created automatically on first run.

---

## 🔑 Environment Variables

Create a `.env` file with the following:

```env
# Flask
SECRET_KEY=change-me-in-production
DB_PATH=bakery.db
FLASK_ENV=development
FLASK_DEBUG=1

# Khalti payment gateway
KHALTI_BASE_URL=https://a.khalti.com/api/v2
KHALTI_SECRET_KEY=your-khalti-secret-key
KHALTI_WEBSITE_URL=http://127.0.0.1:5000
```

> ⚠️ Never commit `.env` or real secret keys. `.env` is gitignored.

---

## 👤 Default Accounts

Seeded automatically when the database is empty:

| Username | Password | Role |
|----------|----------|------|
| `admin` | `admin123` | admin |
| `manager` | `manager123` | manager |
| `staff` | `staff123` | staff |

> Change these credentials in production.

---

## 🌐 Key Routes

### Staff
| Path | Description |
|------|-------------|
| `/login`, `/logout` | Staff authentication |
| `/` | Dashboard |
| `/products`, `/products/add`, `/products/edit/<id>`, `/products/delete/<id>` | Product CRUD |
| `/customers`, `/customers/add`, `/customers/edit/<id>`, `/customers/delete/<id>` | Customer CRUD |
| `/orders`, `/orders/<id>`, `/orders/<id>/status`, `/orders/delete/<id>` | Orders |
| `/cart`, `/cart/add/<id>`, `/cart/update`, `/cart/remove/<id>`, `/cart/clear`, `/cart/checkout` | Cart & checkout |
| `/users`, `/users/add`, `/users/edit/<id>`, `/users/delete/<id>` | Staff management (admin) |
| `/profile` | Change password |
| `/khalti/callback` | Khalti payment callback |
| `/orders/<id>/verify-payment` | Manual payment verification |
| `/api/product/<id>` | Product JSON API |

### Customer
| Path | Description |
|------|-------------|
| `/customer/login`, `/customer/logout` | Customer authentication |
| `/customer/register` | Customer registration |
| `/customer`, `/customer/dashboard` | Customer shop |
| `/customer/orders/<id>` | Customer order detail |

---

## ✅ Input Validation

- Username: 3–50 chars, alphanumeric (with `_`/`-`)
- Password: minimum 6 characters
- Email: standard email format
- Phone: optional, 10 digits, must start with `97` or `98` (Nepal mobile)
- Product price/stock: non-negative numbers
- Image uploads: PNG, JPG, JPEG, GIF, WEBP (max 5 MB)

---

## 💳 Khalti Payment Flow

1. Customer/staff fills cart and checks out (minimum NPR 10.00).
2. An order is created (`pending`, `payment_status=initiated`) and stock is reserved.
3. User is redirected to Khalti's payment page.
4. On return, `/khalti/callback` verifies the payment via Khalti's lookup API.
5. On success: order marked `paid`, transaction id stored.
6. On cancel/fail/expiry: stock is restored and order marked `cancelled`.
7. Manual re-verification available via `/orders/<id>/verify-payment`.

---

## 📝 Notes

- Database file (`bakery.db`) and uploaded images (`static/uploads/`) are gitignored.
- Do not commit `.env`.
- For production, set `FLASK_ENV=production`, use a strong `SECRET_KEY`, enable HTTPS (secure cookies are enabled automatically when `FLASK_ENV=production`), and run behind a production WSGI server (e.g. Gunicorn) instead of `app.run(debug=True)`.
- `DEPLOYMENT.md` in this repo is a legacy Node.js template and does **not** apply to this Flask project.

---

## 📜 License

This project is provided as an ecommerce project.
