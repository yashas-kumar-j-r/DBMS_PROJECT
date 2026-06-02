# ShopMVP — E-Commerce DBMS Mini Project

A complete full-stack e-commerce application built as a college DBMS mini project.  
Demonstrates CRUD operations, relational databases, JWT authentication, and REST APIs.

---

## 📦 Tech Stack

| Layer    | Technology                         |
|----------|------------------------------------|
| Frontend | React 18 (Vite), Axios, React Router DOM |
| Backend  | Node.js, Express.js                |
| Database | MySQL (XAMPP / MySQL Community Server) |
| Auth     | JWT (jsonwebtoken) + bcrypt        |

---

## 🗂 Project Structure

```
ecommerce-mvp/
├── frontend/          ← React + Vite app (port 5173)
│   └── src/
│       ├── pages/     ← Home, Login, Register, Cart, Orders, Admin
│       ├── components/← Navbar, ProductCard, Footer
│       └── services/  ← Axios API instance
│
├── backend/           ← Express REST API (port 5000)
│   ├── config/        ← MySQL connection
│   ├── controllers/   ← Business logic
│   ├── middleware/     ← JWT auth middleware
│   └── routes/        ← API route definitions
│
└── database/
    ├── schema.sql      ← Table definitions
    └── sample_data.sql ← Seed data (10 products, 3 users)
```

---

## ✨ Features

### User Features
- ✅ Register & Login (JWT authentication)
- ✅ Browse all products
- ✅ Search products by name / category / description
- ✅ Add products to cart
- ✅ Remove cart items
- ✅ Place order (clears cart, decrements stock)
- ✅ View order history with status

### Admin Features
- ✅ Add new products
- ✅ Delete products
- ✅ View all orders from all users

---

## 🚀 Installation & Setup

### Prerequisites
- Node.js v18+
- MySQL (via XAMPP or MySQL Community Server)
- npm

---

### Step 1 — Database Setup

1. Start MySQL (start Apache + MySQL in XAMPP Control Panel).

2. Open **phpMyAdmin** (http://localhost/phpmyadmin) or MySQL Workbench.

3. Import the schema:
   ```
   File → Import → choose database/schema.sql → Go
   ```

4. Import sample data:
   ```
   File → Import → choose database/sample_data.sql → Go
   ```

   Or via terminal:
   ```bash
   mysql -u root -p < database/schema.sql
   mysql -u root -p < database/sample_data.sql
   ```

---

### Step 2 — Backend Setup

```bash
cd backend
npm install
```

Edit `.env` if your MySQL password is not empty:

```
DB_PASSWORD=your_mysql_password
```

Start the backend:

```bash
npm run dev        # development (nodemon auto-restart)
# OR
npm start          # production
```

Backend will run at → **http://localhost:5000**

---

### Step 3 — Frontend Setup

```bash
cd frontend
npm install
npm run dev
```

Frontend will run at → **http://localhost:5173**

---

## 🔑 Demo Credentials

| Role  | Email                | Password  |
|-------|----------------------|-----------|
| Admin | admin@store.com      | password  |
| User  | alice@example.com    | password  |
| User  | bob@example.com      | password  |

> **Note:** The sample_data.sql contains bcrypt hashes of the literal string `"password"`.  
> If these don't work, register a new account and use those credentials.

---

## 🔌 API Endpoints

### Authentication
| Method | Endpoint            | Description        | Auth     |
|--------|---------------------|--------------------|----------|
| POST   | `/api/auth/register`| Register new user  | None     |
| POST   | `/api/auth/login`   | Login, get JWT     | None     |

### Products
| Method | Endpoint              | Description              | Auth        |
|--------|-----------------------|--------------------------|-------------|
| GET    | `/api/products`       | Get all products         | None        |
| GET    | `/api/products?search=q` | Search products       | None        |
| POST   | `/api/products`       | Add product              | Admin only  |
| DELETE | `/api/products/:id`   | Delete product           | Admin only  |

### Cart
| Method | Endpoint                | Description         | Auth     |
|--------|-------------------------|---------------------|----------|
| POST   | `/api/cart/add`         | Add item to cart    | Required |
| GET    | `/api/cart/:userId`     | Get user's cart     | Required |
| DELETE | `/api/cart/remove/:id`  | Remove cart item    | Required |

### Orders
| Method | Endpoint      | Description                              | Auth     |
|--------|---------------|------------------------------------------|----------|
| POST   | `/api/orders` | Place order from cart                    | Required |
| GET    | `/api/orders` | Get orders (own for user, all for admin) | Required |

---

## 🗃 Database Schema

### `users`
| Column     | Type         | Description              |
|------------|--------------|--------------------------|
| id         | INT PK AI    | User ID                  |
| name       | VARCHAR(100) | Full name                |
| email      | VARCHAR(150) | Unique email             |
| password   | VARCHAR(255) | Bcrypt hash              |
| role       | ENUM         | 'user' or 'admin'        |
| created_at | TIMESTAMP    | Registration time        |

### `products`
| Column      | Type          | Description         |
|-------------|---------------|---------------------|
| id          | INT PK AI     | Product ID          |
| name        | VARCHAR(200)  | Product name        |
| description | TEXT          | Description         |
| price       | DECIMAL(10,2) | Price in ₹          |
| stock       | INT           | Available units     |
| image_url   | VARCHAR(500)  | Product image URL   |
| category    | VARCHAR(100)  | Category label      |
| created_at  | TIMESTAMP     | Date added          |

### `cart`
| Column     | Type      | Description          |
|------------|-----------|----------------------|
| id         | INT PK AI | Cart item ID         |
| user_id    | INT FK    | References users(id) |
| product_id | INT FK    | References products  |
| quantity   | INT       | Item quantity        |
| added_at   | TIMESTAMP | When added           |

### `orders`
| Column       | Type          | Description          |
|--------------|---------------|----------------------|
| id           | INT PK AI     | Order ID             |
| user_id      | INT FK        | References users(id) |
| total_amount | DECIMAL(10,2) | Order total          |
| status       | ENUM          | confirmed/shipped/…  |
| created_at   | TIMESTAMP     | Order date           |

### `order_items`
| Column     | Type          | Description           |
|------------|---------------|-----------------------|
| id         | INT PK AI     | Row ID                |
| order_id   | INT FK        | References orders     |
| product_id | INT FK        | References products   |
| quantity   | INT           | Units ordered         |
| unit_price | DECIMAL(10,2) | Price at time of order|

---

## 🎓 DBMS Concepts Demonstrated

- **DDL**: CREATE TABLE, PRIMARY KEY, FOREIGN KEY, AUTO_INCREMENT
- **DML**: INSERT, SELECT, UPDATE, DELETE
- **Joins**: INNER JOIN across cart→products, orders→users, order_items→products
- **Transactions**: ACID-compliant order placement with ROLLBACK on failure
- **Normalization**: 3NF — no redundant data, proper FK relationships
- **Indexes**: Primary keys used for fast lookups

---

## 📝 Notes

- JWT tokens are stored in `localStorage`
- Token expiry is set to **7 days** by default
- Passwords are hashed with **bcrypt (10 salt rounds)**
- CORS is configured to allow only `http://localhost:5173`
- All currency is in Indian Rupees (₹)
