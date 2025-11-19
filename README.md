# Monopoly Web Data Service

**Live Service URL:**  
👉 https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/

## Overview
This Azure Web App hosts a REST-based Node.js data service for the CS 262 Monopoly database.  
It connects to an Azure PostgreSQL instance using environment variables for secure credentials.

## API Endpoints

### General
| Method | Route | Description | Example |
|--------|-------|-------------|---------|
| GET | `/` | Health check / root endpoint | [link](https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/) |

### Players
| Method | Route | Description | Example |
|--------|-------|-------------|---------|
| GET | `/players` | Returns a list of all players | [link](https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players) |
| GET | `/players/{id}` | Returns a specific player by ID | [/players/1](https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players/1) |
| POST | `/players` | Creates a new player | See example below |
| PUT | `/players/{id}` | Updates an existing player's information | See example below |
| DELETE | `/players/{id}` | Deletes a player and associated game records | See example below |

### Games
| Method | Route | Description | Example |
|--------|-------|-------------|---------|
| GET | `/games` | Returns a list of all games | [link](https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/games) |
| GET | `/games/{id}` | Returns game details with all players and their scores | [/games/1](https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/games/1) |
| DELETE | `/games/{id}` | Deletes a game and associated player-game records | See example below |

## Usage Examples

### Using cURL

**Health Check:**
```bash
curl -i https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/
```

**Get All Players:**
```bash
curl -i https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players
```

**Get All Games:**
```bash
curl -i https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/games
```

**Get Specific Game with Players:**
```bash
curl -i https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/games/1
```

**Create a New Player:**
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"email":"alice@example.com", "name":"Alice Johnson"}' \
  https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players
```

**Update a Player:**
```bash
curl -X PUT \
  -H "Content-Type: application/json" \
  -d '{"email":"alice.j@example.com", "name":"Alice J. Johnson"}' \
  https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players/1
```

**Delete a Player:**
```bash
curl -X DELETE \
  https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players/1
```

**Delete a Game:**
```bash
curl -X DELETE \
  https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/games/1
```

## Response Examples

### GET /games
```json
[
  {
    "id": 1,
    "time": "2024-01-15T14:30:00.000Z"
  },
  {
    "id": 2,
    "time": "2024-01-16T19:45:00.000Z"
  }
]
```

### GET /games/:id
```json
{
  "game": {
    "id": 1,
    "time": "2024-01-15T14:30:00.000Z"
  },
  "players": [
    {
      "id": 1,
      "name": "Alice Johnson",
      "email": "alice@example.com",
      "score": 1500
    },
    {
      "id": 2,
      "name": "Bob Smith",
      "email": "bob@example.com",
      "score": 1200
    }
  ]
}
```

### GET /players
```json
[
  {
    "id": 1,
    "email": "alice@example.com",
    "name": "Alice Johnson"
  },
  {
    "id": 2,
    "email": "bob@example.com",
    "name": "Bob Smith"
  }
]
```

## Environment Variables (in Azure)
```
DB_SERVER=cs262-postgres-genevieve.postgres.database.azure.com
DB_PORT=5432
DB_USER=Gennie_oo
DB_PASSWORD=********
DB_DATABASE=monopoly
NODE_ENV=production
```

## Technical Details

### Database Schema
The service uses three main tables:
- **Player**: Stores player information (id, email, name)
- **Game**: Stores game records (id, time)
- **PlayerGame**: Junction table linking players to games with scores (playerID, gameID, score)

### Security Features
- **SQL Injection Protection**: Uses parameterized queries via pg-promise
- **Environment Variables**: Database credentials stored securely in Azure
- **Error Handling**: Errors logged server-side, generic responses sent to clients
- **CORS**: Can be configured for cross-origin requests if needed

### Transaction Support
- DELETE operations use transactions to maintain referential integrity
- Cascading deletes handled automatically (PlayerGame records deleted before Player/Game records)

## Deployment

- **CI/CD**: Automated deployment via GitHub Actions
- **Workflow File**: `.github/workflows/main_monopoly-service-gao4.yml`
- **Runtime**: Node.js 22 LTS on Linux App Service
- **Tier**: Azure Free (F1)
- **Type Checking**: Run `npm run type-check` for static TypeScript validation

## Local Development

1. **Clone the repository**
2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Set up environment variables:**
   Create a `.env.sh` file with your database credentials:
   ```bash
   export DB_SERVER=your-server.postgres.database.azure.com
   export DB_PORT=5432
   export DB_DATABASE=monopoly
   export DB_USER=your-username
   export DB_PASSWORD=your-password
   export PORT=3000
   ```

4. **Source the environment file:**
   ```bash
   source .env.sh
   ```

5. **Start the service:**
   ```bash
   npm start
   ```

6. **Access locally:**
   Navigate to `http://localhost:3000`

## Testing

### Test SQL Injection Protection
The service includes a deliberately vulnerable endpoint for educational purposes:
```bash
# Safe endpoint (protected)
curl https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players/1

# Vulnerable endpoint (DO NOT USE IN PRODUCTION)
curl https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/bad/players/1
```

## API Design Notes

### RESTful Principles
- Resource-based URLs (nouns, not verbs)
- Standard HTTP methods for CRUD operations
- Stateless request handling
- Appropriate HTTP status codes (200, 404, 500)

### Idempotency
- **Idempotent**: GET, PUT, DELETE (multiple identical requests = same result)
- **Not Idempotent**: POST (each request creates a new resource)

