# 🏗️ CampusHub - System Architecture Documentation

<div align="center">

![CampusHub Architecture](https://img.shields.io/badge/CampusHub-Architecture-blue?style=for-the-badge&logo=blueprint)

[![Microservices](https://img.shields.io/badge/Architecture-Microservices-green?style=flat-square)](https://microservices.io/)
[![Event Driven](https://img.shields.io/badge/Pattern-Event%20Driven-orange?style=flat-square)](https://martinfowler.com/articles/201701-event-driven.html)
[![Cloud Ready](https://img.shields.io/badge/Deployment-Cloud%20Ready-blue?style=flat-square)](https://12factor.net/)

[🏗️ System Overview](#️-system-overview) | [🔄 Data Flow](#-data-flow) | [📊 Service Architecture](#-service-architecture)

</div>

## 📋 Table of Contents

- [🎯 Architecture Overview](#-architecture-overview)
- [🏗️ System Design](#️-system-design)
- [📦 Service Architecture](#-service-architecture)
- [🔄 Data Flow](#-data-flow)
- [🗄️ Database Architecture](#️-database-architecture)
- [🌐 API Architecture](#-api-architecture)
- [🔐 Security Architecture](#-security-architecture)
- [📡 Communication Patterns](#-communication-patterns)
- [⚡ Performance Architecture](#-performance-architecture)
- [🚀 Deployment Architecture](#-deployment-architecture)
- [📊 Monitoring & Observability](#-monitoring--observability)
- [🔄 Scalability Strategy](#-scalability-strategy)
- [💡 Design Decisions](#-design-decisions)

## 🎯 Architecture Overview

CampusHub follows a **modern microservices architecture** with event-driven communication, implementing industry best practices for scalability, maintainability, and security.

### 🌟 Architectural Principles

- **🔄 Microservices Pattern**: Independent, loosely coupled services
- **📡 Event-Driven Architecture**: Asynchronous communication for better performance
- **🛡️ Security by Design**: Multi-layer security implementation
- **🚀 Cloud-Native**: Container-ready with 12-factor app principles
- **📊 Data-Driven**: Analytics and insights at the core
- **⚡ Performance First**: Optimized for high throughput and low latency

### 🎭 Key Architectural Characteristics

| Characteristic | Implementation | Benefit |
|----------------|----------------|---------|
| **Scalability** | Horizontal scaling with load balancers | Handle 10x user growth |
| **Reliability** | Circuit breakers and health checks | 99.9% uptime |
| **Security** | JWT, OAuth2, RBAC | Enterprise-grade security |
| **Performance** | Redis caching, database optimization | Sub-200ms response times |
| **Maintainability** | Clean architecture, SOLID principles | 50% faster development |
| **Observability** | Structured logging, metrics, tracing | Real-time insights |

## 🏗️ System Design

### 🌐 High-Level Architecture
```mermaid
graph TB
    subgraph "Client Layer"
        A[React Web App<br/>Port: 5173]
        B[Mobile Apps<br/>iOS/Android]
        C[Admin Dashboard<br/>Management UI]
    end
    
    subgraph "Load Balancer Layer"
        D[Nginx Load Balancer<br/>Port: 80/443]
        E[SSL Termination<br/>HTTPS]
    end
    
    subgraph "API Gateway Layer"
        F[API Gateway<br/>Rate Limiting]
        G[Request Routing<br/>Path-based]
        H[CORS Handling<br/>Cross-origin]
    end
    
    subgraph "Microservices Layer"
        I[Auth Service<br/>Port: 8081]
        J[Main Backend<br/>Port: 8080]
        K[SocketIO Service<br/>Port: 9092]
    end
    
    subgraph "Data Layer"
        L[MySQL Primary<br/>Port: 3306]
        M[Redis Cache<br/>Port: 6379]
        N[File Storage<br/>Static Assets]
    end
    
    subgraph "External Services"
        O[Email Service<br/>SMTP]
        P[OAuth2 Providers<br/>Google, Microsoft]
        Q[Monitoring<br/>Health Checks]
    end
    
    A --> D
    B --> D
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
    H --> J
    H --> K
    I --> L
    J --> L
    I --> M
    J --> M
    K --> M
    J --> N
    I --> O
    I --> P
    J --> Q
    I --> Q
```

### 🔄 Request Processing Flow
```mermaid
sequenceDiagram
    participant C as Client
    participant N as Nginx
    participant G as API Gateway
    participant A as Auth Service
    participant B as Backend
    participant D as Database
    participant R as Redis
    participant S as SocketIO

    C->>N: HTTPS Request
    N->>G: Forward Request
    G->>G: Rate Limiting Check
    G->>A: Authentication
    A->>R: Validate Session
    A-->>G: Auth Response
    G->>B: Authorized Request
    B->>R: Check Cache
    alt Cache Miss
        B->>D: Database Query
        D-->>B: Data
        B->>R: Update Cache
    else Cache Hit
        R-->>B: Cached Data
    end
    B->>S: Real-time Event
    S-->>C: WebSocket Update
    B-->>G: API Response
    G-->>N: Response
    N-->>C: HTTPS Response
```

## 📦 Service Architecture

### 🔐 Authentication Service (`auth/`)
```mermaid
graph TD
    subgraph "Auth Service Components"
        A[Auth Controller<br/>REST API]
        B[JWT Provider<br/>Token Management]
        C[OAuth2 Handler<br/>Social Login]
        D[Email Service<br/>Verification]
        E[Security Filter<br/>Request Validation]
    end
    
    subgraph "Data Storage"
        F[(Auth Database<br/>Users, Tokens)]
        G[(Redis Session<br/>Active Sessions)]
    end
    
    A --> B
    A --> C
    A --> D
    E --> A
    B --> F
    B --> G
    C --> F
    D --> F
```

**Responsibilities:**
- 🔐 User authentication and authorization
- 🎫 JWT token generation and validation
- 📧 Email verification and password reset
- 🌐 OAuth2 social login integration
- 🛡️ Session management and security

### 🚀 Main Backend Service (`smarte-vent-backend/`)
```mermaid
graph TD
    subgraph "Backend Service Components"
        A[Activity Controller<br/>CRUD Operations]
        B[Participant Controller<br/>Registration Management]
        C[Analytics Controller<br/>Data Insights]
        D[Notification Service<br/>Real-time Updates]
        E[Email Service<br/>Communications]
    end
    
    subgraph "Business Logic"
        F[Activity Service<br/>Business Rules]
        G[Participant Service<br/>Workflow Management]
        H[Analytics Service<br/>Data Processing]
        I[Statistics Service<br/>Reporting]
    end
    
    subgraph "Data Layer"
        J[(Main Database<br/>Activities, Participants)]
        K[(Redis Cache<br/>Performance)]
        L[File Storage<br/>Attachments]
    end
    
    A --> F
    B --> G
    C --> H
    C --> I
    F --> J
    G --> J
    H --> J
    I --> J
    F --> K
    G --> K
    H --> K
    A --> L
```

**Responsibilities:**
- 🎯 Activity lifecycle management
- 👥 Participant registration and approval
- 📊 Analytics and reporting
- 🔔 Real-time notifications
- 📧 Email communications

### 🌐 Frontend Application (`matcha-web-client/`)
```mermaid
graph TD
    subgraph "Frontend Architecture"
        A[React Components<br/>UI Layer]
        B[Custom Hooks<br/>State Logic]
        C[Redux Store<br/>Global State]
        D[Service Layer<br/>API Calls]
        E[Router<br/>Navigation]
    end
    
    subgraph "Role-Based Layouts"
        F[Admin Layout<br/>Management UI]
        G[Organization Layout<br/>Activity Management]
        H[Student Layout<br/>Participation UI]
    end
    
    subgraph "External Integration"
        I[Backend APIs<br/>REST Calls]
        J[SocketIO Client<br/>Real-time]
        K[Chart Libraries<br/>Visualization]
    end
    
    A --> B
    B --> C
    A --> D
    A --> E
    E --> F
    E --> G
    E --> H
    D --> I
    A --> J
    A --> K
```

**Responsibilities:**
- 🎨 User interface and experience
- 🎭 Role-based navigation and features
- 📊 Data visualization and analytics
- ⚡ Real-time updates and notifications
- 📱 Responsive design

## 🔄 Data Flow

### 📊 Activity Creation Flow
```mermaid
sequenceDiagram
    participant U as User (Organization)
    participant F as Frontend
    participant A as Auth Service
    participant B as Backend
    participant D as Database
    participant S as SocketIO
    participant E as Email Service

    U->>F: Create Activity
    F->>A: Validate Token
    A-->>F: Token Valid
    F->>B: POST /api/activities
    B->>B: Validate Data
    B->>D: Save Activity
    D-->>B: Activity Created
    B->>S: Emit 'activity:created'
    S-->>F: Real-time Update
    B->>E: Send Notification Emails
    B-->>F: Success Response
    F-->>U: Activity Created
```

### 👥 Participant Registration Flow
```mermaid
sequenceDiagram
    participant S as Student
    participant F as Frontend
    participant B as Backend
    participant D as Database
    participant N as Notification
    participant O as Organization

    S->>F: Register for Activity
    F->>B: POST /api/activities/{id}/participants
    B->>D: Check Availability
    D-->>B: Slots Available
    B->>D: Create Participant Record
    D-->>B: Registration Saved
    B->>N: Send Confirmation Email
    B->>N: Notify Organization
    N-->>O: New Registration Alert
    B-->>F: Registration Success
    F-->>S: Confirmation Message
```

### 📈 Analytics Data Flow
```mermaid
sequenceDiagram
    participant A as Admin/Org
    participant F as Frontend
    participant B as Backend
    participant R as Redis
    participant D as Database
    participant C as Chart Component

    A->>F: Request Analytics
    F->>B: GET /api/analytics
    B->>R: Check Cache
    alt Cache Hit
        R-->>B: Cached Analytics
    else Cache Miss
        B->>D: Complex Queries
        D-->>B: Raw Data
        B->>B: Process & Aggregate
        B->>R: Cache Results
    end
    B-->>F: Analytics Data
    F->>C: Render Charts
    C-->>A: Visual Analytics
```

## 🗄️ Database Architecture

### 📊 Entity Relationship Diagram
```mermaid
erDiagram
    User ||--o{ Activity : creates
    User ||--o{ Participant : registers
    User }o--|| Organization : belongs_to
    Activity ||--o{ Participant : has
    Activity }o--|| Category : belongs_to
    Activity }o--|| Organization : organized_by
    Organization ||--o{ OrganizationStatistic : has
    Activity ||--o{ ActivityStatistic : generates
    Participant ||--o{ Feedback : provides
    User ||--o{ Notification : receives
    
    User {
        bigint id PK
        string email UK
        string password_hash
        string full_name
        enum user_role
        bigint organization_id FK
        boolean email_verified
        datetime created_at
        datetime updated_at
        string provider
        string provider_id
    }
    
    Organization {
        bigint id PK
        string name UK
        text description
        string email
        string phone
        text address
        string website
        enum status
        json settings
        datetime created_at
        datetime updated_at
    }
    
    Activity {
        bigint id PK
        string title
        text description
        enum category
        enum status
        datetime start_date
        datetime end_date
        integer max_participants
        json location
        json requirements
        bigint creator_id FK
        bigint organization_id FK
        decimal rating_average
        integer rating_count
        datetime created_at
        datetime updated_at
    }
    
    Participant {
        bigint id PK
        bigint user_id FK
        bigint activity_id FK
        enum status
        text motivation
        enum experience_level
        text additional_info
        datetime registered_at
        datetime approved_at
        datetime attended_at
    }
    
    ActivityStatistic {
        bigint id PK
        bigint activity_id FK
        date statistic_date
        integer total_registrations
        integer approved_participants
        integer attended_participants
        decimal average_rating
        json category_metrics
    }
```

### 🏗️ Database Design Principles

#### 📊 Normalization Strategy
- **Third Normal Form (3NF)**: Eliminates data redundancy
- **Selective Denormalization**: For performance-critical queries
- **JSON Columns**: For flexible schema requirements
- **Indexes**: Optimized for query patterns

#### 🔄 Data Consistency
- **ACID Transactions**: Ensures data integrity
- **Foreign Key Constraints**: Maintains referential integrity
- **Unique Constraints**: Prevents duplicate data
- **Check Constraints**: Validates data quality

#### ⚡ Performance Optimization
```sql
-- Optimized indexes for common queries
CREATE INDEX idx_activity_organization_status ON activities(organization_id, status);
CREATE INDEX idx_participant_activity_status ON participants(activity_id, status);
CREATE INDEX idx_user_email_verified ON users(email, email_verified);
CREATE INDEX idx_activity_dates ON activities(start_date, end_date);

-- Composite indexes for analytics queries
CREATE INDEX idx_activity_stats_date_org ON activity_statistics(statistic_date, organization_id);
CREATE INDEX idx_participant_registered_approved ON participants(registered_at, approved_at);
```

## 🌐 API Architecture

### 🔌 RESTful API Design
```yaml
# API Structure Overview
/api/auth/              # Authentication endpoints
├── POST /login         # User authentication
├── POST /register      # User registration
├── POST /refresh       # Token refresh
└── POST /logout        # User logout

/api/activities/        # Activity management
├── GET /               # List activities (paginated, filtered)
├── POST /              # Create new activity
├── GET /{id}           # Get activity details
├── PUT /{id}           # Update activity
└── DELETE /{id}        # Delete activity

/api/participants/      # Participant management
├── GET /               # List participants
├── POST /register      # Register for activity
├── PUT /{id}/status    # Update participation status
└── GET /{id}/history   # Participation history

/api/analytics/         # Analytics and reporting
├── GET /activities     # Activity statistics
├── GET /organizations  # Organization metrics
├── GET /participants   # Participation data
└── GET /trends         # Trend analysis

/api/notifications/     # Notification system
├── GET /               # List notifications
├── PUT /{id}/read      # Mark as read
└── DELETE /{id}        # Delete notification
```

### 📊 API Response Standards
```json
{
  "success": true,
  "data": {
    "content": [...],
    "pagination": {
      "page": 0,
      "size": 20,
      "totalElements": 150,
      "totalPages": 8
    }
  },
  "message": "Request successful",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### 🚨 Error Response Format
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request data",
    "details": [
      {
        "field": "email",
        "message": "Email format is invalid"
      }
    ]
  },
  "timestamp": "2024-01-15T10:30:00Z",
  "path": "/api/auth/register"
}
```

## 🔐 Security Architecture

### 🛡️ Multi-Layer Security Model
```mermaid
graph TD
    subgraph "External Security"
        A[WAF<br/>Web Application Firewall]
        B[DDoS Protection<br/>Rate Limiting]
        C[SSL/TLS<br/>HTTPS Encryption]
    end
    
    subgraph "Application Security"
        D[JWT Authentication<br/>Token-based]
        E[RBAC Authorization<br/>Role-based Access]
        F[Input Validation<br/>Data Sanitization]
        G[CORS Policy<br/>Cross-origin Control]
    end
    
    subgraph "Data Security"
        H[Database Encryption<br/>At Rest]
        I[Password Hashing<br/>BCrypt]
        J[Sensitive Data Masking<br/>PII Protection]
    end
    
    subgraph "Infrastructure Security"
        K[Container Security<br/>Docker]
        L[Network Isolation<br/>VPC]
        M[Secrets Management<br/>Environment Variables]
    end
    
    A --> D
    B --> D
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
    I --> J
    J --> K
    K --> L
    L --> M
```

### 🔑 Authentication & Authorization Flow
```mermaid
sequenceDiagram
    participant C as Client
    participant G as Gateway
    participant A as Auth Service
    participant B as Backend
    participant D as Database

    C->>G: Request with JWT
    G->>G: Rate Limit Check
    G->>A: Validate Token
    A->>A: Verify Signature
    A->>A: Check Expiration
    A->>A: Extract Claims
    A-->>G: Token Valid + User Info
    G->>G: Check Permissions
    G->>B: Authorized Request
    B->>D: Execute Operation
    D-->>B: Result
    B-->>G: Response
    G-->>C: Final Response
```

## 📡 Communication Patterns

### 🔄 Synchronous Communication (REST)
```typescript
// API Service Pattern
class ApiService {
  async makeRequest<T>(config: RequestConfig): Promise<T> {
    // Request interceptor
    const request = this.addAuthHeader(config);
    
    try {
      const response = await axios(request);
      return response.data;
    } catch (error) {
      // Error handling
      throw this.handleApiError(error);
    }
  }
}
```

### ⚡ Asynchronous Communication (WebSocket)
```typescript
// Real-time Event System
class SocketService {
  connect(token: string) {
    this.socket = io(SOCKET_URL, {
      auth: { token }
    });
    
    this.socket.on('activity:created', this.handleActivityCreated);
    this.socket.on('participant:approved', this.handleParticipantApproved);
    this.socket.on('notification:new', this.handleNewNotification);
  }
  
  private handleActivityCreated = (data: ActivityEvent) => {
    // Update UI in real-time
    eventBus.emit('UI_UPDATE', { type: 'ACTIVITY_CREATED', data });
  };
}
```

### 📧 Event-Driven Communication
```java
// Spring Event System
@EventListener
@Async
public void handleActivityCreated(ActivityCreatedEvent event) {
    // Send email notifications
    emailService.sendActivityNotification(event.getActivity());
    
    // Update statistics
    statisticsService.updateActivityStats(event.getOrganizationId());
    
    // Real-time notification
    socketIOService.broadcastActivityCreated(event);
}
```

## ⚡ Performance Architecture

### 🚀 Caching Strategy
```mermaid
graph TD
    subgraph "Caching Layers"
        A[Browser Cache<br/>Static Assets]
        B[CDN Cache<br/>Global Distribution]
        C[Application Cache<br/>Redis]
        D[Database Cache<br/>Query Results]
    end
    
    subgraph "Cache Patterns"
        E[Cache-Aside<br/>Lazy Loading]
        F[Write-Through<br/>Immediate Update]
        G[Write-Behind<br/>Batch Updates]
    end
    
    A --> B
    B --> C
    C --> D
    C --> E
    C --> F
    C --> G
```

### 📊 Performance Metrics & Targets
| Metric | Target | Monitoring |
|--------|--------|------------|
| **API Response Time** | < 200ms | ✅ Prometheus |
| **Database Query Time** | < 50ms | ✅ Slow Query Log |
| **Cache Hit Rate** | > 80% | ✅ Redis Metrics |
| **Page Load Time** | < 2s | ✅ Lighthouse |
| **WebSocket Latency** | < 100ms | ✅ Custom Metrics |
| **Throughput** | 1000 RPS | ✅ Load Testing |

### 🔧 Performance Optimization Techniques
```java
// Database Query Optimization
@Query("""
    SELECT DISTINCT a FROM Activity a
    JOIN FETCH a.organization o
    LEFT JOIN FETCH a.participants p
    WHERE a.status = :status
    AND a.startDate BETWEEN :startDate AND :endDate
""")
List<Activity> findActivitiesOptimized(
    @Param("status") ActivityStatus status,
    @Param("startDate") LocalDateTime startDate,
    @Param("endDate") LocalDateTime endDate
);

// Caching Implementation
@Cacheable(value = "activities", key = "#organizationId + '_' + #status")
public List<ActivityDTO> getActivitiesByOrganization(
    Long organizationId, ActivityStatus status) {
    return activityRepository.findByOrganizationIdAndStatus(organizationId, status)
        .stream()
        .map(activityMapper::toDTO)
        .toList();
}
```

## 🚀 Deployment Architecture

### 🐳 Containerization Strategy
```yaml
# Docker Compose Production Setup
version: '3.8'
services:
  # Application Services
  auth-service:
    image: campushub/auth-service:latest
    replicas: 2
    resources:
      limits:
        memory: 512M
        cpus: '0.5'
    
  backend-service:
    image: campushub/backend-service:latest
    replicas: 3
    resources:
      limits:
        memory: 1G
        cpus: '1.0'
  
  frontend:
    image: campushub/frontend:latest
    replicas: 2
    
  # Infrastructure Services
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    
  mysql:
    image: mysql:8.0
    volumes:
      - mysql-data:/var/lib/mysql
    
  redis:
    image: redis:7-alpine
    volumes:
      - redis-data:/data
```

### ☁️ Cloud Deployment Options
```mermaid
graph TD
    subgraph "Cloud Providers"
        A[AWS ECS/EKS<br/>Container Orchestration]
        B[Google Cloud Run<br/>Serverless Containers]
        C[Azure Container Instances<br/>Managed Containers]
    end
    
    subgraph "Database Services"
        D[AWS RDS<br/>Managed MySQL]
        E[Google Cloud SQL<br/>Managed Database]
        F[Azure Database<br/>MySQL Service]
    end
    
    subgraph "Caching Services"
        G[AWS ElastiCache<br/>Redis Service]
        H[Google Memorystore<br/>Redis Service]
        I[Azure Cache<br/>Redis Service]
    end
    
    A --> D
    A --> G
    B --> E
    B --> H
    C --> F
    C --> I
```

## 📊 Monitoring & Observability

### 📈 Observability Stack
```mermaid
graph TD
    subgraph "Metrics Collection"
        A[Prometheus<br/>Metrics Server]
        B[Grafana<br/>Visualization]
        C[Custom Metrics<br/>Business KPIs]
    end
    
    subgraph "Logging System"
        D[Structured Logging<br/>JSON Format]
        E[Log Aggregation<br/>ELK Stack]
        F[Log Analysis<br/>Search & Alerts]
    end
    
    subgraph "Distributed Tracing"
        G[Request Tracing<br/>Jaeger/Zipkin]
        H[Performance Analysis<br/>APM Tools]
        I[Error Tracking<br/>Sentry]
    end
    
    subgraph "Health Monitoring"
        J[Health Checks<br/>Spring Actuator]
        K[Uptime Monitoring<br/>External Services]
        L[Alert Management<br/>PagerDuty]
    end
    
    A --> B
    B --> C
    D --> E
    E --> F
    G --> H
    H --> I
    J --> K
    K --> L
```

### 🚨 Alert Configuration
```yaml
# Prometheus Alert Rules
groups:
  - name: campushub-alerts
    rules:
      - alert: HighResponseTime
        expr: http_request_duration_seconds{quantile="0.95"} > 0.5
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High response time detected"
      
      - alert: DatabaseConnectionFailure
        expr: up{job="mysql"} == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Database connection failed"
      
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.1
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
```

## 🔄 Scalability Strategy

### 📈 Horizontal Scaling Plan
```mermaid
graph TD
    subgraph "Load Balancing"
        A[Application Load Balancer<br/>L7 Routing]
        B[Database Load Balancer<br/>Read Replicas]
        C[Cache Cluster<br/>Redis Sentinel]
    end
    
    subgraph "Auto Scaling"
        D[Container Orchestration<br/>Kubernetes]
        E[CPU/Memory Based<br/>HPA]
        F[Custom Metrics<br/>Request Rate]
    end
    
    subgraph "Data Scaling"
        G[Database Sharding<br/>Horizontal Partitioning]
        H[Read Replicas<br/>Query Distribution]
        I[Archive Strategy<br/>Cold Storage]
    end
    
    A --> D
    B --> H
    C --> E
    D --> E
    E --> F
    G --> H
    H --> I
```

### 📊 Scaling Metrics & Triggers
| Resource | Metric | Scale Up Trigger | Scale Down Trigger |
|----------|--------|------------------|-------------------|
| **Web Servers** | CPU Usage | > 70% | < 30% |
| **API Servers** | Request Rate | > 1000 RPS | < 300 RPS |
| **Database** | Connection Pool | > 80% | < 40% |
| **Cache** | Memory Usage | > 85% | < 50% |
| **Storage** | Disk Usage | > 80% | N/A |

## 💡 Design Decisions

### 🎯 Technology Choices

#### Frontend: React + TypeScript
**Decision**: React with TypeScript for the frontend
**Rationale**: 
- ✅ Strong ecosystem and community support
- ✅ Excellent TypeScript integration
- ✅ Component reusability and maintainability
- ✅ Rich library ecosystem for charts and UI components

#### Backend: Spring Boot + Java 21
**Decision**: Spring Boot with Java 21
**Rationale**:
- ✅ Enterprise-grade framework with proven reliability
- ✅ Excellent ecosystem for microservices
- ✅ Strong security features out of the box
- ✅ Easy integration with databases and external services

#### Database: MySQL + Redis
**Decision**: MySQL for primary storage, Redis for caching
**Rationale**:
- ✅ MySQL: ACID compliance, mature ecosystem, good performance
- ✅ Redis: High-performance caching, session storage, pub/sub

#### Communication: REST + WebSocket
**Decision**: REST APIs for request/response, WebSocket for real-time
**Rationale**:
- ✅ REST: Standard, cacheable, stateless
- ✅ WebSocket: Real-time updates, bi-directional communication

### 🏗️ Architectural Patterns

#### Microservices Architecture
**Decision**: Separate authentication service from main backend
**Rationale**:
- ✅ Independent scaling and deployment
- ✅ Technology diversity (can use different tech stacks)
- ✅ Fault isolation (auth failure doesn't break entire system)
- ✅ Team autonomy (separate teams can work independently)

#### Event-Driven Communication
**Decision**: Asynchronous events for non-critical operations
**Rationale**:
- ✅ Better performance (non-blocking operations)
- ✅ Improved resilience (temporary service unavailability)
- ✅ Scalability (handles traffic spikes better)

#### Caching Strategy
**Decision**: Multi-layer caching with Redis
**Rationale**:
- ✅ Reduces database load
- ✅ Improves response times
- ✅ Better user experience
- ✅ Cost optimization (fewer database resources needed)

### 🔐 Security Decisions

#### JWT Tokens
**Decision**: JWT for stateless authentication
**Rationale**:
- ✅ Stateless (no server-side session storage needed)
- ✅ Scalable (works well with load balancers)
- ✅ Standard (widely supported)
- ✅ Secure (when implemented correctly)

#### Role-Based Access Control
**Decision**: RBAC with fine-grained permissions
**Rationale**:
- ✅ Flexible permission model
- ✅ Easy to understand and implement
- ✅ Scalable (new roles can be added easily)
- ✅ Audit-friendly (clear permission trails)

---

<div align="center">

**🏗️ CampusHub System Architecture - Designed for Scale & Performance**

[🏠 Main Project](./README.md) | [🔐 Auth Service](./auth/README.md) | [🚀 Backend](./smarte-vent-backend/README.md) | [🌐 Frontend](./matcha-web-client/README.md)

[📧 Architecture Questions](mailto:architecture@campushub.dev) | [🐛 Issues](https://github.com/activity-group3/fe-full/issues) | [💡 Improvements](https://github.com/activity-group3/fe-full/discussions)

</div>
