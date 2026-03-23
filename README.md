# Cafeteria System — Centralized Configurations

This repository contains the centralized configuration files for the **Cafeteria System** microservices architecture. It is designed to be served by the **Spring Cloud Config Server**.

## 📌 Overview
Instead of hardcoding database URLs, Eureka ports, and other properties inside each microservice's source code, all services fetch their configurations dynamically from this repository at startup.

If any configuration changes (e.g., database password update), you only need to update the file here and restart the respective service.

## 📂 Directory Structure
```text
configurations/
├── application.yaml                  # Global fallback config (Eureka URLs)
├── application-dev.yaml              # Global default config for Local/Dev
├── platform/
│   ├── api-gateway.yaml              # Gateway routing and CORS config
│   ├── service-registry.yaml         # Eureka registry production config
│   └── service-registry-dev.yaml     # Eureka registry local config
└── services/
    ├── kitchen-service.yaml          # MongoDB production config
    ├── kitchen-service-dev.yaml      # MongoDB local config
    ├── menu-service.yaml             # PostgreSQL & GCP storage production config
    ├── menu-service-dev.yaml         # PostgreSQL & GCP storage local config
    ├── order-service.yaml            # PostgreSQL production config
    ├── order-service-dev.yaml        # PostgreSQL local config
    ├── user-service.yaml             # PostgreSQL production config
    └── user-service-dev.yaml         # PostgreSQL local config
```

## ⚙️ Profiles (Environments)
Every service has two configuration files based on the active Spring profile:

1. **Production (Default Profile)**  
   - Files like `menu-service.yaml`
   - Configured for Docker/Kubernetes networking.
   - Uses `vm-node-a.platform`, `config.platform` hostnames for service discovery and db connections.

2. **Development (`dev` Profile)**  
   - Files like `menu-service-dev.yaml`
   - Configured for local development.
   - Uses `localhost` for tracing, Eureka, and PostgreSQL/MongoDB connections.
   - Run services with `-Dspring-boot.run.profiles=dev` to activate this.

## 🗄️ Database Mapping
- **Kitchen Service**: Uses MongoDB (`cafeteria-mongodb:27017`)
- **Menu, Order, User Services**: Use PostgreSQL 16 (`cafeteria-postgresql:5432`)
- **Credentials (Dev)**: Postgres (`postgres/postgres`), Mongo (`admin/adminpassword`)

## 🚀 How to Apply Updates
1. Modify the necessary `.yaml` file.
2. Commit and push the changes to your Git repository:
   ```bash
   git add .
   git commit -m "Updated configurations"
   git push origin main
   ```
3. Restart the respective microservice to fetch the latest properties.
