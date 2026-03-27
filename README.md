# Cafeteria System — Centralized Configurations (Local Reference)

> **⚠️ Note**: This is a **local backup/reference copy** of configurations. The Config Server loads configurations from the **Git repository** at runtime.

**Primary Configuration Source (Production):**
📦 https://github.com/ChamathDilshanC/Cafeteria-System-Configurations

## 📌 Overview

The **Spring Cloud Config Server** (port 9000) loads all configurations from the Git repository above. This local directory serves as:

- A reference copy for offline development
- A fallback when Git repository is unavailable (native profile)
- Documentation of the configuration structure

## 🔄 How It Works

1. **Config Server starts** → Clones the Git repository
2. **Microservices start** → Contact Config Server at `http://localhost:9000`
3. **Config Server serves** → Delivers configuration from Git repo to services

## 📂 Git Repository Structure

```text
Cafeteria-System-Configurations/
├── application.yaml                  # Global config (Eureka cluster, logging)
├── platform/
│   ├── api-gateway.yaml              # Gateway routing, CORS, filters
│   ├── config-server.yaml            # Config Server settings
│   └── service-registry.yaml         # Eureka configuration
└── services/
    ├── kitchen-service.yaml          # MongoDB connections, kitchen operations
    ├── menu-service.yaml             # PostgreSQL + GCS image storage
    ├── order-service.yaml            # PostgreSQL + OpenFeign settings
    └── user-service.yaml             # PostgreSQL + JWT authentication
```

## 🗄️ Database Configuration

| Service         | Database   | Default DB Name   | Local Connection |
| --------------- | ---------- | ----------------- | ---------------- |
| user-service    | PostgreSQL | cafeteria_users   | localhost:5432   |
| menu-service    | PostgreSQL | cafeteria_menu    | localhost:5432   |
| order-service   | PostgreSQL | cafeteria_orders  | localhost:5432   |
| kitchen-service | MongoDB    | cafeteria_kitchen | localhost:27017  |

**Dev Credentials:**

- PostgreSQL: `postgres` / `postgres`
- MongoDB: `admin` / `adminpassword`

## 🚀 Updating Configurations

### For Production (Git Repository):

1. Clone the configuration repository:

   ```bash
   git clone https://github.com/ChamathDilshanC/Cafeteria-System-Configurations.git
   cd Cafeteria-System-Configurations
   ```

2. Modify the necessary `.yaml` file

3. Commit and push changes:

   ```bash
   git add .
   git commit -m "Update: [describe change]"
   git push origin main
   ```

4. Restart Config Server to fetch latest changes:

   ```bash
   pm2 restart config-server
   ```

5. Restart affected microservices or trigger refresh:

   ```bash
   # Option 1: Restart service
   pm2 restart user-service

   # Option 2: Dynamic refresh (if @RefreshScope is enabled)
   curl -X POST http://localhost:8081/actuator/refresh
   ```

### For Local Development (This Directory):

If using `native` profile (fallback mode), update files in this directory and restart Config Server.

## 🔧 Config Server Settings

**Git Mode (Primary):**

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/ChamathDilshanC/Cafeteria-System-Configurations.git
          clone-on-start: true
          search-paths: platform,services
```

**Native Mode (Fallback):**

```yaml
spring:
  cloud:
    config:
      server:
        native:
          search-locations: classpath:/configurations
```

## 📡 How Services Fetch Config

Each microservice's `application.yml` contains:

```yaml
spring:
  application:
    name: user-service # Service name matches config file
  config:
    import: optional:configserver:http://localhost:9000
```

At startup:

1. Service requests: `GET http://localhost:9000/user-service/default`
2. Config Server fetches `services/user-service.yaml` from Git
3. Service receives and applies configuration

## 🌍 Environment Variables

Services support environment variable overrides:

```bash
export CONFIG_SERVER_URI=http://config-server:9000
export EUREKA_URI=http://localhost:8761/eureka
export POSTGRES_HOST=localhost
export POSTGRES_PASSWORD=postgres
export MONGO_HOST=localhost
```

## 📚 Related Documentation

- [Config Server README](../../README.md)
- [Spring Cloud Config Docs](https://docs.spring.io/spring-cloud-config/docs/current/reference/html/)
- [Main Project README](../../../../../README.md)

---

**ITS 2130 — Enterprise Cloud Architecture | Final Project**
