#  DummyJSON API Testing – Postman Collection

A comprehensive API test suite built with **Postman** for the [DummyJSON](https://dummyjson.com/) REST API. This collection covers functional, validation, and edge-case testing across multiple resource modules using JavaScript test scripts.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Modules Covered](#modules-covered)
- [Test Coverage](#test-coverage)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Running the Collection](#running-the-collection)
- [Folder Structure](#folder-structure)

---

## Overview

This project tests the DummyJSON public REST API across key e-commerce and content endpoints. Each request includes automated assertions that validate status codes, response body structure, field types, and business logic (e.g. unique IDs, valid ratings, non-negative stock).

**Tools Used:**
- [Postman](https://www.postman.com/) – API testing & automation
- JavaScript (Postman test scripts using `pm.*` API)
- Collection variables for token management

---

## Modules Covered

| Module | Methods Tested |
|--------|---------------|
| **Products** | GET, POST, PUT, PATCH, DELETE |
| **Auth** | POST (Login / Token generation) |
| **Carts** | GET, POST, PATCH, DELETE |
| **Recipes** | GET, POST, PATCH, DELETE |

---

## Test Coverage

### ✅ Products API
- Get all products – validates array structure, required fields (`id`, `title`, `price`), unique IDs, and rating range (0–5)
- Get single product – validates correct ID is returned, product structure, valid rating, and non-negative stock
- Search products – validates response structure, pagination (`limit`, `skip`, `total`), and result count
- Get all categories – validates array response, non-empty list, and that each category has `slug`, `name`, and a valid `url`
- Get category list – validates string-only array, non-empty, and uniqueness of entries
- Create product – validates 201 status, returned fields match request body, price above 0
- Update product (PUT/PATCH) – validates ID match, all expected fields present with correct types
- Delete product – validates `isDeleted: true` and `deletedOn` is not null

### ✅ Auth API
- Login / Register token – validates access token and refresh token are created, user details (`id`, `username`, `email`, `firstName`, `lastName`) are valid, and token is stored as a collection variable

### ✅ Carts API
- Get all carts, get single cart, get carts by user
- Add to cart, update cart, delete cart
- Response structure and field-type validation

### ✅ Recipes API
- Get all recipes, get single recipe, search recipes
- Filter by cuisine and meal type
- Add, update, and delete recipe
- Delete validation: checks `id`, `isDeleted`, and `deletedOn`

---

## Getting Started

### Prerequisites
- [Postman](https://www.postman.com/downloads/) installed (v10+ recommended)

### Import the Collection

1. Clone or download this repository
2. Open Postman
3. Click **Import** → select `Dummy_JSON_API_testing_postman_collection.json`
4. Set up the environment (see below)

---

## Environment Variables

Create a Postman environment and set the following variables:

| Variable | Description | Example Value |
|----------|-------------|---------------|
| `DbaseURL` | Base URL for the API | `https://dummyjson.com/` |
| `accessToken` | Auth token (auto-set after login) | *(auto-populated)* |

> **Tip:** Run the **Register Token** request first. It will automatically save the `accessToken` to your collection variables for use in authenticated requests.

---

## Running the Collection

### In Postman UI
1. Right-click the collection → **Run collection**
2. Select the environment with `DbaseURL` set
3. Click **Run Dummy JSON API testing**

### With Newman (CLI)
```bash
# Install Newman
npm install -g newman

# Run the collection
newman run Dummy_JSON_API_testing_postman_collection.json \
  --env-var "DbaseURL=https://dummyjson.com/"
```

---

## Folder Structure

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
│   └── Invalid paths/
│       └── (negative / edge case tests)
├── Carts API/
│   └── (CRUD tests for carts)
└── Recipes API/
    └── (CRUD + filter tests for recipes)
```

---

## 📌 Notes

- This collection uses the **DummyJSON** public API — responses are simulated; write operations (POST/PUT/DELETE) return mock responses and do not persist real data.
- The `accessToken` is captured automatically from the login response and stored as a collection variable for use in protected endpoints.
- Some tests intentionally validate edge cases such as pagination boundaries, empty arrays, and data type integrity.

---

*Built with ❤️ using Postman*
