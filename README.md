# WalletCore API

A secure and scalable REST API built with Node.js and Express to manage digital wallets, user accounts, and transactions. It uses MongoDB for reliable data storage and JWT-based authentication to ensure safe and controlled access.

## Features

- **User Authentication**: Register, login, and logout functionality with secure password hashing using bcryptjs
- **Account Management**: Create and manage multiple user accounts with balance tracking
- **Transaction Processing**: Handle financial transactions between accounts with ledger tracking
- **JWT Security**: Token-based authentication with JWT for secure API endpoints
- **Email Integration**: Nodemailer support for sending notifications and confirmations
- **Token Blacklisting**: Logout functionality with token blacklist model for security
- **Protected Routes**: Role-based access control with authentication middleware
- **MongoDB Integration**: Persistent data storage with Mongoose ODM

## Tech Stack

- **Runtime**: Node.js
- **Framework**: Express.js (v5.2.1)
- **Database**: MongoDB with Mongoose (v9.2.3)
- **Authentication**: JWT (jsonwebtoken v9.0.3)
- **Password Security**: bcryptjs (v3.0.3)
- **Email Service**: Nodemailer (v8.0.1)
- **Environment Management**: dotenv (v17.3.1)
- **Cookie Management**: cookie-parser (v1.4.7)

## Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd WalletCore API
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Create environment configuration**
   - Copy `.env.example` to `.env` (if available) or create a new `.env` file
   - Configure the following variables:
     ```
     PORT=3000
      MONGO_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/<database>

      JWT_SECRET=your_jwt_secret_here

      CLIENT_ID=your_google_client_id
      CLIENT_SECRET=your_google_client_secret
      REFRESH_TOKEN=your_google_refresh_token
      EMAIL_USER=your_email@example.com
     ```

4. **Start the server**
   - Development mode (with auto-reload):
     ```bash
     npm run dev
     ```
   - Production mode:
     ```bash
     npm start
     ```

The server will start on `http://localhost:3000`

## API Endpoints

### Authentication Routes (`/api/auth`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---|
| POST | `/register` | Register a new user | No |
| POST | `/login` | Login user and receive JWT token | No |
| POST | `/logout` | Logout user and blacklist token | Yes |

### Account Routes (`/api/accounts`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---|
| POST | `/` | Create a new account | Yes |
| GET | `/` | Get all accounts for logged-in user | Yes |
| GET | `/balance/:accountId` | Get account balance | Yes |

### Transaction Routes (`/api/transactions`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---|
| POST | `/` | Create a new transaction | Yes |
| POST | `/system/initial-funds` | Create initial funds from system | System User |

## Project Structure

```
WalletCore API/
├── server.js                          # Entry point
├── package.json                       # Project dependencies
├── .env                              # Environment variables (create locally)
└── src/
    ├── app.js                        # Express app configuration
    ├── config/
    │   └── db.js                     # MongoDB connection
    ├── controllers/
    │   ├── auth.controller.js        # Authentication logic
    │   ├── account.controller.js     # Account operations
    │   └── transaction.controller.js # Transaction logic
    ├── middleware/
    │   └── auth.middleware.js        # JWT verification & auth checks
    ├── models/
    │   ├── user.model.js             # User schema
    │   ├── account.model.js          # Account schema
    │   ├── transaction.model.js      # Transaction schema
    │   ├── ledger.model.js           # Ledger records
    │   └── blackList.model.js        # Token blacklist for logout
    ├── routes/
    │   ├── auth.routes.js            # Auth endpoints
    │   ├── account.routes.js         # Account endpoints
    │   └── transaction.routes.js     # Transaction endpoints
    └── services/
        └── email.service.js          # Email sending utility
```

## Usage Examples

### 1. Register a New User

```bash
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "securePassword123",
    "name": "John Doe"
  }'
```

### 2. Login User

```bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "securePassword123"
  }'
```

Response includes JWT token to be used in subsequent requests.

### 3. Create Account (Protected Route)

```bash
curl -X POST http://localhost:3000/api/accounts \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -d '{
    "accountName": "Savings Account"
  }'
```

### 4. Get User Accounts (Protected Route)

```bash
curl -X GET http://localhost:3000/api/accounts \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

### 5. Get Account Balance (Protected Route)

```bash
curl -X GET http://localhost:3000/api/accounts/balance/<ACCOUNT_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

### 6. Create Transaction (Protected Route)

```bash
curl -X POST http://localhost:3000/api/transactions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -d '{
    "fromAccountId": "<SOURCE_ACCOUNT_ID>",
    "toAccountId": "<DESTINATION_ACCOUNT_ID>",
    "amount": 100,
    "description": "Payment for services"
  }'
```

## Authentication

The API uses JWT (JSON Web Tokens) for authentication:

1. **Login** to receive a JWT token
2. **Include the token** in the `Authorization` header as `Bearer <token>` for protected routes
3. **Logout** invalidates the token by adding it to the blacklist

Example authenticated request:
```bash
curl -X GET http://localhost:3000/api/accounts \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

## Security Features

- ✅ Password hashing with bcryptjs
- ✅ JWT-based authentication
- ✅ Token blacklisting on logout
- ✅ Protected routes with middleware
- ✅ Role-based access control (system user, regular user)
- ✅ Cookie-based session management
- ✅ Secure error handling

## Database Models

### User
- Email (unique)
- Password (hashed)
- Name
- Profile information

### Account
- User reference
- Account name/type
- Balance
- Created/Updated timestamps

### Transaction
- Source account
- Destination account
- Amount
- Description
- Transaction type
- Timestamp

### Ledger
- Account reference
- Transaction reference
- Balance changes
- Audit trail

### BlackList
- Token storage for logout management
- Expiry timestamp

## Development

### Start Development Server
```bash
npm run dev
```

This uses `nodemon` for automatic server restart on file changes.

### Key Dependencies
- `bcryptjs`: Password hashing
- `jsonwebtoken`: JWT token creation and verification
- `mongoose`: MongoDB object modeling
- `nodemailer`: Email sending
- `express`: Web framework

## Error Handling

The API returns meaningful error messages:

```json
{
  "success": false,
  "message": "Invalid credentials",
  "statusCode": 401
}
```

## Contributing

1. Create a feature branch
2. Make your changes
3. Test thoroughly
4. Submit a pull request

## License

ISC

## Support

For issues or questions, please create an issue in the repository.

---

**Version**: 1.0.0  
**Last Updated**: March 2026
