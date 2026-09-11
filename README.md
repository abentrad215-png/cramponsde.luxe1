# ⚽ BootCraft — Premium Football Boots E-Commerce Platform

A complete, professional, fully-dynamic e-commerce platform for selling football boots with:

- 🛒 **Customer website** — Home, Shop (with filters/search), Product details, Cart, Checkout, Order confirmation, Contact, About
- 📦 **Order system** — Unique order IDs, status tracking (Pending → Confirmed → Preparing → Shipped → Delivered / Cancelled)
- 🤖 **Telegram Bot notifications** — Instant order alerts with inline Confirm/Cancel buttons
- 🛠️ **Admin Dashboard** — Full CRUD for products, orders, categories; statistics; image uploads
- 🔒 **Secure** — Hashed passwords (bcrypt), JWT auth, protected API routes, environment variables

---

## 🔧 Tech Stack

| Layer         | Technology                        |
|---------------|-----------------------------------|
| Frontend      | HTML5, CSS3, Vanilla JavaScript   |
| Backend       | Node.js + Express 4               |
| Database      | MongoDB (via Mongoose)            |
| Authentication| JWT + bcryptjs                    |
| Images        | Multer (filesystem uploads)       |
| Notifications | Telegram Bot API                  |

---

## 📁 Project Structure

```
football-boots-store/
├── package.json
├── .env.example                # Copy to .env and fill in your values
├── .env                        # Your real secrets (never commit this!)
├── server/
│   ├── index.js                # Server entry point
│   ├── seed.js                 # Runs database seeding
│   ├── config/
│   │   └── db.js               # MongoDB connection
│   ├── models/
│   │   ├── Product.js          # Product schema (sizes, stock, images, prices...)
│   │   ├── Order.js            # Order schema + statuses
│   │   ├── Category.js         # Category schema
│   │   └── Admin.js            # Admin schema (bcrypt hashing)
│   ├── routes/
│   │   ├── products.js         # Product CRUD + public listing/filtering
│   │   ├── orders.js           # Order creation, status updates, dashboard stats
│   │   ├── categories.js       # Category CRUD
│   │   └── auth.js             # Admin login / password change
│   ├── middleware/
│   │   ├── auth.js             # JWT protection
│   │   ├── upload.js           # Multer image upload
│   │   └── errorHandler.js     # Central error handling
│   └── utils/
│       ├── telegram.js         # Telegram bot integration
│       └── helpers.js          # Order ID generator
├── public/                     # Customer-facing site (served at root)
│   ├── index.html              # Home
│   ├── shop.html               # Shop with filters/search
│   ├── product.html            # Product details
│   ├── cart.html               # Shopping cart
│   ├── checkout.html           # Checkout
│   ├── order-confirmation.html # Order confirmation
│   ├── contact.html            # Contact
│   ├── about.html              # About
│   ├── css/styles.css          # Shared styles (premium dark theme)
│   ├── js/app.js               # Shared frontend logic
│   └── images/                 # Uploads + placeholders
├── admin/                      # Admin Dashboard (served at /admin)
│   ├── index.html
│   ├── css/admin.css
│   └── js/admin.js
```

---

## 🚀 Getting Started

### Prerequisites

- **Node.js** v16+ — https://nodejs.org
- **MongoDB** — local install https://www.mongodb.com/try/download/community  **or** free cloud [MongoDB Atlas](https://www.mongodb.com/atlas)
- A **Telegram bot token** (optional but recommended)

### 1. Install dependencies

```bash
cd football-boots-store
npm install
```

### 2. Configure environment variables

Copy the example file and edit it:

```bash
cp .env.example .env
```

```env
PORT=5000
MONGODB_URI=mongodb://localhost:27017/football-boots-store
JWT_SECRET=change-this-to-a-long-random-secret
ADMIN_EMAIL=admin@bootsstore.com
ADMIN_PASSWORD=Admin@123456
TELEGRAM_BOT_TOKEN=123456789:ABCdefGHIJK...   # from BotFather
TELEGRAM_CHAT_ID=123456789                    # your user/chat ID
BASE_URL=http://localhost:5000
```

> **Windows:** `copy .env.example .env`

### 3. Start MongoDB

**Option A — Local install:** Install MongoDB Community Server, then the service runs automatically (default port 27017).

**Option B — MongoDB Atlas (free):**
1. Create a free cluster at mongodb.com
2. Create a database user + allow your IP
3. Get your connection string, e.g. `mongodb+srv://<user>:<password>@cluster0.xxxxx.mongodb.net/football-boots-store`
4. Put that string in `.env` → `MONGODB_URI`

### 4. Seed the database (creates admin + sample products)

```bash
npm run seed
```

This creates:
- **Admin account** using your `.env` credentials (default: `admin@bootsstore.com` / `Admin@123456`)
- 5 categories, 8 sample products with tags/sizes/stock

### 5. Run the server

```bash
npm start          # production
npm run dev        # with auto-restart (nodemon)
```

Open in your browser:

