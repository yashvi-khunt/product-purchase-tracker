# Product Purchase Tracker

> A full-featured ASP.NET MVC web application for managing manufacturers, products, pricing rates, and purchase invoices — with built-in user authentication and role-based access control.

---

## Overview

**Product Purchase Tracker** is an internal business tool designed to help small-to-medium enterprises record, track, and review product purchases across multiple manufacturers. It solves the common pain point of manually managing invoices, product catalogs, and price history in spreadsheets by providing a centralized, database-backed web application with a clean UI.

**Who it's for:** Operations teams, procurement managers, or developers learning full-stack ASP.NET MVC with Entity Framework.

**What makes it notable:** The application demonstrates a production-style MVC architecture with soft-delete patterns, time-versioned pricing rates, dynamic AJAX-driven invoice forms, and ASP.NET Identity for secure multi-user access — all within a single, cohesive .NET solution.

---

## 🚀 Live Demo

🚀 Demo coming soon

---

## Tech Stack

| Category | Technology |
|---|---|
| Framework | ASP.NET MVC 5 (.NET Framework 4.7.2) |
| Language | C# |
| ORM | Entity Framework 6 (Code First) |
| Database | SQL Server (LocalDB / MSSQL) |
| Authentication | ASP.NET Identity 2 + OWIN |
| Frontend | Razor Views, Bootstrap 5, jQuery 3 |
| Validation | jQuery Validation + Unobtrusive Validation |
| JSON Serialization | Newtonsoft.Json 12 |
| Database Migrations | EF Code First Migrations |

---

## ✨ Key Features

- 🏭 **Manufacturer Management** — Create, edit, and soft-delete manufacturers; cascading soft-delete propagates to associated products and their rates automatically.
- 📦 **Product Catalog** — Manage products linked to manufacturers with unique name constraints enforced at the database level.
- 💲 **Time-Versioned Pricing Rates** — Track historical price changes per product with effective dates; invoices always reference the most recent active rate at the time of purchase.
- 🧾 **Multi-Line Invoice Entry** — Dynamic invoice form that uses AJAX to load products by manufacturer and auto-populate the latest rate, supporting multiple line items per invoice in a single submission.
- 📋 **Purchase History** — View all invoices grouped by invoice ID with drill-down to full line-item details (manufacturer, product, rate, quantity, date).
- 🔒 **User Authentication & Roles** — Registration, login, and role-based access powered by ASP.NET Identity; a seeded admin user and role are created via EF Migrations.
- 🗑️ **Soft Delete** — Records are never hard-deleted; `IsDeleted` flags preserve historical data integrity across purchases, products, and manufacturers.
- ✅ **Server-Side Validation** — Model validation annotations (`[Required]`, `[StringLength]`, unique indexes) enforced on both the server and client via unobtrusive validation.

---

## Architecture Overview

The application follows the standard **ASP.NET MVC** pattern with server-rendered Razor views — there is no separate frontend or API layer.

```
Browser
   │
   ▼
Razor Views (.cshtml)          ← Presentation layer: HTML rendered on the server
   │
   ▼
Controllers (C#)               ← Request handling, business logic, EF queries
   │  ├── ManufacturerController
   │  ├── ProductController
   │  ├── RateController
   │  ├── PurchaseHistoryController
   │  └── AccountController / ManageController (Identity)
   │
   ▼
ViewModels                     ← Typed data containers passed from controllers to views
   │  ├── ProductFormViewModel
   │  ├── RateFormViewModel
   │  └── InvoiceFormViewModel
   │
   ▼
Models / Domain Entities       ← EF Code First entities with data annotations
   │  ├── Manufacturer  ──┐
   │  ├── Product       ──┼── Soft-delete cascade via extension methods
   │  ├── Rate          ──┘
   │  └── PurchaseHistory
   │
   ▼
ApplicationDbContext (EF 6)    ← Unit of work / repository via DbContext
   │
   ▼
SQL Server Database            ← Schema managed by EF Code First Migrations
```

**AJAX integration:** The invoice form uses jQuery `$.ajax` calls to the `PurchaseHistoryController` JSON endpoints (`GetProducts`, `GetRate`, `GetInvoiceId`) to dynamically populate dropdowns and auto-fill rate fields without a full page reload. The completed line-item table is serialized to JSON and posted back in a single form submission.

---

## Getting Started

### Prerequisites

