# VELOUR

### Full-Stack AI-Powered Fashion E-Commerce Platform

> A production-inspired fashion e-commerce platform built with the MERN stack, designed with secure authentication, optimized product discovery, AI-powered size recommendations, global cart management, cloud image storage, and an administrative management system.

**Project Status:** 🚧 In Development
**Project Type:** Major Project

VELOUR is being developed as a full-stack fashion e-commerce platform with a focus on practical engineering, security, scalability, reusable frontend architecture, and AI integration. The project is designed around a React frontend, Express/Node.js backend, MongoDB database, Redis-based token management, Cloudinary image storage, and Gemini-powered size recommendations. 

---

## Features

* 🔐 Secure JWT-based authentication
* 🍪 HTTP-only cookie-based sessions
* 🔑 bcrypt password hashing
* 🚪 Redis token blacklist for immediate logout
* 🛡️ Protected API routes with authentication middleware
* ✅ React Hook Form + Zod validation
* 🏠 Reusable category-based frontend architecture
* 👕 Men, Women, and Kids categories
* 🔎 Debounced product search
* 🎯 Backend-driven product filtering
* 💰 Price and sorting filters
* 🛍️ Product details and multiple product images
* 📦 Stock management
* 🛒 Redux Toolkit-powered cart
* 💸 Automatic discount calculation
* 📏 AI-powered size recommendation
* 🤖 Gemini AI integration
* 📋 Checkout and order management
* 👤 User profile and order history
* 👨‍💼 Admin dashboard
* 🖼️ Cloudinary image storage and delivery
* ☁️ Cloud deployment architecture

---

## Technology Stack

| Layer                      | Technologies                   |
| -------------------------- | ------------------------------ |
| Frontend                   | React.js, JavaScript           |
| State Management           | Redux Toolkit                  |
| Forms & Validation         | React Hook Form, Zod           |
| UI / Interaction           | Swiper.js, HTML5, CSS3         |
| Backend                    | Node.js, Express.js            |
| Authentication             | JWT, HTTP-only Cookies, bcrypt |
| Database                   | MongoDB, MongoDB Atlas         |
| Caching / Token Management | Redis                          |
| Image Storage              | Cloudinary                     |
| AI                         | Google Gemini                  |
| Frontend Deployment        | Vercel                         |
| Backend Deployment         | Render                         |

---

## System Architecture

```text
                         ┌─────────────────────┐
                         │      VELOUR UI      │
                         │      React.js       │
                         └──────────┬──────────┘
                                    │
                                    │ HTTPS / REST API
                                    ▼
                         ┌─────────────────────┐
                         │    Express.js       │
                         │      Backend        │
                         └───────┬─────┬───────┘
                                 │     │
                    ┌────────────┘     └────────────┐
                    ▼                               ▼
           ┌────────────────┐              ┌────────────────┐
           │    MongoDB     │              │     Redis      │
           │  Application   │              │ Token Blacklist│
           │     Data       │              │                │
           └────────────────┘              └────────────────┘
                    │
                    ▼
           ┌────────────────┐
           │   Cloudinary   │
           │ Product Images │
           └────────────────┘

                         ┌─────────────────────┐
                         │     Gemini AI       │
                         │ Size Recommendation │
                         └─────────────────────┘
```

---

## Authentication

VELOUR uses JWT-based authentication with HTTP-only cookies.

The planned login flow is:

```text
User
 │
 │ Email + Password
 ▼
Express Backend
 │
 ▼
MongoDB
 │
 │ Find User
 ▼
bcrypt
 │
 │ Compare Password
 ▼
JWT Generated
 │
 ▼
HTTP-only Cookie
 │
 ▼
Authenticated Session
```

Passwords are stored as one-way bcrypt hashes. After successful authentication, the JWT is sent through an HTTP-only cookie rather than being stored in `localStorage`. This is intended to prevent JavaScript from directly accessing the authentication token. 

### Protected Routes

Protected requests pass through authentication middleware.

```text
Client Request
      │
      ▼
HTTP-only Cookie
      │
      ▼
Authentication Middleware
      │
      ├───────────────┐
      │               │
    Valid           Invalid
      │               │
      ▼               ▼
Controller        403 Access Denied
```

The middleware verifies the JWT signature and expiry before allowing the request to reach the controller. 

---

## Redis Token Blacklist

VELOUR uses Redis to provide immediate JWT invalidation during logout.

Deleting a cookie alone does not invalidate a JWT that has not yet expired. The planned Redis blacklist closes this gap.

```text
User Logs Out
      │
      ▼
Logout Request
      │
      ▼
JWT → Redis
      │
      ▼
Token Blacklisted
      │
      ▼
Future Protected Request
      │
      ▼
Redis Lookup
      │
   ┌──┴───────┐
   │          │
 Found     Not Found
   │          │
   ▼          ▼
Reject     Continue
```

