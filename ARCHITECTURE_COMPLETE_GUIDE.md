# 🚀 KBNT Microservices Architecture - Complete Setup Guide

## 📋 Overview

Esta é uma arquitetura de microserviços completa implementada com Spring Boot, Spring Cloud Gateway, Apache Kafka, PostgreSQL e Redis, seguindo os princípios de Clean Architecture e Hexagonal Architecture.

## 🏗️ Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              EXTERNAL LAYER                                    │
├─────────────────────────────────────────────────────────────────────────────────┤
│  📱 Postman/Frontend  ──HTTP──▶  🌐 API Gateway (8080)                        │
│                                      │                                          │
│                                      ▼                                          │
├─────────────────────────────────────────────────────────────────────────────────┤
│                             APPLICATION LAYER                                  │
├─────────────────────────────────────────────────────────────────────────────────┤
│  🎯 API Gateway (8080)                                                         │
│  ├── Route: /api/v1/virtual-stock/** ──▶ Virtual Stock Service (8084)         │
│  ├── Route: /api/v1/logs/**         ──▶ Log Producer Service (8081)           │
│  └── Route: /api/v1/kbnt-logs/**    ──▶ KBNT Log Service (8082)               │
│                                      │                                          │
│                                      ▼                                          │
├─────────────────────────────────────────────────────────────────────────────────┤
│                            MICROSERVICES LAYER                                 │
├─────────────────────────────────────────────────────────────────────────────────┤
│  🏪 Virtual Stock Service (8084)    📝 Log Producer Service (8081)            │
│  ├── REST APIs                      ├── Kafka Message Production               │
│  ├── Business Logic                 ├── Log Processing                         │
│  ├── Database Operations            └── Event Publishing                       │
│  └── Event Publishing                                                          │
│                                      │                                          │
│                                      ▼                                          │
├─────────────────────────────────────────────────────────────────────────────────┤
│                           INFRASTRUCTURE LAYER                                 │
├─────────────────────────────────────────────────────────────────────────────────┤
│  🗄️ PostgreSQL (5432)              📨 Apache Kafka (9092)                    │
│  ├── Primary Database               ├── Message Broker                         │
│  ├── ACID Transactions              ├── Event Streaming                        │
│  └── Data Persistence               └── Async Communication                    │
│                                                                                 │
│  ⚡ Redis (6379)                   🏗️ Zookeeper (2181)                       │
│  ├── Caching Layer                  ├── Kafka Coordination                     │
│  ├── Session Storage                └── Service Discovery                      │
│  └── Rate Limiting                                                             │
└─────────────────────────────────────────────────────────────────────────────────┘
```

## 🌍 Environment Configurations

### 🔧 Available Environments

| Environment | Profile | Description | Docker Compose File |
|-------------|---------|-------------|-------------------|
| **Local** | `local` | Development on host machine | `docker-compose-local.yml` |
| **Development** | `dev` | Containerized development | `docker-compose-dev.yml` |
| **Docker** | `docker` | Full containerized stack | `docker-compose-decoupled.yml` |
| **Homologation** | `hml` | Testing environment | `docker-compose-hml.yml` |
| **Production** | `prd` | Production environment | `docker-compose-prd.yml` |

### 🚀 Quick Start Commands

#### Local Development (Gateway + Infrastructure only)
```bash
cd microservices
docker-compose -f docker-compose-local.yml up -d
```

#### Development Environment (All services)
```bash
cd microservices
docker-compose -f docker-compose-dev.yml up -d
```

#### Current Running Environment (Decoupled Architecture)
```bash
cd microservices
docker-compose -f docker-compose-decoupled.yml up -d
```

## 📡 API Endpoints

### 🌐 API Gateway (Primary Access Point)
- **Base URL:** `http://localhost:8080`
- **Health Check:** `http://localhost:9080/actuator/health`
- **Gateway Routes:** `http://localhost:9080/actuator/gateway/routes`

### 🏪 Virtual Stock Service (via Gateway)
```http
GET    http://localhost:8080/api/v1/virtual-stock/stocks
POST   http://localhost:8080/api/v1/virtual-stock/stocks
GET    http://localhost:8080/api/v1/virtual-stock/stocks/{id}
PUT    http://localhost:8080/api/v1/virtual-stock/stocks/{id}
DELETE http://localhost:8080/api/v1/virtual-stock/stocks/{id}
```

### 📝 Log Producer Service (via Gateway)
```http
POST   http://localhost:8080/api/v1/logs/produce
GET    http://localhost:8080/api/v1/logs/status
```

### 📊 Direct Service Access (Development Only)
- **Virtual Stock:** `http://localhost:8084/api/v1/virtual-stock/stocks`
- **Log Producer:** `http://localhost:8081/api/v1/logs/produce`

## 🛠️ Service Ports

| Service | Application Port | Management Port | Description |
|---------|------------------|-----------------|-------------|
| **API Gateway** | 8080 | 9080 | Primary entry point |
| **Virtual Stock** | 8084 | 9084 | Stock management |
| **Log Producer** | 8081 | 9081 | Log processing |
| **PostgreSQL** | 5432 | - | Database |
| **Redis** | 6379 | - | Cache |
| **Kafka** | 9092 | - | Message broker |
| **Zookeeper** | 2181 | - | Coordination |

## 🔍 Health Checks

### Service Health Status
```bash
# API Gateway Health
curl http://localhost:9080/actuator/health

# Virtual Stock Service Health  
curl http://localhost:9084/actuator/health

# Log Producer Service Health
curl http://localhost:9081/actuator/health
```

### Gateway Routes Information
```bash
# List all configured routes
curl http://localhost:9080/actuator/gateway/routes

# Gateway metrics
curl http://localhost:9080/actuator/metrics
```

## 🔄 Architecture Patterns

### ✅ Implemented Patterns
- **Hexagonal Architecture** - Clean separation of concerns
- **Clean Architecture** - Domain-driven design
- **API Gateway Pattern** - Single entry point
- **Circuit Breaker** - Resilience and fault tolerance
- **Event-Driven Architecture** - Kafka-based messaging
- **Database per Service** - Data isolation
- **Health Check Pattern** - Service monitoring

### 🛡️ Resilience Features
- **Circuit Breakers** - Prevent cascade failures
- **Retry Logic** - Automatic retry on failures
- **Rate Limiting** - Protect against overload
- **Graceful Degradation** - Fallback responses
- **Health Monitoring** - Real-time status

## 📦 Docker Compose Profiles

### Environment-Specific Configurations

#### 🏠 Local (`docker-compose-local.yml`)
- Minimal infrastructure
- API Gateway + Database + Cache
- Ideal for frontend development

#### 🔧 Development (`docker-compose-dev.yml`) 
- Full microservices stack
- All services with debug logging
- Development-optimized settings

#### 🚢 Docker (`docker-compose-decoupled.yml`)
- Production-like environment
- Independent service startup
- No hard dependencies

## 🚀 Deployment Steps

### 1. Environment Setup
```bash
# Clone repository
git clone <repository-url>
cd estudosKBNT_Kafka_Logs_V2_cleanCode/microservices

# Choose environment and start
docker-compose -f docker-compose-[environment].yml up -d
```

### 2. Verify Deployment
```bash
# Check service status
docker-compose -f docker-compose-[environment].yml ps

# Verify API Gateway
curl http://localhost:8080/api/v1/virtual-stock/stocks
```

### 3. Postman Testing
- Import Postman collection (if available)
- Base URL: `http://localhost:8080`
- Test endpoints through API Gateway

## 🔧 Configuration Management

### Spring Profiles
Each service supports multiple profiles:
- `local` - Local development
- `dev` - Development environment  
- `docker` - Container environment
- `hml` - Homologation/testing
- `prd` - Production

### Environment Variables
Services are configured via environment variables in docker-compose files:
```yaml
environment:
  - SPRING_PROFILES_ACTIVE=docker
  - SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/loganalytics
  - SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka:9092
```

## 📈 Monitoring & Observability

### Available Endpoints
- `/actuator/health` - Health status
- `/actuator/metrics` - Application metrics
- `/actuator/info` - Application information
- `/actuator/gateway/routes` - Gateway routes (API Gateway only)

### Logging
- Structured logging with correlation IDs
- Environment-specific log levels
- Centralized log aggregation ready

## 🏆 Success Metrics

### ✅ Current Status
- ✅ API Gateway: Operational (8080)
- ✅ Virtual Stock Service: Operational (8084)  
- ✅ PostgreSQL: Operational (5432)
- ✅ Redis: Operational (6379)
- ✅ Kafka: Operational (9092)
- ✅ Zookeeper: Operational (2181)
- ✅ End-to-end API calls working
- ✅ Multi-environment support
- ✅ Zero-cost deployment ready

---

**🎯 Ready for Postman testing and production deployment!**
