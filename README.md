# Monopoly Web Data Service

**Live Service URL:**  
👉 https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/

## Overview
This Azure Web App hosts a REST-based Node.js data service for the CS 262 Monopoly database.  
It connects to an Azure PostgreSQL instance using environment variables for secure credentials.

## API Endpoints
| Method | Route | Description | Example |
|--------|-------|--------------|----------|
| GET | `/` | Health check / root endpoint | [link](https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/) |
| GET | `/players` | Returns a list of players | [link](https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players) |
| GET | `/players/{id}` | Returns a specific player by ID | `/players/1` |
| POST | `/players` | Adds a new player (record in the database) | `curl -X POST -H "Content-Type: application/json" -d '{"name":"Alice"}' <URL>/players` |
| PUT | `/players/{id}` | Updates a player’s data |  |
| DELETE | `/players/{id}` | Deletes a player entry |  |

## Environment Variables (in Azure)
DB_SERVER=<cs262-postgres-genevieve.postgres>
DB_PORT=5432
DB_USER=<Gennie_oo>
DB_PASSWORD=<Miseducation_2025>
DB_DATABASE=monopoly


## Deployment
- Built and deployed automatically via GitHub Actions.  
- Workflow file: `.github/workflows/main_monopoly-service-gao4.yml`  
- Runtime: Node 22 LTS on Linux App Service Free (F1).

curl -i https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/
curl -i https://monopoly-service-gao4-dxefetbbgaa9d4bk.westus3-01.azurewebsites.net/players
