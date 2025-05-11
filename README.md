# wekee-be

# Wekee Africa Platform - Backend Technical Documentation

## Overview

Wekee Africa is a logistics platform offering **Moving, Courier, and Delivery services**, along with an integrated **eCommerce shop**, tailored for the African market. This documentation outlines the backend architecture, core modules, database structure, API endpoints, and development roadmap.

Wekee Africa's mission is to **simplify logistics and empower local businesses** with a full-stack solution that combines delivery, real-time tracking, online shopping, and job access in one ecosystem.

---

## Table of Contents

1. User Roles
2. Core Features & Functionalities
3. Database Schema (Entities & Relationships)
4. API Endpoints (RESTful)
5. Tech Stack
6. Future Features
7. Development Notes

---

## 1. User Roles

* **Customer**: Requests services, shops, tracks deliveries, confirms drop-offs, and makes payments.
* **Driver/Partner**: Accepts/executes jobs, tracks status, confirms deliveries with signatures, and views earnings.
* **Admin**: Full access to users, orders, shop, dashboard analytics, blogs, and financials. Admin can do anything any other user can do, plus manage the entire system and view analytics.
* **Support Agent**: Oversees system activity, confirms driver bookings, assists with assigning packages, verifies driver confirmations, and moderates blog content. They preview and match store orders with drivers and monitor delivery statuses.

---

## 2. Core Features & Functionalities

### A. Booking & Request System

* Pickup & Drop-off entry
* Instant quote calculator
* Select service type: `Moving`, `Courier`, `Delivery`
* Schedule bookings (Now or Later)
* Apply promo codes

### B. Order Management

* Create, update, cancel bookings
* Live status updates (Requested → Picked → En Route → Delivered)
* Admin/Agent assigns/reassigns driver
* Driver signs at pickup; customer confirms at drop-off

### C. Pricing Engine

* Base Fare + Distance + Weight
* Supports dynamic pricing
* Zone-based pricing logic

### D. Payments & Wallet

* Mobile Money (MTN, Airtel)
* Cards (Stripe/Flutterwave)
* Driver wallets & payout requests
* Shop orders integrated with delivery and payment

### E. Real-Time Tracking & Route Optimization

* Real-time tracking begins once a driver accepts a booking and goes to the pickup location
* Once pickup is confirmed, clients, agents, and admins can view real-time progress
* Information includes current location, estimated time remaining, and trip details
* Drivers can only view tracking details of the packages they are delivering

### F. Notifications

* SMS / Email / Push
* Booking and shop updates, confirmations, payment alerts

### G. Rating & Feedback

* Customer rates driver/shop order
* Admin reviews complaints

### H. eCommerce Shop

* Product listings & categories
* Cart, checkout, and integrated delivery
* Discounted delivery for Wekee store customers
* Admin and Agents manage product inventory, prices, and promotions
* Orders sync with delivery module for fulfillment
* Agent/Admin preview orders, match with driver, and handle exceptions
* If a driver rejects a package, rejection reason is recorded for resolution (new driver dispatch or notify client if undeliverable)

### I. Analytics Dashboard

* Tracks number of clients, booking requests, confirmed bookings
* Service usage breakdown (Moving, Courier, Delivery)
* Revenue analytics per service and per user type

### J. Blog System

* Admin/Agents can post blogs for SEO/engagement
* Categories: Tips, Announcements, Success Stories
* Customers can view via frontend

### K. Jobs & Internship Listings

* Listings for internal Wekee roles or partners
* Apply with uploaded CV or form
* Admin approval and status updates

### L. Digital Receipts & Signatures

* Pickup: Driver signs confirmation on app
* Drop-off: Customer confirms delivery (via OTP or digital sign)
* Receipt sent via email/SMS or downloadable

---

## 3. Database Schema (Simplified)

### Entities

* `users` (id, username[unique], name, role, contact_info, password_hash, status, profile_info)
* `bookings` (id, username, driver_username, type, pickup, dropoff, price, status, timestamps)
* `vehicles` (id, driver_username, type, license_plate, capacity)
* `payments` (id, booking_id, username, method, amount, status, timestamp)
* `wallets` (username, balance, transactions[])
* `ratings` (id, booking_id, username, driver_username, score, feedback)
* `promo_codes` (id, code, discount_type, value, expiry, usage_limit)
* `zones` (id, name, rate_per_km, base_fare)
* `products` (id, name, category, price, stock, description, image_url, status)
* `shop_orders` (id, username, product_items[], total, delivery_status, timestamps)
* `tracking` (id, booking_id, current_coords, updated_at)
* `blogs` (id, title, content, tags[], status, created_by, created_at)
* `jobs` (id, title, description, type, status, posted_by, applicants[], deadline)
* `signatures` (id, booking_id, type[pickup|dropoff], signed_by, signature_image_url, timestamp)
* `rejections` (id, booking_id, driver_username, reason, timestamp)

---

## 4. API Endpoints (Examples)

Base URL prefix: `/api/v1`

### Auth

* `POST /api/v1/auth/register` – Register (unique username enforced)
* `POST /api/v1/auth/login`
* `POST /api/v1/auth/forgot-password`
* `POST /api/v1/auth/reset-password`

### Users

* `GET /api/v1/users` – list all users
* `GET /api/v1/users/:username` – get user by username
* `PATCH /api/v1/users/:username/profile` – update user profile (addresses, payment methods, contacts, etc.)

### Bookings

* `POST /api/v1/bookings`
* `GET /api/v1/bookings/:id`
* `PATCH /api/v1/bookings/:id/status`
* `POST /api/v1/bookings/:id/confirm`
* `POST /api/v1/bookings/:id/signature`
* `POST /api/v1/bookings/:id/reject` – driver rejects with reason


### Drivers

* `GET /api/v1/drivers/available`
* `PATCH /api/v1/drivers/:username/status`
* `GET /api/v1/drivers/:username/track`

### Tracking

* `GET /api/v1/tracking/:booking_id`

### Shop

* `GET /api/v1/shop/products`
* `GET /api/v1/shop/products/:id`
* `POST /api/v1/shop/products`
* `PATCH /api/v1/shop/products/:id`
* `DELETE /api/v1/shop/products/:id`
* `GET /api/v1/shop/products/analytics`
* `POST /api/v1/shop/cart`
* `POST /api/v1/shop/checkout`
* `GET /api/v1/shop/orders/:username`

### Admin

* `GET /api/v1/admin/users`
* `POST /api/v1/admin/assign` - e.g. assign driver..
* `GET /api/v1/admin/dashboard`
* `GET /api/v1/admin/shop-orders`
* `POST /api/v1/admin/blogs`
* `GET /api/v1/admin/jobs`
* `GET /api/v1/admin/analytics` – clients, requests, bookings, top services, revenue

### Blog

* `GET /api/v1/blogs`
* `GET /api/v1/blogs/:id`

### Jobs

* `GET /api/v1/jobs`
* `POST /api/v1/jobs/apply`

---

## 6. Future Features

* Wekee API integration into third-party stores (WooCommerce, Shopify)
* AI-powered dispatch recommendations and customer support
* Package recommendation engine (based on weight/urgency)
* Partner APIs for third-party delivery fulfillment

---



*End of Wekee Africa Platform Backend Documentation v1.4*