- [Visual Studio 2019 or later](https://visualstudio.microsoft.com/) with the **ASP.NET and web development** workload
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/en-us/download/dotnet-framework/net472)
- SQL Server (LocalDB is sufficient; ships with Visual Studio)
- NuGet Package Manager (included with Visual Studio)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yashvi-khunt/product-purchase-tracker.git
   cd product-purchase-tracker
   ```

2. **Open the solution in Visual Studio**
   ```
   Open Exercise3.sln
   ```

3. **Restore NuGet packages**
   Visual Studio restores packages automatically on build, or run:
   ```
   Tools → NuGet Package Manager → Restore NuGet Packages
   ```

4. **Apply database migrations**
   Open the **Package Manager Console** (`Tools → NuGet Package Manager → Package Manager Console`) and run:
   ```powershell
   Update-Database
   ```
   This creates the database, applies all migrations, and seeds the admin user and role.

### Environment Variables / Connection String

Update the connection string in `Exercise3/Web.config` to point to your SQL Server instance:

```xml
<connectionStrings>
  <add name="DefaultConnection"
       connectionString="Data Source=.\SQLEXPRESS;AttachDbFilename=|DataDirectory|\aspnet-Exercise3.mdf;Initial Catalog=aspnet-Exercise3;Integrated Security=True"
       providerName="System.Data.SqlClient" />
</connectionStrings>
```

Replace `.\SQLEXPRESS` with your SQL Server instance name (e.g., `(LocalDB)\MSSQLLocalDB` for LocalDB).

### Run Locally

Press **F5** in Visual Studio (or use `Ctrl+F5` to run without debugging). The application will launch in your default browser at `https://localhost:{port}/`.

---

## Project Structure

```
product-purchase-tracker/
├── Exercise3.sln                   # Visual Studio solution file
├── exercise3.sql                   # Reference SQL script
└── Exercise3/                      # Main ASP.NET MVC project
    ├── Controllers/                # MVC controllers — one per domain entity
    │   ├── AccountController.cs    # Registration, login, logout (Identity)
    │   ├── HomeController.cs       # Landing page
    │   ├── ManageController.cs     # User profile & password management
    │   ├── ManufacturerController.cs
    │   ├── ProductController.cs
    │   ├── PurchaseHistoryController.cs  # Includes AJAX JSON endpoints
    │   └── RateController.cs
    ├── Models/                     # EF Code First domain entities & Identity
    │   ├── IdentityModels.cs       # ApplicationUser, ApplicationDbContext
    │   ├── Manufacturer.cs         # + ManufacturerExtensions (soft-delete)
    │   ├── Product.cs              # + ProductExtensions (soft-delete)
    │   ├── PurchaseHistory.cs
    │   ├── Rate.cs
    │   ├── RoleNames.cs            # Role name constants
    │   ├── AccountViewModels.cs    # Login/register view models
    │   ├── ManageViewModels.cs     # Profile management view models
    │   └── ExtensionGenericDelete.cs
    ├── ViewModel/                  # Custom view models for forms
    │   ├── InvoiceFormViewModel.cs
    │   ├── ProductFormViewModel.cs
    │   └── RateFormViewModel.cs
    ├── Views/                      # Razor views grouped by controller
    │   ├── Account/                # Login, register, etc.
    │   ├── Home/
    │   ├── Manage/
    │   ├── Manufacturer/           # Index, ManufacturerForm
    │   ├── Product/                # Index, ProductForm
    │   ├── PurchaseHistory/        # Index, Details, InvoiceForm
    │   ├── Rate/                   # Index, RateForm
    │   └── Shared/                 # Layout, error pages, partials
    ├── Migrations/                 # EF Code First migration files
    ├── App_Start/                  # RouteConfig, BundleConfig, etc.
    ├── Content/                    # CSS and static assets
    ├── Scripts/                    # JavaScript files (jQuery, Bootstrap, etc.)
    ├── Global.asax(.cs)            # Application startup
    ├── Startup.cs                  # OWIN pipeline configuration
    ├── Web.config                  # App settings and connection strings
    └── packages.config             # NuGet package references
```

---

## 📸 Screenshots

📸 Screenshots coming soon

---

## What I Learned

- 🔗 **Entity Framework relationships & migrations** — I deepened my understanding of configuring one-to-many relationships with Code First, managing schema evolution through EF Migrations, and seeding initial data (roles and admin user) within a migration.
- 🗑️ **Soft-delete design pattern** — Implementing `IsDeleted` flags with cascading extension methods taught me how to preserve referential integrity and historical data while keeping the active dataset clean.
- ⚡ **AJAX in a server-rendered MVC app** — Integrating jQuery AJAX calls for dynamic dropdowns and rate auto-population within a Razor form showed me how to blend partial client-side interactivity into a traditional MVC architecture without a full SPA framework.
- 🔒 **ASP.NET Identity & OWIN** — Wiring up cookie-based authentication, role management, and a seeded admin account gave me practical experience with the full Identity pipeline in a .NET Framework application.

---

## Author

**Yashvi Khunt**  
MS Computer Science (Cybersecurity) @ Stevens Institute of Technology

[![GitHub](https://img.shields.io/badge/GitHub-yashvi--khunt-181717?logo=github)](https://github.com/yashvi-khunt)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-yashvi--khunt-0A66C2?logo=linkedin)](https://linkedin.com/in/yashvi-khunt)
