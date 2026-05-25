# TitanBridge

**School & Institutional Payments Portal**

Multi-tenant platform for school fee collection via BankLink (bank transfers) and OctoPlus (card payments).

Built by Team Titans @ CoralPay Technologies, Lagos.

---

## Project Overview

- **Technology:** ASP.NET Core MVC (.NET 8, C#)
- **Database:** SQL Server with Entity Framework Core
- **Cache:** Redis
- **Queue:** RabbitMQ
- **Team:** David (Lead, UI/UX), Aisosa (Integrations), Victory (Infrastructure)
- **Status:** Active Development

---

## Quick Start (For All Developers)

### Prerequisites

Before you can run TitanBridge locally, install:

1. **.NET 8 SDK** — [Download](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)
2. **Visual Studio 2022** or **Visual Studio Code** with C# extension
3. **SQL Server 2022** (LocalDB is fine for development) — [Download](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)
4. **Redis** (optional for local development; we'll use in-memory cache initially) — [Download](https://redis.io/download)
5. **Git** — [Download](https://git-scm.com/)

### Clone the Repository

```bash
git clone https://github.com/coralpaytechnologies/titanbridge.git
cd titanbridge
```

### Local Development Setup

1. **Create appsettings.Development.json** (NOT committed to git):

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=TitanBridgeDb;Trusted_Connection=true;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning"
    }
  },
  "BankLink": {
    "ApiBaseUrl": "https://staging-api.banklink.coralpaytechnologies.com",
    "ApiKey": "YOUR_API_KEY_HERE",
    "ApiSecret": "YOUR_API_SECRET_HERE"
  },
  "OctoPlus": {
    "ApiBaseUrl": "https://staging-api.octoplus.coralpaytechnologies.com",
    "ApiKey": "YOUR_API_KEY_HERE"
  }
}
```

2. **Restore NuGet Packages:**

```bash
dotnet restore
```

3. **Run Migrations:**

```bash
dotnet ef database update
```

This creates the database and all tables.

4. **Run the Application:**

```bash
dotnet run
```

The app will be available at `https://localhost:5001`.

### Branching & Workflow

- **main:** Production code. Protected. Never push directly.
- **dev:** Integration branch. All features merge here first.
- **feature/student-portal:** David's branch
- **feature/payment-integration:** Aisosa's branch
- **feature/admin-infrastructure:** Victory's branch

### Making Changes

1. **Pull the latest dev branch:**
   ```bash
   git checkout dev
   git pull origin dev
   ```

2. **Create your feature branch:**
   ```bash
   git checkout -b feature/<feature-name>
   ```

3. **Make changes, commit often:**
   ```bash
   git add .
   git commit -m "Clear, descriptive commit message"
   ```

4. **Push to GitHub:**
   ```bash
   git push origin feature/<feature-name>
   ```

5. **Create a Pull Request (PR):**
   - Go to GitHub repo
   - Click "New Pull Request"
   - Select your branch → dev
   - Write PR description (what you changed, why)
   - Request review from David (development lead)

6. **After approval, merge and delete the branch:**
   ```bash
   git checkout dev
   git pull origin dev
   ```

---

## Project Structure