If a blacklisted token is detected, the request is rejected immediately. 

---

## Frontend Form Validation

VELOUR uses **React Hook Form** and **Zod** for frontend validation.

Instead of sending invalid input to the server first:

```text
User Input
    │
    ▼
Zod Validation
    │
 ┌──┴──────┐
 │         │
Valid    Invalid
 │         │
 ▼         ▼
API      Error
Request
```

Zod is planned to centralize validation rules such as:

* Required fields
* Email format
* Minimum password requirements
* Form-specific constraints

React Hook Form is used to simplify form state management and reduce unnecessary re-renders. 

---

## Frontend Architecture

VELOUR follows a reusable component-based React architecture.

### Category Landing

Men, Women, and Kids use the same general page structure through a reusable `CategoryLanding` component.

```text
                  CategoryLanding
                  /      |      \
                 /       |       \
              Men      Women     Kids
```

Category-specific information such as the category name, hero image, and banner content can be passed into the shared component.

This reduces duplicated UI code and makes future changes easier. 

---

## Home Page

The planned home page contains:

```text
Hero Banner
     │
     ▼
Top Picks
     │
     ▼
New Arrivals
     │
     ▼
Best Sellers
     │
     ▼
Product Scrolling
```

The presentation defines:

* **Top Picks** — rating based
* **New Arrivals** — date based
* **Best Sellers** — rating based
* **Swiper.js** — horizontal product scrolling 

---

## Product Search

VELOUR uses debouncing to reduce unnecessary API calls during search.

### Without Debouncing

```text
S      → API
Sh     → API
Shi    → API
Shir   → API
Shirt  → API
```

Five keystrokes can generate five requests.

### With Debouncing

```text
S → Sh → Shi → Shir → Shirt
                    │
                    ▼
             User stops typing
                    │
                    ▼
                 1 API
```

The search request is triggered after the user pauses typing rather than on every keystroke.

This is designed to reduce redundant requests and server load while providing a smoother search experience. 

---

## Product Filtering

Filtering is performed on the backend rather than downloading the complete product catalog to the browser.

```text
User Selects Filters
        │
        ▼
React Frontend
        │
        │ Category / Sub-category / Price / Sort
        ▼
Express Backend
        │
        ▼
MongoDB Query
        │
        ▼
Matching Products
        │
        ▼
React Frontend
```

Only the selected filter values are sent from the frontend. Express constructs the MongoDB query, and MongoDB returns the matching products. 

This approach is intended to be more suitable for large product catalogs than downloading all products and filtering them entirely in the browser. 

---

## Product & Cart

The product experience is planned to support:

* Multiple product images
* Size selection
* Live stock management
* Automatic discount calculation
* Add to cart



### Redux Toolkit

Redux Toolkit manages the global cart state.

```text
Product Page
     │
     ▼
Add to Cart
     │
     ▼
Redux Store
     │
 ┌───┼───────────┐
 ▼   ▼           ▼
Cart Navbar   Checkout   Other Components
```

This provides a centralized state for cart-related UI and allows cart changes to be reflected throughout the application. 

---

## AI-Powered Size Recommendation

A key feature of VELOUR is its AI-powered size recommendation system.

Instead of relying only on traditional size labels such as `S`, `M`, and `L`, the system is designed to use user measurements such as height and weight.

```text
User
 │
 │ Height + Weight
 ▼
VELOUR
 │
 │ Measurements + Product Information
 ▼
Gemini AI
 │
 ▼
AI Analysis
 │
 ▼
Recommended Size
 │
 ▼
Plain-Language Explanation
```

The planned system sends the user's measurements together with product information to Gemini AI. Gemini then analyzes the information and returns a recommended size along with an explanation. 

---

## Orders & Account

VELOUR is designed to provide a complete shopping flow:

```text
Cart
 │
 ▼
Checkout
 │
 ▼
Order Confirmation
 │
 ▼
My Orders
 │
 ▼
Order Status & Purchase History
```

Users will also have a profile section where account details can be edited.

The planned application stores profile and order data in MongoDB. 

---

## Admin Dashboard

VELOUR includes an administrative dashboard.

The planned admin functionality includes:

### Product Management

* Product creation
* Product updates
* Product information management
* Inventory management

### Order Management

* Viewing orders
* Managing orders
* Updating order information

The project presentation specifically identifies product management and order management as the core admin functionality. 

---

## Database & Storage

### MongoDB

MongoDB serves as the primary application database.

Planned data includes:

```text
Users
Products
Orders
Profiles
```

MongoDB Atlas is planned for cloud database hosting.

### Cloudinary

Cloudinary is planned for product image storage and optimized image delivery.

```text
Product Image
      │
      ▼
 Cloudinary
      │
      ▼
Optimized Delivery
      │
      ▼
VELOUR Frontend
```

---

## Deployment Architecture

The planned deployment architecture is:

