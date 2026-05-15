# 📋 Logira (Commercial Management System)

A fullstack web application for managing clients, products, inventory, and orders.

The system was built with **FastAPI** on the backend and **Vanilla JavaScript** on the frontend. It includes JWT authentication, protected routes, PostgreSQL persistence, Alembic migrations, Docker support, and an n8n integration for automatically sending order details through WhatsApp.

### 🖥️ Main Screen

![Main Screen](media/demo.png)

## 📈 Features

- Order creation, update, and deletion
- Order item management
- Automated order sending through n8n
- Client registration and management
- Product registration and management
- Inventory control
- User authentication with JWT
- Protected application routes
- Request logging middleware
- Basic rate limiting middleware
- PostgreSQL database persistence
- Database migrations with Alembic

## 🛠️ Technologies

### 🗄️ Backend

- Python 3.14
- FastAPI
- SQLAlchemy
- PostgreSQL
- Alembic
- JWT authentication
- Passlib / bcrypt
- Jinja2 templates

### 🎨 Frontend

- HTML5
- CSS3
- JavaScript
- Vanilla JS modular structure

### 🔧 Infrastructure

- Docker
- Docker Compose
- PostgreSQL 16
- Coolify
- n8n

## 🏢 Project Organization

### 🗃️ Structure

```
📂 logira-api/
│
├── backend/
│   ├── alembic/
│   │   ├── versions/
│   │   ├── env.py
│   │   └── script.py.mako
│   │
│   ├── src/
│   │   ├── middlewares/
│   │   ├── models/
│   │   ├── repositories/
│   │   ├── routes/
│   │   ├── schemas/
│   │   ├── security/
│   │   ├── config.py
│   │   ├── database.py
│   │   └── main.py
│   │
│   ├── static/
│   │   ├── app/
│   │   ├── core/
│   │   ├── pages/
│   │   ├── main.js
│   │   ├── styles.css
│   │   └── responsive.css
│   │
│   ├── templates/
│   │   ├── index.html
│   │   └── login.html
│   │
│   ├── Dockerfile
│   ├── alembic.ini
│   └── requirements.txt
│
├── media/
│   ├── demo.png
│   └── demo.gif
│
├── workflow/
│   └── main.json
│
├── docker-compose.yml
└── README.md
```

## 🏗️ Architecture

The backend follows a layered architecture:

- **Middlewares** → Request logging, rate limiting, and static file cache control
- **Models** → SQLAlchemy ORM models
- **Repositories** → Database access layer
- **Routes** → HTTP endpoints
- **Schemas** → Data validation with Pydantic
- **Security** → Authentication, password hashing, JWT creation, and route protection
- **Config** → Environment variable loading and application settings

The frontend uses a modular Vanilla JavaScript structure with separation between:

- Global state
- API communication
- Authentication guard
- UI feedback
- Form handling
- Page rendering
- Tab navigation

## 🧩 Main API Modules

The API is organized by business domain:

| Module | Base Path | Description |
|--------|-----------|-------------|
| Auth | `/auth` | Handles login and access token refresh |
| Clients | `/clientes` | Manages client registration, search, update, and deletion |
| Products | `/produtos` | Manages product registration, update, and deletion |
| Inventory | `/estoque` | Manages product stock levels and stock reset |
| Orders | `/pedidos` | Handles order creation, update, items, and deletion |
| Users | `/usuarios` | Manages users and current authenticated user profile |
| n8n | `/n8n` | Sends order data to an external n8n workflow |

Most business routes are protected and require a valid authenticated user.

## 🔐 Authentication

The system uses JWT authentication.

Main authentication flow:

1. The user logs in with email and password.
2. The API returns an access token and a refresh token.
3. Protected routes require the access token.
4. The refresh token can be used to generate a new access token.

## 🔀 n8n Flow

The n8n workflow was created to send order information to clients through WhatsApp.

It is triggered from the system when an order is sent through the actions column in the orders page.

### ⚙️ How it works

