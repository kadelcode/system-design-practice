<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [System Design: [System Name]](#system-design-system-name)
  - [Problem Statement](#problem-statement)
  - [1. Functional Requirements](#1-functional-requirements)
  - [2. Non-Functional Requirements](#2-non-functional-requirements)
  - [3. Scale Estimation](#3-scale-estimation)
    - [Assumptions](#assumptions)
    - [Traffic Estimation](#traffic-estimation)
    - [QPS Calculation](#qps-calculation)
    - [Read/Write Ratio](#readwrite-ratio)
    - [Storage Estimation](#storage-estimation)
    - [Peak Traffic](#peak-traffic)
    - [Key Observations](#key-observations)
  - [4. High-Level Architecture](#4-high-level-architecture)
    - [Components](#components)
  - [5. Data Flow (VERY IMPORTANT)](#5-data-flow-very-important)
    - [Write Flow](#write-flow)
    - [Read Flow](#read-flow)
  - [6. API Design](#6-api-design)
    - [Create Resource](#create-resource)
      - [POST /api/v1/resource](#post-apiv1resource)
      - [Get Resource](#get-resource)
  - [7. Database Design](#7-database-design)
    - [Indexing Strategy](#indexing-strategy)
  - [8. Scaling & Performance Strategy](#8-scaling--performance-strategy)
    - [Caching](#caching)
    - [Horizontal Scaling](#horizontal-scaling)
    - [Database Scaling](#database-scaling)
    - [Other](#other)
  - [9. Bottlenecks & Failure Handling](#9-bottlenecks--failure-handling)
    - [Potential Bottlenecks](#potential-bottlenecks)
    - [Failure Scenarios](#failure-scenarios)
  - [10. Security Considerations](#10-security-considerations)
  - [11. Tradeoffs](#11-tradeoffs)
  - [12. Improvements](#12-improvements)
  - [13. Summary](#13-summary)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# System Design: [System Name]

---

## Problem Statement

Design a system that...

---

## 1. Functional Requirements

- 
- 
- 

---

## 2. Non-Functional Requirements

- Scalability
- Availability
- Reliability
- Low latency
- Consistency (if applicable)
- Durability
- Security
- Observability

---

## 3. Scale Estimation

### Assumptions

- Daily active users:
- Actions per user per day:
- Average data size per record:
- Data retention period:

---

### Traffic Estimation

- Writes/day:
- Reads/day:

---

### QPS Calculation

- Write QPS:
- Read QPS:

---

### Read/Write Ratio

- e.g., 10:1 (read-heavy)

---

### Storage Estimation

- Daily storage:
- Yearly storage:
- Total storage (e.g., 5 years):

---

### Peak Traffic

- Peak QPS (e.g., 5x average):

---

### Key Observations

- (e.g., Read-heavy -> caching required)
- (e.g., High storage growth -> scalable DB)
---

## 4. High-Level Architecture

> Add diagram (diagram.png)

### Components

- Client
- Load Balancer
- Application Servers
- Cache (e.g., Redis, Memcached)
- Database
- Message Queue (e.g., Kafka, RabbitMQ)

---

## 5. Data Flow (VERY IMPORTANT)

### Write Flow

Step-by-step:
1. Client -> API
2. Validation
3. DB write
4. Cache update / invalidation

---

### Read Flow

Step-by-step:
1. Client -> CDN
2. Cache lookup
3. DB fallback

---

## 6. API Design

### Create Resource

#### POST /api/v1/resource
**Request:**
```json
{}
```

**Response:**
```json
{}
```

#### Get Resource
```
GET /api/v1/resource/{id}
```

---

## 7. Database Design

Example Table
| Field          | Type          | Notes                 |
|----------------|---------------|-----------------------|
| id             | string        | Primary Key           |
| createdAt      | datetime      |                       |

### Indexing Strategy
- Which fields are indexed and why


---

## 8. Scaling & Performance Strategy


### Caching
- Caching patterns (e.g., cache-aside, read-through, write-through, write-back, write-around)
- TTL strategy
- Cache keys

### Horizontal Scaling
- Stateless services

### Database Scaling
- Replicas, sharding

### Other
- CDN
- Async processing

---

## 9. Bottlenecks & Failure Handling

### Potential Bottlenecks
- 
- 

### Failure Scenarios
| Failure                  | Impact                  | Mitigation            |
|--------------------------|-------------------------|-----------------------|
| Cache down               | Slow reads              | Fallback to DB        |
| DB down (master)         | Write operations fail   | Failover to replica   |

---

## 10. Security Considerations
- Authentication / Authorization
- Rate limiting
- Input validation
- Data encryption (at rest & in transit)
- Secrets management
- Protection against common vulnerabilities (e.g., OWASP Top 10)

---

## 11. Tradeoffs
| Decision              | Why               | Downsides             |
|-----------------------|-------------------|-----------------------|
|                       |                   |                       |

---

## 12. Improvements

- 
- 
- 

## 13. Summary

- Key insights
- What you'd improve in V2