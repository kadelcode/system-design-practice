# System Design: [System Name]

---

## Problem Statement

Design a system that...

---

## 1. Scale Estimation

### Assumptions

- Daily active users:
- Request per second:
- Read/Write ratio

### Back-of-the-envelope Calculations

- Reads/sec:
- Writes/sec:
- Storage/year:

---

## 2. Functional Requirements

- 
- 
- 

---

## 3. Non-Functional Requirements

- Scalability
- Availability
- Reliability
- Low latency
- Consistency (if applicable)

---

## 4. High-Level Architecture

> Add diagram (diagram.png)

### Components

- Client
- Load Balancer
- Application Servers
- Cache (Redis)
- Database
- Message Queue (Kafka)

---

## 5. Data Flow (VERY IMPORTANT)

### Write Flow

Step-by-step:
1. Client -> API
2. Validation
3. DB write
4. Cache update

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

## 8. Caching Strategy

- Cache-aside / write-through
- TTL strategy
- Cache keys

---

## 9. Scaling Strategy

### Horizontal scaling
- Stateless services

### Database Scaling
- Read replicas
- Sharding strategy

### Other
- CDN
- Async processing (queues)

---

## 10. Bottlenecks & Failure Handling

### Potential Bottlenecks
- 
- 

### Failure Scenarios
| Failure                  | Impact                  | Mitigation            |
|--------------------------|-------------------------|-----------------------|
| Cache down               | Slow reads              | Fallback to DB        |
| Db down                  | System halt             | Replication           |

---

## 11. Security Considerations
- Authentication / Authorization
- Rate limiting
- Input validation

---

## 12. Tradeoffs
| Decision              | Why               | Downsides             |
|-----------------------|-------------------|-----------------------|
|                       |                   |                       |

---

## 13. Improvements

- 
- 
- 

## 14. Summary

- Key insights
- What you'd improve in V2