1. The system sends order data to the backend.
2. The backend forwards the order payload to the configured n8n webhook.
3. n8n receives the data through a Webhook node.
4. The workflow processes the order information.
5. The order message is formatted.
6. The message is sent to the client using the registered phone number.

### 🖥️ Workflow Running

![Workflow Running](media/demo.gif)

## 🗄️ Database

The system uses PostgreSQL with Alembic migrations.

Main database entities:

- Users
- Clients
- Products
- Inventory
- Orders
- Order items

Important relationships:

- A client can have multiple orders.
- An order can have multiple items.
- Each order item is linked to a product.
- Each product has an inventory record.

## 🚀 How to Run the Project

### 1. Clone the repository

```
git clone https://github.com/kahsale94/logira-api.git
cd logira-api
```

### 2. Create a virtual environment

Linux/macOS:

```
cd backend
python -m venv venv
source venv/bin/activate
```

Windows:

```
cd backend
python -m venv venv
.\venv\Scripts\activate
```

### 3. Install dependencies

```
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a `.env` file in the project root:

```
ENVIRONMENT=development

DATABASE_URL=postgresql://user:password@localhost:5432/logira

SECRET_KEY=your_secret_key
ALGORITHM=HS256

ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7

N8N_URL=your_n8n_webhook_url
N8N_KEY=your_internal_n8n_key

POSTGRES_USER=user
POSTGRES_PASSWORD=password
POSTGRES_DB=logira
```

#### Environment variables

| Variable | Description |
|----------|-------------|
| ENVIRONMENT | Defines the application environment |
| DATABASE_URL | PostgreSQL connection string |
| SECRET_KEY | Secret key used to sign JWT tokens |
| ALGORITHM | JWT signing algorithm |
| ACCESS_TOKEN_EXPIRE_MINUTES | Access token expiration time |
| REFRESH_TOKEN_EXPIRE_DAYS | Refresh token expiration time |
| N8N_URL | n8n webhook URL used to send order data |
| N8N_KEY | Internal secret used when calling the n8n webhook |
| POSTGRES_USER | PostgreSQL user used by Docker Compose |
| POSTGRES_PASSWORD | PostgreSQL password used by Docker Compose |
| POSTGRES_DB | PostgreSQL database name used by Docker Compose |

### 5. Run database migrations

```
alembic upgrade head
```

### 6. Run the application locally

```
uvicorn src.main:app --reload
```

The application will be available at:

```
http://localhost:8000
```

The login page will be available at:

```
http://localhost:8000/login
```

In development mode, the API documentation is available at:

```
http://localhost:8000/docs
```

In production mode, the API documentation is disabled.

## 🐳 Running with Docker Compose

Before running the project with Docker Compose, create the external networks:

```
docker network create logira-network
docker network create n8n-network
```

Then run:

```
docker compose up -d --build
```

The Docker Compose setup includes:

- PostgreSQL database
- Migration container
- Backend application

The migration container runs:

```
alembic upgrade head
```

After the database is healthy and migrations are completed, the backend starts automatically.

## 📦 Importing the n8n Workflow

1. Open n8n.
2. Go to **Import Workflow**.
3. Import the file:

```
workflow/main.json
```

4. Configure the required credentials.
5. Update the webhook URL in the `.env` file using the `N8N_URL` variable.
6. Set the same internal secret in both the backend and n8n using `N8N_KEY`.

## 💭 Considerations

This project was designed for small business management scenarios.

For high traffic, multiple users, or larger production environments, the system could be improved with:

- Distributed cache using Redis
- Queue-based asynchronous processing
- Horizontal scaling
- More restrictive CORS settings
- Centralized logging
- Automated tests
- CI/CD pipeline
- Monitoring and observability

## 🔮 Next Steps

- Refactor the frontend to React
- Add automated tests with pytest
- Improve logging strategy
- Implement CI/CD
- Improve production security settings
- Add advanced reporting
- Improve inventory movement history
- Expand the n8n automation flow
- Add automated customer service workflows