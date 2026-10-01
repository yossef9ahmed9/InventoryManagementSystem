# 📦 Inventory Management System

A full-featured, multi-tenant **Inventory Management REST API** built with **.NET 10** and **ASP.NET Core**. Each registered user gets a completely isolated data environment — manage your products, suppliers, customers, purchases, and sales all in one place, backed by clean business intelligence from the dashboard and reports.

---

## 🚀 Features at a Glance

- 🏢 **Multi-Tenancy** — Every user sees only their own data, fully isolated via `UserId` scoping on every entity
- 🔐 **Cookie-Based Auth** — Secure HttpOnly sessions with BCrypt password hashing, no JWT overhead
- 📦 **Product & Category Management** — Organize your catalog with categories, stock levels, and pricing
- 🏭 **Supplier & Purchase Tracking** — Record stock-in from suppliers, auto-incrementing product inventory
- 👥 **Customer & Sales Management** — Create sales orders with line items, auto-decrementing stock on sale
- 📊 **Live Dashboard** — 12 real-time business KPIs in a single query (inventory value, sales totals, low stock alerts, and more)
- 📈 **Business Reports** — Top-selling products and sales revenue summaries grouped by day or month
- 🔍 **Search & Filter** — Search by name, filter by category/customer/date range across all major entities
- 📝 **Swagger UI** — Interactive API docs available in development mode

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Runtime | .NET 10 |
| Framework | ASP.NET Core Web API |
| ORM | Entity Framework Core 10 |
| Database | Microsoft SQL Server |
| Authentication | Cookie Authentication (HttpOnly, sliding 24h session) |
| Password Hashing | BCrypt.Net-Next |
| API Documentation | Swashbuckle / Swagger |
| Architecture | Layered (Controllers → Services → Repositories → EF Core) |

---

## 🗄️ Database Schema

```
users            — Id, Name, Email (unique), PasswordHash, CreatedAt
categories       — Id, Name, Description, UserId*
customers        — Id, Name, Email, Phone, Address, UserId*
suppliers        — Id, Name, Email, Phone, Address, UserId*
products         — Id, Name, Description, Price, Stock, CategoryId, UserId*
sales            — Id, CustomerId, Date, TotalPrice, Status, UserId*
purchase_products — Id, ProductId, SupplierId, Quantity, DateIn, Notes, UserId*
sale_products    — Id, ProductId, SaleId, Quantity, DateOut, Notes, UserId*
```

> `UserId*` — present on every table to enforce per-user data isolation (multi-tenancy)

---

## 📁 Project Structure

```
📦 Inventory-Management-system-Project-main
├── 📄 database.sql                         # Full DB creation script
├── 📄 InventoryManagementSystem.slnx       # Solution file
└── 📂 src/
    └── 📂 InventoryManagementSystem.Api/
        ├── 📄 Program.cs                   # DI registration & middleware pipeline
        ├── 📂 Controllers/                 # 10 controllers
        ├── 📂 Data/                        # EF Core DbContext
        ├── 📂 DTOs/
        │   ├── 📂 Auth/                    # Login, Register, AuthResponse
        │   ├── 📂 Requests/                # 14 Create/Update/Patch DTOs
        │   └── 📂 Responses/              # 10 response DTOs
        ├── 📂 Migrations/                  # EF Core migrations
        ├── 📂 Models/                      # 8 entity classes
        ├── 📂 Repositories/               # Generic + 7 specialized repos
        └── 📂 Services/                    # 11 business logic services
```

---

## 🔌 API Endpoints

### 🔐 Auth — `/api/auth`

| Method | Route | Description |
|---|---|---|
| `POST` | `/register` | Register a new account |
| `POST` | `/login` | Login and receive a session cookie |
| `POST` | `/logout` | Sign out and clear the cookie |
| `GET` | `/me` | Get current user info |

---

### 📂 Categories — `/api/categories`

| Method | Route | Description |
|---|---|---|
| `GET` | `/` | List all categories |
| `GET` | `/{id}` | Get category by ID |
| `POST` | `/` | Create a category |
| `PUT` | `/{id}` | Update a category |
| `DELETE` | `/{id}` | Delete a category |

---

### 📦 Products — `/api/products`

| Method | Route | Description |
|---|---|---|
| `GET` | `/` | List all products |
| `GET` | `/{id}` | Get product by ID |
| `POST` | `/` | Create a product |
| `PUT` | `/{id}` | Update a product |
| `DELETE` | `/{id}` | Delete a product |
| `GET` | `/byCategory/{categoryId}` | Filter products by category |
| `GET` | `/search?name=` | Search products by name |

---

### 🏭 Suppliers — `/api/suppliers`

| Method | Route | Description |
|---|---|---|
| `GET` | `/` | List all suppliers |
| `GET` | `/{id}` | Get supplier by ID |
| `POST` | `/` | Create a supplier |
| `PUT` | `/{id}` | Update a supplier |
| `DELETE` | `/{id}` | Delete a supplier |
| `GET` | `/search?name=` | Search suppliers by name |

---

### 👥 Customers — `/api/customers`

| Method | Route | Description |
|---|---|---|
| `GET` | `/` | List all customers |
| `GET` | `/{id}` | Get customer by ID |
| `POST` | `/` | Create a customer |
| `PUT` | `/{id}` | Update a customer |
| `DELETE` | `/{id}` | Delete a customer |
| `GET` | `/search?name=` | Search customers by name |