```text
                    Internet
                       │
                       ▼
              ┌─────────────────┐
              │     Vercel      │
              │ React Frontend  │
              └────────┬────────┘
                       │
                       │ HTTPS
                       ▼
              ┌─────────────────┐
              │     Render      │
              │ Express Backend │
              └──────┬─────┬────┘
                     │     │
            ┌────────┘     └────────┐
            ▼                       ▼
     ┌─────────────┐         ┌─────────────┐
     │ MongoDB     │         │   Redis     │
     │   Atlas     │         │   Blacklist │
     └─────────────┘         └─────────────┘

                     │
                     ▼
               ┌────────────┐
               │ Cloudinary │
               │   Images   │
               └────────────┘
```

The presentation specifies:

* **Vercel** — React frontend
* **Render** — Express backend
* **MongoDB Atlas** — cloud database
* **Cloudinary** — image storage and delivery 

Sensitive information such as database connection strings, JWT secrets, and Cloudinary credentials will be stored through environment variables rather than hardcoded into the source code. 

---

## Environment Variables

Sensitive configuration should be stored in environment variables.

Example:

```env
# Server
PORT=5000

# Database
MONGODB_URI=your_mongodb_connection_string

# Authentication
JWT_SECRET=your_jwt_secret

# Redis
REDIS_URL=your_redis_connection_string

# Cloudinary
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

# Gemini
GEMINI_API_KEY=your_gemini_api_key
```

Never commit real credentials to GitHub.

Add the following to `.gitignore`:

```gitignore
node_modules/
.env
.env.local
dist/
build/
```

---

## Development Roadmap

### Phase 1 — Foundation

* [ ] Initialize React frontend
* [ ] Initialize Node.js / Express backend
* [ ] Configure MongoDB
* [ ] Configure environment variables
* [ ] Establish frontend/backend communication

### Phase 2 — Authentication

* [ ] Registration
* [ ] Login
* [ ] bcrypt password hashing
* [ ] JWT generation
* [ ] HTTP-only cookies
* [ ] Authentication middleware
* [ ] Redis token blacklist
* [ ] Logout

### Phase 3 — Frontend

* [ ] Home page
* [ ] Reusable components
* [ ] Category landing pages
* [ ] Men category
* [ ] Women category
* [ ] Kids category
* [ ] Product cards
* [ ] Product details

### Phase 4 — Search & Filtering

* [ ] Product search
* [ ] Search debouncing
* [ ] Category filtering
* [ ] Sub-category filtering
* [ ] Price filtering
* [ ] Sorting
* [ ] Backend-driven filtering

### Phase 5 — Cart & Orders

* [ ] Redux Toolkit store
* [ ] Add to cart
* [ ] Remove from cart
* [ ] Quantity management
* [ ] Stock management
* [ ] Discount calculation
* [ ] Checkout
* [ ] Order creation
* [ ] Order history
* [ ] User profile

### Phase 6 — AI

* [ ] Gemini integration
* [ ] User measurement input
* [ ] Product information processing
* [ ] Size recommendation
* [ ] Recommendation explanation

### Phase 7 — Admin

* [ ] Admin authentication
* [ ] Product management
* [ ] Inventory management
* [ ] Order management
* [ ] Admin dashboard

### Phase 8 — Deployment

* [ ] MongoDB Atlas
* [ ] Cloudinary
* [ ] Redis
* [ ] Render backend deployment
* [ ] Vercel frontend deployment
* [ ] Production environment variables
* [ ] Production testing

---

## Future Scope

The project presentation identifies the following future enhancements:

* 💳 Secure online payment integration
* ⭐ Product reviews and ratings
* ❤️ Wishlists
* 🎯 Personalized recommendations
* 📧 Email notifications
* 📊 Advanced admin analytics
* 🤖 AI-powered fashion suggestions
* 🌐 Multilingual support



---

## Security

VELOUR is designed with security as an important part of the architecture.

Key security considerations include:

* JWT-based authentication
* HTTP-only cookies
* bcrypt password hashing
* Protected routes
* Authentication middleware
* Redis token blacklist
* Frontend input validation
* Environment-based secret management
* No hardcoded credentials

---

## Project Status

```text
Planning & Requirements     ✅
System Architecture         ✅
UI/UX Planning              ✅
Technical Design             ✅
Development                 🚧
Testing                     ⏳
Deployment                  ⏳
```

**VELOUR is currently in the planning/development stage.**

The features, architecture, deployment model, and future scope described in this README are based on the current project presentation and represent the intended implementation. The presentation itself describes VELOUR as a production-inspired platform and outlines its authentication, frontend, search, filtering, AI, order, admin, and deployment architecture. 

---

## 👨‍💻 Project

**VELOUR**
*Full-Stack AI-Powered Fashion E-Commerce Platform*

**Project Type:** Major Academic Project

---

### 🚧 VELOUR

**Engineered as a full-stack product — not just a shopping website.**