| Page                    | URL                              |
|-------------------------|----------------------------------|
| Store (public site)     | http://localhost:5000            |
| Admin Dashboard         | http://localhost:5000/admin      |
| Shop                    | http://localhost:5000/shop       |

---

## 🤖 Telegram Bot Setup

1. Open Telegram and message **[@BotFather](https://t.me/BotFather)**.
2. Send `/newbot`, choose a name and username → you receive a **bot token**.
3. Get your **chat ID**:
   - Message your new bot once (e.g. "hi").
   - Visit `https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates`
   - Copy the number in `"chat":{"id":...}` → that's `TELEGRAM_CHAT_ID`.
4. Put both values in `.env`:
   ```
   TELEGRAM_BOT_TOKEN=123456789:ABC...
   TELEGRAM_CHAT_ID=987654321
   ```
5. Restart the server.

**What happens now:** Every new order posts a formatted notification with inline buttons:

```
🛒 NEW ORDER
Order ID: #FBXXX

👤 Customer:
Name: ...
Phone: ...
Email: ...
Address: ...
City: ...

📦 Products:
1. Nike Mercurial Superfly 9
   Size: 42 | Qty: 1 | 199.99 DT

💰 Total: 214.99 DT
📅 Date: ...

[✅ Confirm] [❌ Cancel]
[📦 Preparing] [🚚 Shipped]
[✅ Delivered]
```

Clicking **Confirm / Cancel / etc.** updates the order status in the database while updating the message, all through the backend. The bot token is **never** exposed to the browser.

> **Note:** Button callbacks work through the `/api/orders/telegram/webhook` endpoint. For production, point your bot's update webhook there, or replace it with a long-polling loop if you prefer.

---

## 🔐 Security

- Passwords hashed with **bcrypt** (12 salt rounds)
- Session via **JWT** (7-day expiry, stored in `Authorization: Bearer` header)
- All admin routes protected by middleware
- Telegram token / DB credentials / JWT secret in `.env` — **never** in frontend code
- Server-side validation of order data (stock checks, required customer fields)
- File-upload type + size limits

---

## 🗄️ Data Model

**Product** — name, slug, description, price, oldPrice (discount), brand, category (ref), images[], sizes[] ({size, stock}), featured, enabled, tags[]

**Order** — orderId (unique), customer{fullName, phone, email, address, city}, items[] ({product, name, image, size, quantity, price}), subtotal, shippingCost, total, status, notes

**Category** — name, slug, description, image, productCount

**Admin** — email, password (hashed), name, role, lastLogin

---

## 📸 Image Management

The Admin uploads product images through the Dashboard. Files are stored in `public/images/uploads/` and served automatically. Replace images there and they update instantly on the public site — no HTML editing.

---

## 🌍 Deployment Guide

### Deploy on Render / Railway / Heroku

1. Push this project to a Git repository (e.g. GitHub).
2. Create an account at **Render** (render.com) → **New → Web Service**.
3. Connect your repo.
4. Build command: `npm install`
5. Start command: `node server/index.js`
6. Add environment variables (same as your `.env`).
7. For MongoDB, use **MongoDB Atlas** and paste the URI.
8. Uploads go to local disk — use a mounted disk volume on Render, or switch to a cloud storage service for the images.

### Deploy on a VPS (e.g. Ubuntu)

```bash
# Install Node.js + MongoDB
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs mongodb-org

# Clone project, install, configure
git clone <your-repo> football-boots-store
cd football-boots-store
npm install
cp .env.example .env   # fill in real values
npm run seed
npm start
```

Run with a process manager:

```bash
sudo npm install -g pm2
pm2 start server/index.js --name boots-store
pm2 save
pm2 startup
```

### Use a reverse proxy (nginx)

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://localhost:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    client_max_body_size 10M;   # allow image uploads
}
```

---

## 🛍️ How Everything Flows Together

```
Customer places order
    ↓
Backend validates + saves order to MongoDB
    ↓  (stock is decremented)
Telegram bot sends formatted notification with buttons
    ↓
Order appears in Admin Dashboard (Pending)
    ↓
Admin (or Telegram button) changes status
    ↓
Status updates in DB + Telegram message edited
    ↓
Website product stock updates automatically
```

---

## 💻 Default Admin Credentials

After running `npm run seed`:

- **Email:** `admin@bootsstore.com`
- **Password:** `Admin@123456`

**Change these immediately** by editing `.env` (before seeding) and/or via Admin → Settings → Change Password.

---

## ❓ Troubleshooting

| Problem | Solution |
|---------|----------|
| `MongoNetworkError` / can't connect | Make sure MongoDB is running (`mongod`) or the Atlas URI is correct. |
| Admin shows login loop | Re-run `npm run seed` or check `.env` credentials match the seeded admin. |
| No Telegram messages | Verify `TELEGRAM_BOT_TOKEN` + `TELEGRAM_CHAT_ID` are set correctly and the server restarted. |
| Images not uploading | Check `public/images/uploads/` exists & is writable. |
| Port already in use | Change `PORT` in `.env`. |