---

### 🛒 Sales — `/api/sales`

| Method | Route | Description |
|---|---|---|
| `GET` | `/` | List all sales |
| `GET` | `/{id}` | Get sale with line items |
| `POST` | `/` | Create a sale (auto-decrements stock) |
| `PATCH` | `/{id}` | Update sale header (customer, date, status) |
| `DELETE` | `/{id}` | Delete a sale |
| `GET` | `/byCustomer/{customerId}` | Filter sales by customer |
| `GET` | `/byDateRange?from=&to=` | Filter sales by date range |

---

### 🧾 Sale Products (Line Items) — `/api/saleProducts`

| Method | Route | Description |
|---|---|---|
| `GET` | `/` | List all sale line items |
| `GET` | `/{id}` | Get line item by ID |
| `POST` | `/` | Add a line item to a sale |
| `PUT` | `/{id}` | Update a line item |
| `DELETE` | `/{id}` | Remove a line item |
| `GET` | `/bySale/{saleId}` | Get all line items for a sale |

---

### 📥 Purchase Products — `/api/purchaseProducts`

| Method | Route | Description |
|---|---|---|
| `GET` | `/` | List all purchases |
| `GET` | `/{id}` | Get purchase by ID |
| `POST` | `/` | Record a purchase (auto-increments stock) |
| `PUT` | `/{id}` | Update a purchase |
| `DELETE` | `/{id}` | Delete a purchase |
| `GET` | `/byProduct/{productId}` | Filter purchases by product |
| `GET` | `/byDateRange?from=&to=` | Filter purchases by date range |

---

### 📊 Dashboard — `/api/dashboard`

| Method | Route | Description |
|---|---|---|
| `GET` | `/stats?lowStockThreshold=10` | Get all business KPIs in one call |

**Response includes:**

| Metric | Description |
|---|---|
| `totalProducts` | Total number of products |
| `totalStockUnits` | Total units across all products |
| `inventoryValue` | Total value of current stock (price × quantity) |
| `salesValue` | Cumulative sales revenue |
| `salesCount` | Total number of sales |
| `lowStockCount` | Products below the threshold |
| `outOfStockCount` | Products with zero stock |
| `categoriesCount` | Total categories |
| `customersCount` | Total customers |
| `suppliersCount` | Total suppliers |
| `purchasesCount` | Total purchase records |
| `lowStockThreshold` | The threshold used in this query |

---

### 📈 Reports — `/api/reports`

| Method | Route | Description |
|---|---|---|
| `GET` | `/topProducts?from=&to=&limit=5` | Top products ranked by units sold |
| `GET` | `/salesSummary?from=&to=&groupBy=day\|month` | Sales revenue grouped by day or month |

---

## ⚙️ Getting Started

### Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- Microsoft SQL Server (local or remote)

### 1. Clone the repository

```bash
git clone https://github.com/your-username/Inventory-Management-system-Project.git
cd Inventory-Management-system-Project
```

### 2. Configure the database connection

Edit `src/InventoryManagementSystem.Api/appsettings.json`:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=InventoryManagementSystem;Trusted_Connection=True;TrustServerCertificate=True"
  }
}
```

> Replace `Server=.` with your SQL Server instance name if needed.

### 3. Set up the database

**Option A — Run the SQL script directly:**

```bash
sqlcmd -S . -i database.sql
```

**Option B — Apply EF Core migrations:**

```bash
cd src/InventoryManagementSystem.Api
dotnet ef database update
```

### 4. Configure CORS (optional)

By default the API allows requests from `http://localhost:5173` (Vite dev server). To change this, add to `appsettings.json`:

```json
{
  "Cors": {
    "AllowedOrigins": ["http://localhost:3000", "https://your-frontend.com"]
  }
}
```

### 5. Run the API

```bash
cd src/InventoryManagementSystem.Api
dotnet run
```

The API will start at `https://localhost:7xxx` / `http://localhost:5xxx`.  
Swagger UI will be available at `http://localhost:{port}/swagger` in development.

---

## 🔐 Authentication Flow

The API uses **cookie-based sessions** — no tokens to manage on the client side.

```
POST /api/auth/register   →   Creates account + signs you in
POST /api/auth/login      →   Returns HttpOnly session cookie
GET  /api/...             →   Include cookie automatically (credentials: 'include')
POST /api/auth/logout     →   Clears the session cookie
```

All requests to protected endpoints must include the session cookie. In a browser-based frontend, set `credentials: 'include'` (fetch) or `withCredentials: true` (axios).

Session cookies are:
- **HttpOnly** — not accessible via JavaScript
- **SameSite: Lax** — CSRF protection
- **24-hour sliding expiration** — refreshed on each request

---

## 🏗️ Architecture

The project follows a clean, layered architecture:

```
HTTP Request
    ↓
Controller          — Validates input, delegates to service, maps response
    ↓
Service             — Business logic, orchestrates repositories, applies multi-tenancy
    ↓
Repository          — Data access via EF Core (generic + specialized)
    ↓
AppDbContext        — EF Core, SQL Server
```

Dashboard and report services query the `DbContext` directly to push aggregations down to SQL rather than loading collections into memory.

---

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is open source. See the repository for license details.
