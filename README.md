![CI](https://github.com/athullur-svg/banking-ledger-api/actions/workflows/ci.yml/badge.svg)
# Banking Transaction Ledger API

A production-ready REST API built with FastAPI and PostgreSQL that simulates a banking transaction ledger system. This project demonstrates modern Python API development practices including authentication, database migrations, comprehensive testing, and containerized deployment.

## Features

- **Account Management**: Create and manage bank accounts
- **Transaction Processing**: Post debits and credits with atomic operations
- **Balance Queries**: Real-time balance calculations with transaction history
- **JWT Authentication**: Secure endpoints with token-based auth
- **Input Validation**: Pydantic models with comprehensive validation
- **Database Migrations**: Alembic for schema version control
- **API Documentation**: Auto-generated Swagger/OpenAPI docs
- **Comprehensive Testing**: Pytest with >80% coverage
- **Dockerized Setup**: One-command local development environment
- **Pagination**: Efficient data retrieval for large datasets
- **Error Handling**: Consistent error responses with proper HTTP status codes

## Tech Stack

- **FastAPI**: Modern, fast web framework
- **PostgreSQL**: Robust relational database
- **SQLAlchemy**: ORM with async support
- **Alembic**: Database migration tool
- **Pydantic**: Data validation
- **JWT**: Authentication tokens
- **Pytest**: Testing framework
- **Docker**: Containerization
- **Docker Compose**: Multi-container orchestration

## Project Structure

```
banking-ledger-api/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI application entry point
│   ├── config.py               # Configuration management
│   ├── database.py             # Database connection setup
│   ├── models/                 # SQLAlchemy models
│   │   ├── __init__.py
│   │   ├── account.py
│   │   ├── transaction.py
│   │   └── user.py
│   ├── schemas/                # Pydantic schemas
│   │   ├── __init__.py
│   │   ├── account.py
│   │   ├── transaction.py
│   │   └── user.py
│   ├── api/                    # API routes
│   │   ├── __init__.py
│   │   ├── deps.py             # Dependencies (auth, db)
│   │   ├── auth.py
│   │   ├── accounts.py
│   │   └── transactions.py
│   └── core/                   # Core utilities
│       ├── __init__.py
│       ├── security.py         # Password hashing, JWT
│       └── exceptions.py       # Custom exceptions
├── tests/
│   ├── __init__.py
│   ├── conftest.py             # Pytest fixtures
│   ├── test_auth.py
│   ├── test_accounts.py
│   └── test_transactions.py
├── alembic/                    # Database migrations
│   ├── versions/
│   └── env.py
├── alembic.ini
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
├── .env.example
├── .gitignore
└── README.md
```

## Prerequisites

- Docker and Docker Compose (recommended)
- OR Python 3.11+ and PostgreSQL 15+

## Quick Start (Docker)

1. **Clone the repository**
```bash
git clone <your-repo-url>
cd banking-ledger-api
```

2. **Set up environment variables**
```bash
cp .env.example .env
# Edit .env if needed (defaults work for Docker setup)
```

3. **Start the application**
```bash
docker-compose up --build
```

4. **Run migrations**
```bash
docker-compose exec api alembic upgrade head
```

5. **Access the API**
- API: http://localhost:8000
- Swagger Docs: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

## Manual Setup (Without Docker)

1. **Install Python dependencies**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

2. **Set up PostgreSQL**
```bash
# Install PostgreSQL and create database
createdb banking_ledger
```

3. **Configure environment**
```bash
cp .env.example .env
# Edit .env with your database credentials
```

4. **Run migrations**
```bash
alembic upgrade head
```

5. **Start the server**
```bash
uvicorn app.main:app --reload
```

## API Usage Examples

### 1. Register a User

```bash
curl -X POST "http://localhost:8000/api/auth/register" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "SecurePass123!",
    "full_name": "John Doe"
  }'
```

### 2. Login

```bash
curl -X POST "http://localhost:8000/api/auth/login" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=user@example.com&password=SecurePass123!"
```

Response:
```json
{
  "access_token": "eyJhbGc...",
  "token_type": "bearer"
}
```

### 3. Create an Account

```bash
curl -X POST "http://localhost:8000/api/accounts" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "account_type": "SAVINGS",
    "currency": "USD"
  }'
```

### 4. Post a Transaction

```bash
curl -X POST "http://localhost:8000/api/transactions" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "account_id": "123e4567-e89b-12d3-a456-426614174000",
    "amount": 1000.00,
    "transaction_type": "CREDIT",
    "description": "Initial deposit"
  }'
```

### 5. Get Account Balance

```bash
curl -X GET "http://localhost:8000/api/accounts/123e4567-e89b-12d3-a456-426614174000/balance" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 6. List Transactions (with pagination)

```bash
curl -X GET "http://localhost:8000/api/transactions?skip=0&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Running Tests

```bash
# With Docker
docker-compose exec api pytest

# With coverage report
docker-compose exec api pytest --cov=app --cov-report=html

# Without Docker
pytest
pytest --cov=app --cov-report=html
```

View coverage report: `open htmlcov/index.html`

## Database Migrations

```bash
# Create a new migration
alembic revision --autogenerate -m "Description of changes"

# Apply migrations
alembic upgrade head

# Rollback one migration
alembic downgrade -1

# View migration history
alembic history
```

## API Documentation

Once the server is running, visit:

- **Swagger UI**: http://localhost:8000/docs
  - Interactive API documentation
  - Test endpoints directly from browser
  
- **ReDoc**: http://localhost:8000/redoc
  - Alternative documentation interface

## Security Features

- **Password Hashing**: bcrypt with salt
- **JWT Tokens**: HS256 algorithm with expiration
- **SQL Injection Protection**: SQLAlchemy ORM
- **CORS**: Configurable origins
- **Input Validation**: Pydantic models
- **Rate Limiting**: (TODO: Add in production)

## Error Handling

The API uses standard HTTP status codes:

- `200 OK`: Successful request
- `201 Created`: Resource created
- `400 Bad Request`: Invalid input
- `401 Unauthorized`: Missing/invalid token
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `409 Conflict`: Duplicate resource
- `422 Unprocessable Entity`: Validation error
- `500 Internal Server Error`: Server error

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://postgres:postgres@db:5432/banking_ledger` |
| `SECRET_KEY` | JWT signing key | Random generated |
| `ALGORITHM` | JWT algorithm | `HS256` |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Token expiration | `30` |
| `CORS_ORIGINS` | Allowed CORS origins | `["http://localhost:3000"]` |

## Production Deployment Checklist

- [ ] Change `SECRET_KEY` to a strong random value
- [ ] Set `DEBUG=False`
- [ ] Configure proper `DATABASE_URL` with SSL
- [ ] Set up environment-specific `.env` files
- [ ] Enable HTTPS
- [ ] Configure CORS for production domains
- [ ] Set up logging and monitoring
- [ ] Implement rate limiting
- [ ] Add database backups
- [ ] Set up CI/CD pipeline
- [ ] Add health check endpoints
- [ ] Configure proper container resources

## Future Enhancements

- [ ] Transfer between accounts
- [ ] Transaction reversal/void
- [ ] Account statements (PDF generation)
- [ ] Multi-currency support
- [ ] Transaction categories and tagging
- [ ] Scheduled transactions
- [ ] Account freeze/unfreeze
- [ ] Audit logging
- [ ] Webhooks for transaction events
- [ ] GraphQL endpoint

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

MIT License - see LICENSE file for details

## Author

Built to demonstrate production-ready Python API development practices, showcasing skills relevant to backend engineering roles at financial institutions.

## Contact

- GitHub: [@yourusername](https://github.com/athullur-svg)
- LinkedIn: [Alekhya Thulluru](https://www.linkedin.com/in/alekhyathulluru/)
- Email: athullur@gmail.com
