# API Testing – Postman Collection

A comprehensive REST API test suite built with **Postman** for the [DummyJSON](https://dummyjson.com/) API. This collection covers **functional**, **validation**, **negative**, and **edge-case** testing across Products and Carts modules, using JavaScript test scripts with the Postman `pm.*` API.

---

## 📋 Table of Contents

* [Overview](#overview)
* [Test Summary](#test-summary)
* [Modules & Test Coverage](#modules--test-coverage)
* [Getting Started](#getting-started)
* [Environment Variables](#environment-variables)
* [Running the Collection](#running-the-collection)
* [Folder Structure](#folder-structure)
* [Test Cases](docs/test-cases.md)        

---

## Overview

**API Under Test:** [DummyJSON](https://dummyjson.com/) – a free fake REST API for e-commerce data  
**Tool:** Postman (JavaScript test scripts)  
**Test Types:** Functional, schema validation, business logic, negative/error path testing  
**Auth:** Bearer token – auto-captured from login response and stored as a collection variable

---

## Test Summary

| Module | Valid Path Tests | Invalid Path Tests | HTTP Methods |
|--------|:---:|:---:|---|
| **Products** | ✅ | ✅ | GET, POST, PATCH, DELETE |
| **Carts** | ✅ | ✅ | GET, POST, PATCH, DELETE |

---

## Modules & Test Coverage

### 📦 Products API

#### ✅ Valid Paths

**Get All Products**
- Status code is 200
- Products array exists and is not empty
- Each product has required fields: `id` (number (which is unique)), `title` (string), `price` (number > 0)
- Each product has a valid rating between 0 and 5
- Each product ID is unique

**Get Single Product**
- Status code is 200
- Returned product ID matches the requested ID
- Response has valid product structure (`id`, `title`, `price`, `category`)
- Product has a valid rating (0–5)
- Product stock is non-negative

**Search Products (with pagination)**
- Status code is 200
- Response contains `products`, `limit`, `skip`, `total` fields with correct data types
- Response respects `limit=10` and `skip=4` parameters
- Returned products count does not exceed the limit

**Create Product**
- Status code is 201
- Response contains `id`, `title`, `price` fields with correct data types
- Response data matches the request body (title and price)
- Price is above 0

**Register Token (Auth)**
- Status code is 200
- `accessToken` and `refreshToken` are created and non-empty strings
- User details are valid: `id`, `username`, `email` (contains @), `firstName`, `lastName`
- Response username matches the requested username
- `accessToken` is auto-saved to collection variable

**Get All Products Categories**
- Status code is 200
- Response is a non-empty array
- Each category item contains `slug`, `name`, and `url` (valid https:// string)

**Get All Product Category List**
- Status code is 200
- Response is a non-empty array of strings
- Each item is a non-empty trimmed string
- Category names are unique (no duplicates)

**Update Product (PUT/PATCH)**
- Status code is 200
- Product `id` matches the request URL
- All expected fields present with correct types: `id`, `title`, `price`, `discountPercentage`, `stock`, `rating`, `description`, `brand`, `category`
- Updated field reflects the value sent in the request body

**Delete Product**
- Status code is 200
- `isDeleted` is `true`
- `deletedOn` is not null

---

#### ❌ Invalid Paths

**Invalid Credentials (POST /auth/login)**
- Status code is 400
- Response has a `message` property (non-empty string)
- Error message equals `"Invalid credentials"`

**Without Credentials (POST /auth/login)**
- Status code is 400
- Response has a `message` property (non-empty string)
- Error message equals `"Username and password required"`

**Invalid Product ID (GET /products/195)**
- Status code is 404
- Response has a `message` property (non-empty string)
- Error message equals `"Product with id '195' not found"`
- Error message contains the ID `195`

**Invalid Skip Value (GET /products?skip=abc)**
- Status code is 400
- Response has a `message` property (non-empty string)
- Error message equals `"Invalid 'skip' - must be a number"`

**Invalid Limit Value (GET /products?limit=-)**
- Status code is 400
- Response has a `message` property (non-empty string)
- Error message equals `"Invalid 'limit' - must be a number"`

---

### 🛒 Carts API

#### ✅ Valid Paths

**Get All Carts**
- Status code is 200
- Response contains a `carts` array with at least one item
- Each cart has expected fields with correct data types

**Get Single Cart**
- Status code is 200
- Response contains a `carts` array with at least one item
- Cart `id` matches the requested ID
- Cart has all expected fields: `id`, `products`, `total`, `discountedTotal`, `totalProducts`, `totalQuantity`
- Each product in cart has all required keys: `id`, `title`, `price`, `quantity`, `total`, `discountPercentage`, `discountedTotal`, `thumbnail`
- Product field data types are correct
- **Business logic validations:**
  - Cart `total` equals the sum of all product `total` values (within ±0.01)
  - Product `total` = `price × quantity` (within ±0.01)
  - Cart `discountedTotal` equals the sum of product `discountedTotal` values (within ±0.01)
  - `totalQuantity` equals the sum of all product quantities
  - `discountedTotal` is less than `total`
  - `totalProducts` equals the length of the products array

**Get Cart by User**
- Status code is 200
- All returned carts belong to the requested user (`userId` matches)
- Carts array is not empty

**Add a New Cart**
- Status code is 201
- userId in response matches the userID in request body
- Response contains cart product that is an array and fields with correct data types

**Update Cart (PATCH)**
- Status code is 200
- Cart `id` matches the request URL
- Updated product data reflects the changes sent

**Delete Cart**
- Status code is 200
- `isDeleted` is `true`
- `deletedOn` is not null

---

#### ❌ Invalid Paths

**Invalid Cart ID (GET /carts/12345)**
- Status code is 404
- Response has a valid `message` string
- Error message contains the requested cart ID dynamically

**Invalid userId – Add a New Cart (userId: -1)**
- Status code is 404
- Response has a valid `message` string
- Error message contains the invalid `userId` from the request body

**No Data – Add a New Cart (empty body)**
- Status code is 400
- Response has a valid `message` string
- Error message equals `"User id is required"`

---

## Getting Started

### Prerequisites
- [Postman](https://www.postman.com/downloads/) v10+

### Import the Collection
1. Clone or download this repository
2. Open Postman → click **Import**
3. Select `collections/Dummy JSON API testing.postman_collection.json`
4. Set up the environment (see below)

---

## Environment Variables

Create a Postman environment with the following variables:

| Variable | Description | Example Value |
|----------|-------------|---------------|
| `DbaseURL` | Base URL of the API (include trailing slash) | `https://dummyjson.com/` |
| `accessToken` | Auth token – auto-populated after login | *(set automatically)* |

> **Tip:** Run the **Register Token** request first. It automatically saves the `accessToken` to collection variables, making it available to all authenticated requests in the collection.

---

## Running the Collection

### Via Postman UI
1. Right-click the collection → **Run collection**
2. Select your environment (`DbaseURL` must be set)
3. Click **Run Dummy JSON API testing**

## Folder Structure

```
ecommerce-api-testing/
├── collections/
│   └── Dummy JSON API testing.postman_collection.json
├── docs/
│   └── test-cases.md
└── README.md
```

**Collection structure:**
```
Dummy JSON API testing/
├── product API/
│   ├── Valid paths/
│   │   ├── Get All Products
│   │   ├── Get Single Product
│   │   ├── Search Products
│   │   ├── Create Product
│   │   ├── Register Token
│   │   ├── Get All Products Categories
│   │   ├── Get All Product List
│   │   ├── Update Product
│   │   └── Delete Product
│   └── Invalid Paths/
│       ├── Invalid Credentials
│       ├── Without Credentials
│       ├── Invalid Product ID
│       ├── Invalid Skip Value – Search Products
│       └── Invalid Limit Value – Search Products
└── Carts/
    ├── Valid Paths/
    │   ├── Get All Carts
    │   ├── Get Single Cart
    │   ├── Get Cart by User
    │   ├── Add a New Cart
    │   ├── Update Cart
    │   └── Delete Cart
    └── Invalid Paths/
        ├── Invalid Cart ID
        ├── Invalid UserID – Add a New Cart
        └── No Data – Add a New Cart
```

---

> **Note:** DummyJSON is a public mock API — write operations (POST/PUT/PATCH/DELETE) return simulated responses and do not persist real data.

*Built with Postman · Tested with JavaScript (pm.\* API)*
