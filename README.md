# 🛒 Smart E-Commerce Checkout System

> Complete backend microservices pipeline for e-commerce checkout flow — API-driven, no frontend required.

![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)
![Flask](https://img.shields.io/badge/Flask-REST%20API-lightgrey?logo=flask)
![MySQL](https://img.shields.io/badge/MySQL-8.0-orange?logo=mysql)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-3-red?logo=rabbitmq)
![Docker](https://img.shields.io/badge/Docker-Containerized-blue?logo=docker)

---

## 📋 Project Summary

Demonstrates full e-commerce checkout workflow using decoupled microservices, mimicking **Amazon**/**Flipkart** backend architecture.

**MCA Cloud Computing coursework project.**

### Checkout Flow

```
Cart Service → Discount Service → Payment Service → Inventory Update → RabbitMQ Event
```

---

## 🏗️ Service Architecture Diagram

```
                        CLIENT (Postman)
                              |
          ┌───────────────────┼───────────────────┐
          │                   │                   │
    Port 5001           Port 5002           Port 5003
          │                   │                   │
  ┌───────┴──────┐    ┌───────┴──────┐    ┌───────┴──────┐
  │  Inventory   │    │     Cart     │    │   Discount   │
  │   Service    │    │   Service    │    │   Service    │
  └──────────────┘    └──────────────┘    └──────────────┘

                        Port 5004
                      ┌───────┴──────┐
                      │   Payment    │
                      │   Service    │
                      └──────┬───────┘
                             │
               ┌─────────────┴─────────────┐
               │                           │
        ┌──────┴──────┐           ┌────────┴───────┐
        │    MySQL    │           │   RabbitMQ     │
        │  Port 3306  │           │   Port 5672    │
        └─────────────┘           └────────────────┘
```

---

## 🛠️ Technology Stack

| Component      | Version | Usage                    |
| -------------- | ------- | ------------------------ |
| Python + Flask | 3.11    | REST API microservices   |
| MySQL          | 8.0     | Database persistence     |
| RabbitMQ       | 3       | Event-driven messaging   |
| Docker         | Latest  | Container orchestration  |
| Postman        | Latest  | API testing & validation |

---

## 📁 Project Structure

```
smart-ecommerce-checkout/
├── 📂 db-init/
│   └── init.sql                  ← Database schema + seed data
├── 📂 inventory-service/
│   ├── app.py                    ← CRUD APIs for products & stock
│   ├── requirements.txt
│   └── Dockerfile
├── 📂 cart-service/
│   ├── app.py                    ← Add items, view cart
│   ├── requirements.txt
│   └── Dockerfile
├── 📂 discount-service/
│   ├── app.py                    ← Serverless-style coupon logic
│   ├── requirements.txt
│   └── Dockerfile
├── 📂 payment-service/
│   ├── app.py                    ← Payment + RabbitMQ publisher
│   ├── requirements.txt
│   └── Dockerfile
├── 📂 screenshots/               ← Demo screenshots
├── 📄 README.md
└── 📄 LICENSE
```

---

## 🚀 Getting Started

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- [Postman](https://www.postman.com/downloads/) for API testing

### 1. Clone the Repository

git clone https://github.com/albin-shaji/smart-ecommerce-checkout.git
cd smart-ecommerce-checkout

### 2. Create Docker Network

docker network create ecommerce-net

### 3. Start Infrastructure

docker run -d --name mysql-db --network ecommerce-net -e MYSQL_ROOT_PASSWORD=root123 -e MYSQL_DATABASE=ecommerce -p 3306:3306 mysql:8.0

docker run -d --name rabbitmq --network ecommerce-net -p 5672:5672 -p 15672:15672 rabbitmq:3-management

### 4. Initialize Database

docker exec -i mysql-db mysql -uroot -proot123 < db-init/init.sql

### 5. Build & Run All Services

docker build -t inventory-service ./inventory-service
docker run -d --name inventory --network ecommerce-net -p 5001:5001 inventory-service

docker build -t cart-service ./cart-service
docker run -d --name cart --network ecommerce-net -p 5002:5002 cart-service

docker build -t discount-service ./discount-service
docker run -d --name discount --network ecommerce-net -p 5003:5003 discount-service

docker build -t payment-service ./payment-service
docker run -d --name payment --network ecommerce-net -p 5004:5004 payment-service

### 6. Verify Everything is Running

docker ps

| Container | Port        | Status |
| --------- | ----------- | ------ |
| mysql-db  | 3306        | ✅ Up  |
| rabbitmq  | 5672, 15672 | ✅ Up  |
| inventory | 5001        | ✅ Up  |
| cart      | 5002        | ✅ Up  |
| discount  | 5003        | ✅ Up  |
| payment   | 5004        | ✅ Up  |

## 🧪 Testing the Checkout Flow

### Step 1 — Add Item to Cart

POST http://localhost:5002/cart
Body: { "product_id": 1, "quantity": 2 }

### Step 2 — Apply Discount Code

POST http://localhost:5003/discount
Body: { "code": "NEWYEAR", "original_price": 100000 }

### Step 3 — Process Payment

POST http://localhost:5004/payment
Body: { "product_id": 1, "quantity": 2, "discount_code": "NEWYEAR" }

### Step 4 — Verify Inventory Updated

GET http://localhost:5001/inventory/1

### Step 5 — Verify RabbitMQ Event

Open http://localhost:15672 → Login: guest / guest → Queues tab → payment_processed

---

## 🎫 Coupon Codes List

| Coupon  | Discount | Offer Type      |
| ------- | -------- | --------------- |
| NEWYEAR | 10% off  | New Year offer  |
| SAVE20  | 20% off  | Save more offer |
| FLAT50  | 50% off  | Flat half price |

---

## 💾 Data Schema Overview

| Table     | Function                             |
| --------- | ------------------------------------ |
| inventory | Product catalog with pricing & stock |
| cart      | Temporary shopping cart items        |
| payments  | Payment transaction history          |

---

## 🔗 API Gateway URLs

| Microservice | Endpoint               |
| ------------ | ---------------------- |
| Inventory    | http://localhost:5001  |
| Cart         | http://localhost:5002  |
| Discount     | http://localhost:5003  |
| Payment      | http://localhost:5004  |
| RabbitMQ UI  | http://localhost:15672 |

---

## 🙋‍♂️ Project Creator

**Thejas K S**  
MCA Student | Palakkad, Kerala

[![GitHub](https://img.shields.io/badge/GitHub-devthejas-black?logo=github)](https://github.com/devthejas)
[![Email](https://img.shields.io/badge/Email-myname@gmail.com-red?logo=gmail)](mailto:myname@gmail.com)
