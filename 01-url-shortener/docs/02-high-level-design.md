<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [URL Shortener - High Level Design](#url-shortener---high-level-design)
  - [Goal](#goal)
  - [Core Components](#core-components)
    - [1. Load Balancer (LB)](#1-load-balancer-lb)
    - [2. Application Servers (App Layer)](#2-application-servers-app-layer)
    - [3. Cache (Redis)](#3-cache-redis)
    - [4. Database (Persistent Storage)](#4-database-persistent-storage)
    - [5. CDN (Optional but Recommended)](#5-cdn-optional-but-recommended)
    - [6. ID Generator Service](#6-id-generator-service)
  - [High-Level Data Flow](#high-level-data-flow)
    - [1. URL Shortening Flow (Write Path)](#1-url-shortening-flow-write-path)
    - [2. URL Redirection Flow (Read Path)](#2-url-redirection-flow-read-path)
  - [Architecture Diagram](#architecture-diagram)
  - [Design Decisions Summary](#design-decisions-summary)
  - [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# URL Shortener - High Level Design

## Goal

Define the main components of the system and how they interact to handle URL shortening and redirection at scale

---

## Core Components

### 1. Load Balancer (LB)
**Role:**
- Distributes incoming traffic across multiple application servers
- Ensures high availability and fault tolerance
- Prevents any single server from being overwhelmed

**Why needed:**
- Handles peak traffic (~580 read QPS)
- Enables horizontal scaling of application layer

---

### 2. Application Servers (App Layer)
**Role:**
- Handle business logic
- Generate short URLs
- Process redirection requests

**Responsibilities:**
- Validate incoming URLs
- Generate unique short codes
- Store mappings (short -> original URL)
- Query cache/DB for redirection

---

### 3. Cache (Redis)
**Role:**
- Store frequently accessed (hot) URLs
- Reduce database load
- Improve latency for redirection

**Why needed:**
- System is read-heavy (10:1 ratio)
- Most traffic targets a small subset of URLs

**Cache Strategy:**
- Cache-aside strategy (lazy loading)
    - Application first checks cache
    - On cache miss, data is fetched from the database
    - Cache is updated with the fetched data
    - This provides flexibility and better control over cache behavior
- Cache popular URLs (~20%)

---

### 4. Database (Persistent Storage)
**Role:**
- Store all URL mappings permanently

**Options:**
- NoSQL (e.g., DynamoDB, Cassandra) for scalability
- Relational DB (initial stage)

**Data Stored:**
- Short code
- Original URL
- Metadata (created_at, expiry, etc.)

---

### 5. CDN (Optional but Recommended)
**Role:**
- Cache redirection responses globally
- Serve users from nearest location

**Why needed:**
- Reduce latency for global users
- Offload traffic from origin servers

---

### 6. ID Generator Service
**Role:**
- Generate unique short codes

**Options:**
- Non-sequential unique IDs (e.g., Snowflake, UUID) + Base62 encoding
    - Prevents predictability and enumeration attacks
    - Supports distributed ID generation at scale
- Distributed ID generator (e.g., Snowflake)

---

## High-Level Data Flow

### 1. URL Shortening Flow (Write Path)
- Client sends request to shorten URL
- Request hits Load Balancer
- Routed to Application Server
- App generates short code (via ID Generator)
- App stores mapping in Database
- App returns shortened URL to client

---

### 2. URL Redirection Flow (Read Path)
- User clicks shortened URL
- Request hits Load Balancer
- Routed to Application Server
- App checks the Cache (Redis)
    - If HIT -> return original URL
    - If MISS -> query Database
- Store result in Cache (if from DB)
- Redirect user to original URL

---

## Architecture Diagram

![URL Shortner Architecture Diagram](../../assets/images/URL%20Shortener%20Architecture.drawio.png)

---

## Design Decisions Summary

- Use **Load Balancer** for scalability and reliability
- Use **stateless App Servers** for horizontal scaling
- Use **Redis Cache** for fast reads
- Use **scalable DB** for long-term storage
- Use **CDN** for global performance
- Use **ID generator** for unique short URLs

---

## Conclusion
This architecture ensures:
- High availability
- Low latency (via caching + CDN)
- Scalability (horizontal scaling at app + DB level)
- Efficiency for read-heavy workloads

It is simple enough to start small but can evolve to handle large-scale traffic.