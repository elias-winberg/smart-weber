# Smart Weber

**Author:** Elias Winberg

## Description

Smart Weber is a modern, multi-tenant real estate web platform built with Rails 8 and Ruby 3.4.7. It enables real estate agencies to quickly create and manage professional property listing websites with comprehensive features including property search, multi-language support, customizable themes, and powerful admin interfaces.

Key features include:

- **Multi-Tenancy** - Host multiple real estate websites from a single installation using subdomain-based tenant separation
- **Property Management** - Complete system for managing sale and rental listings with photos, features, and detailed property information
- **Admin Panels** - Dual admin interfaces: site admin (super admin) and tenant admin (per-tenant management)
- **Themes** - Customizable themes (Brisbane, Bologna, Bristol, and more) with Tailwind CSS and Liquid templates
- **Multi-Language Support** - Built-in support for multiple languages using the Mobility gem
- **Advanced Search** - Faceted search functionality with field filtering for properties
- **SEO Optimized** - Comprehensive SEO implementation for better search engine visibility
- **Responsive Design** - Mobile-friendly layouts that work across all devices
- **RESTful API** - Public API for property listings and website content
- **GraphQL Support** - Modern GraphQL API for flexible data queries

## Architecture

### Multi-Tenancy Architecture

Smart Weber uses subdomain-based multi-tenancy to host multiple real estate websites from a single installation:

```mermaid
graph TB
    subgraph "Single Application Instance"
        App[Smart Weber Application]
        DB[(PostgreSQL Database)]
    end
    
    subgraph "Tenant Websites"
        T1[tenant1.example.com<br/>Real Estate Agency A]
        T2[tenant2.example.com<br/>Real Estate Agency B]
        T3[tenant3.example.com<br/>Real Estate Agency C]
    end
    
    subgraph "Admin Interfaces"
        SA[Site Admin<br/>Super Admin Panel]
        TA1[Tenant Admin A]
        TA2[Tenant Admin B]
        TA3[Tenant Admin C]
    end
    
    T1 --> App
    T2 --> App
    T3 --> App
    App --> DB
    SA --> App
    TA1 --> App
    TA2 --> App
    TA3 --> App
    
    style App fill:#4A90E2,stroke:#2E5C8A,color:#fff
    style DB fill:#7B68EE,stroke:#5B4C99,color:#fff
    style SA fill:#FF6B6B,stroke:#CC5555,color:#fff
```

### System Components

```mermaid
graph LR
    subgraph "Frontend"
        Public[Public Website<br/>React/Vue.js]
        Admin[Admin Panel<br/>Vue.js 3 + Quasar]
        Themes[Themes<br/>Tailwind CSS + Liquid]
    end
    
    subgraph "Backend"
        Rails[Rails 8 API]
        GraphQL[GraphQL API]
        REST[REST API]
    end
    
    subgraph "Data Layer"
        Models[ActiveRecord Models]
        MultiTenant[Multi-Tenancy Layer]
        Storage[ActiveStorage<br/>S3/R2]
    end
    
    subgraph "Features"
        Props[Property Management]
        Search[Search Engine]
        Auth[Authentication]
        I18n[Multi-Language]
    end
    
    Public --> Rails
    Admin --> Rails
    Themes --> Rails
    Rails --> GraphQL
    Rails --> REST
    Rails --> Models
    Models --> MultiTenant
    Models --> Storage
    Rails --> Props
    Rails --> Search
    Rails --> Auth
    Rails --> I18n
    
    style Rails fill:#4A90E2,stroke:#2E5C8A,color:#fff
    style Admin fill:#FF6B6B,stroke:#CC5555,color:#fff
    style Models fill:#7B68EE,stroke:#5B4C99,color:#fff
```

### Property Management Flow

```mermaid
flowchart TD
    Start[Property Creation] --> Type{Property Type?}
    Type -->|Sale| SaleListing[Create Sale Listing]
    Type -->|Rental| RentalListing[Create Rental Listing]
    
    SaleListing --> RealtyAsset[Create Realty Asset]
    RentalListing --> RealtyAsset
    
    RealtyAsset --> Photos[Upload Photos]
    RealtyAsset --> Features[Add Features]
    RealtyAsset --> Location[Set Location]
    
    Photos --> Publish{Publish?}
    Features --> Publish
    Location --> Publish
    
    Publish -->|Yes| Listed[Appears in Public Listings]
    Publish -->|No| Draft[Saved as Draft]
    
    Listed --> Search[Searchable via<br/>Faceted Search]
    Listed --> API[Available via<br/>REST/GraphQL API]
    
    style RealtyAsset fill:#4A90E2,stroke:#2E5C8A,color:#fff
    style Listed fill:#50C878,stroke:#3D9D5F,color:#fff
    style Search fill:#FFD700,stroke:#CCAA00,color:#000
```

## Usage

### Prerequisites

- Ruby 3.4.7 or higher
- Rails 8.0 or higher
- PostgreSQL database
- Node.js and npm (for frontend assets)

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd property_web_builder
```

2. Install dependencies:
```bash
bundle install
npm install
```

3. Setup the database:
```bash
bin/rails db:create
bin/rails db:migrate
```

4. Seed the database with sample data:
```bash
bundle exec rake pwb:db:seed
```

Alternatively, use the setup script which does all of the above:
```bash
bin/setup
```

### Running the Application

Start the development server:
```bash
bin/dev
```

This will start both the Rails server and the Vite frontend build process. Access the application at `http://localhost:3000`.

### Creating a Website/Tenant

The application uses subdomain-based multi-tenancy. After seeding, you can access the default website or create new tenants through the admin interface.

### Admin Access

After seeding, an admin user is created with:
- Email: `admin@<subdomain>.com`
- Password: `password`

Access the admin panel at `/site_admin` after logging in.

### Database Seeding Options

To seed without sample properties:
```bash
SKIP_PROPERTIES=true bundle exec rake pwb:db:seed
```

For detailed development instructions, including setup, testing, and troubleshooting, please refer to [DEVELOPMENT.md](DEVELOPMENT.md).
