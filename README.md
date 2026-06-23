# Movie-Vault

Movie-Vault is a backend application that enables users to manage their watchlists, track completed movies, rate favorites, and discover new content through intelligent recommendations. The API combines PostgreSQL, Prisma ORM, and Prisma Accelerate for data management, TMDb for movie metadata, and Google Gemini for preference analysis and personalized recommendation explanations.

**Live Demo:** https://movie-watchlist-api-axno.onrender.com

## Tech Stack

- **Runtime:** Node.js
- **Framework:** Express.js
- **Database:** PostgreSQL (hosted)
- **ORM:** Prisma ORM with Prisma Accelerate
- **Authentication:** JWT (JSON Web Tokens)
- **Validation:** Zod
- **AI Integration:** Google Gemini API
- **Movie Data Provider:** TMDb API
- **Environment Management:** dotenv
- **Package Manager:** npm
- **Development Tools:** Nodemon
- **API Testing:** Postman
- **Version Control:** Git & GitHub

## Getting Started

### Prerequisites

- Node.js (v18+)
- PostgreSQL database (local or hosted)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/movie-vault.git
   cd movie-vault
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Create a `.env` file in the project root:
   ```env
   DATABASE_URL="your-prisma-accelerate-connection-string"
   DIRECT_URL="your-direct-postgresql-connection-string"
   JWT_SECRET="your-jwt-secret"
   JWT_EXPIRES_IN="7d"
   PORT=5001
   NODE_ENV=development
   TMDB_API_KEY="your-tmdb-api-read-access-token"
   TMDB_BASE_URL="https://api.themoviedb.org/3"
   GEMINI_API_KEY="your-google-gemini-api-key"
   ```
   > **Note:** `DIRECT_URL` is used for running migrations; `DATABASE_URL` is the Prisma Accelerate connection used by the app at runtime. `TMDB_API_KEY` must be a TMDb **API Read Access Token (v4 auth)**, sent as a Bearer token.

4. Run Prisma migrations and generate the client:
   ```bash
   npx prisma migrate deploy
   npx prisma generate
   ```

5. Start the server:
   ```bash
   # Development (with hot reload)
   npm run dev

   # Production
   npm start
   ```

The server will be running at `http://localhost:5001`.

## API Documentation

Interactive Swagger docs are available at `/api-docs` when the server is running.

## API Endpoints

### Authentication

| Method | Endpoint           | Description                                  | Auth Required |
|--------|--------------------|-----------------------------------------------|---------------|
| POST   | `/auth/register`   | Register a new user                          | No            |
| POST   | `/auth/login`      | Login                                         | No            |
| POST   | `/auth/logout`     | Logout                                        | No            |
| DELETE | `/auth/delete/:id` | Delete a user (must match your own user ID)  | Yes           |

### Watchlist

All watchlist routes require a valid JWT token via the `Authorization: Bearer <token>` header.

| Method | Endpoint           | Description                       | Auth Required |
|--------|--------------------|------------------------------------|---------------|
| GET    | `/watchlist/search?query=` | Search movies via TMDb    | Yes           |
| POST   | `/watchlist`       | Add a movie to your watchlist     | Yes           |
| PUT    | `/watchlist/:id`   | Update a watchlist item           | Yes           |
| DELETE | `/watchlist/:id`   | Remove from your watchlist        | Yes           |

### Request & Response Examples

**Register**
```bash
curl -X POST http://localhost:5001/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name": "John", "email": "john@example.com", "password": "secret123"}'
```

**Search Movies (TMDb)**
```bash
curl -X GET "http://localhost:5001/watchlist/search?query=inception" \
  -H "Authorization: Bearer <your-token>"
```

**Add to Watchlist**
```bash
curl -X POST http://localhost:5001/watchlist \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <your-token>" \
  -d '{"tmdbId": "27205", "title": "Inception", "status": "PLANNED", "rating": 8, "notes": "Must watch!"}'
```

> `tmdbId` and `title` come straight from the TMDb search results above — there's no need to create a local movie record first.

### Watchlist Statuses

| Status      | Description              |
|-------------|--------------------------|
| `PLANNED`   | Planning to watch        |
| `WATCHING`  | Currently watching       |
| `COMPLETED` | Finished watching        |
| `DROPPED`   | Stopped watching         |

## Project Structure

```
movie-vault/
├── prisma/
│   ├── schema.prisma        # Database schema
│   └── migrations/
├── src/
│   ├── config/
│   │   └── db.js            # Prisma client & connection
│   ├── controllers/
│   │   ├── authController.js
│   │   └── watchListController.js
│   ├── middleware/
│   │   ├── authMiddleware.js # JWT verification
│   │   └── validateRequest.js
│   ├── routes/
│   │   ├── authRoutes.js
│   │   ├── landingPage.js
│   │   └── watchListRoutes.js
│   ├── services/
│   │   ├── tmdbService.js   # TMDb movie search
│   │   └── geminiService.js # Gemini-powered preference analysis
│   ├── utils/
│   │   └── generateToken.js
│   ├── validators/
│   │   ├── authValidators.js
│   │   └── watchlistValidators.js
│   └── server.js            # App entry point
├── swagger.yaml
├── package.json
└── .env
```

## Scripts

| Script             | Command               | Description                          |
|--------------------|-----------------------|--------------------------------------|
| `npm run dev`      | `nodemon src/server.js` | Start dev server with hot reload   |
| `npm start`        | `node src/server.js`    | Start production server            |
| `npm run build`    | `prisma generate && prisma migrate deploy` | Generate client & run migrations |

## License

ISC