```
TitanBridge/
├── TitanBridge.Web/                          # Main MVC application
│   ├── Controllers/
│   │   ├── Student/                          # David: student controllers
│   │   ├── Admin/                            # Victory: admin controllers
│   │   ├── Webhooks/                         # Aisosa: webhook handlers
│   │   └── Api/                              # Shared API controllers
│   ├── Views/
│   │   ├── Student/                          # David: student views
│   │   ├── Admin/                            # Victory: admin views
│   │   ├── Shared/                           # David: layout and shared components
│   │   └── Home/
│   ├── Services/
│   │   ├── Payment/                          # Aisosa: payment service
│   │   ├── BankLink/                         # Aisosa: BankLink integration
│   │   ├── OctoPlus/                         # Aisosa: OctoPlus integration
│   │   ├── Notification/                     # Aisosa: email/SMS
│   │   ├── Admin/                            # Victory: admin business logic
│   │   └── Interfaces/                       # All: service interfaces
│   ├── Data/
│   │   ├── AppDbContext.cs                   # Victory: EF Core context
│   │   ├── Models/                           # Victory: entity models
│   │   ├── Migrations/                       # Victory: database migrations
│   │   └── Seeders/                          # Test data
│   ├── Middleware/
│   │   ├── TenantMiddleware.cs               # Victory: tenant identification
│   │   └── ErrorHandlingMiddleware.cs
│   ├── wwwroot/
│   │   ├── css/                              # David: stylesheets
│   │   ├── js/                               # David: client-side scripts
│   │   └── images/
│   ├── appsettings.json                      # Shared configuration
│   ├── Program.cs                            # Dependency injection setup
│   └── Startup.cs
│
├── TitanBridge.Worker/                       # Aisosa: background worker
│   ├── Jobs/
│   ├── Consumers/                            # RabbitMQ message consumers
│   └── Services/
│
├── TitanBridge.Tests/                        # Unit & integration tests
│   ├── Controllers/
│   ├── Services/
│   └── Integration/
│
├── docs/                                      # David: design system, diagrams
│   ├── DESIGN_SYSTEM.md
│   ├── ARCHITECTURE.md
│   └── SEQUENCES/
│
├── .gitignore
├── .gitattributes
├── README.md (this file)
├── CONTRIBUTING.md
├── TitanBridge.sln                           # Solution file
└── LICENSE
```

---

## Technology & Dependencies

### Core Packages (To Be Added)

- **EntityFramework Core:** ORM for database
- **Identity:** ASP.NET Core authentication
- **Serilog:** Logging
- **MassTransit + RabbitMQ:** Message queue
- **StackExchange.Redis:** Redis client
- **Newtonsoft.Json:** JSON parsing
- **AutoMapper:** Object mapping

See `TitanBridge.Web.csproj` for exact versions.

---

## Database

### Connection String

Development uses SQL Server LocalDB. Connection string is in `appsettings.Development.json`.

### Migrations

Victory manages migrations. To add a migration after changing models:

```bash
dotnet ef migrations add <MigrationName>
dotnet ef database update
```

### Multi-Tenancy

Every table includes a `TenantId` column. Queries are automatically filtered by current tenant via EF Core's `HasQueryFilter()`.

---

## Development Standards

### Naming Conventions

- **Classes:** PascalCase (e.g., `StudentService`)
- **Methods:** PascalCase (e.g., `GetStudentFees()`)
- **Variables/parameters:** camelCase (e.g., `studentId`)
- **Database fields:** PascalCase matching C# property names
- **Enums:** PascalCase values (e.g., `PaymentStatus.Pending`)

### Code Review Process

1. Create a PR against `dev`
2. Assign David as reviewer
3. Address feedback
4. Once approved, squash commits and merge

### Commit Messages

Keep them clear and descriptive:

```
Bad:  "fixes stuff"
Good: "Add BankLink webhook handler with idempotency check"

Bad:  "WIP"
Good: "WIP: Student registration form (60% complete)"
```

---

## Running Tests

```bash
dotnet test
```

Tests should cover:
- Payment processing logic
- Multi-tenant isolation
- Webhook handling

---

## Troubleshooting

### "Unable to create a DbContext"
- Ensure SQL Server is running
- Check `appsettings.Development.json` connection string
- Run `dotnet ef database update`

### "Package not found"
- Run `dotnet restore`
- Check NuGet.org availability

### "Port 5001 already in use"
- Kill the process or use `dotnet run --urls=https://localhost:5002`

---

## Deployment (Later Phase)

- **Staging:** Merge to `staging` branch (auto-deploys to staging environment)
- **Production:** Merge to `main` (manual approval required)

See `DEPLOYMENT.md` for details.

---

## Contact

- **Development Lead:** David
- **Supervisor:** [Supervisor name]
- **Team:** Team Titans @ CoralPay

For questions, ping the team in Slack or create an issue on GitHub.

---

**Last Updated:** May 2026  
**Team:** Team Titans  
**Status:** Active Development
