# CollabFlow

CollabFlow is a multi-tenant SaaS project management platform. Teams organize their work in workspaces, control access with roles, and track projects and tasks in one place.

The backend is an ASP.NET Core Web API backed by PostgreSQL. The frontend is not implemented yet.

## Features

- JWT authentication with registration, login, and a "current user" endpoint
- Workspaces with member management
- Role-based permissions inside each workspace: Admin, Manager, Member
- Projects scoped to a workspace, with Active, Completed, and Archived states
- Domain models for tasks, comments, file attachments, notifications, and activity logs (planned API surface)

## Tech stack

- .NET 9, ASP.NET Core Web API
- Entity Framework Core 9 with Npgsql (PostgreSQL 16)
- JWT bearer authentication
- Docker Compose for the local database

## Project structure

```
backend/CollabFlow.Api   Web API (controllers, services, DTOs, EF Core models and migrations)
frontend                 Reserved for the client application
docker-compose.yml       Local PostgreSQL
```

## Getting started

Requirements: .NET 9 SDK and Docker.

1. Start the database:

   ```bash
   docker compose up -d
   ```

2. Apply the migrations:

   ```bash
   cd backend/CollabFlow.Api
   dotnet ef database update
   ```

3. Run the API:

   ```bash
   dotnet run
   ```

The API listens on `http://localhost:5007` (and `https://localhost:7117` with the https profile). In the Development environment the OpenAPI document is exposed.

## Configuration

Settings live in `backend/CollabFlow.Api/appsettings.json`:

| Key | Purpose |
| --- | --- |
| `ConnectionStrings:DefaultConnection` | PostgreSQL connection string |
| `Jwt:Key` | Signing key for tokens |
| `Jwt:Issuer`, `Jwt:Audience` | Token issuer and audience |
| `Jwt:ExpiresInMinutes` | Token lifetime |

The values in the repository are for local development only. Set your own connection string and a long random `Jwt:Key` through environment variables or user secrets in any real deployment.

## API overview

| Method | Route | Description |
| --- | --- | --- |
| GET | `/api/health` | Health check |
| POST | `/api/auth/register` | Create an account |
| POST | `/api/auth/login` | Get a JWT |
| GET | `/api/auth/me` | Current user |
| POST | `/api/workspaces` | Create a workspace |
| GET | `/api/workspaces` | List your workspaces |
| GET | `/api/workspaces/{workspaceId}` | Get a workspace |
| POST | `/api/workspaces/{workspaceId}/members` | Add a member |
| PUT | `/api/workspaces/{workspaceId}/members/{memberUserId}/role` | Change a member role |
| DELETE | `/api/workspaces/{workspaceId}/members/{memberUserId}` | Remove a member |
| POST | `/api/workspaces/{workspaceId}/projects` | Create a project |
| GET | `/api/workspaces/{workspaceId}/projects` | List projects in a workspace |
| GET | `/api/projects/{projectId}` | Get a project |
| PUT | `/api/projects/{projectId}` | Update a project |
| DELETE | `/api/projects/{projectId}` | Delete a project |

All routes except health, register, and login require a `Bearer` token.

## Roadmap

- Task API (statuses, priorities, assignment)
- Comments, file uploads, notifications, and activity feed
- Frontend client
