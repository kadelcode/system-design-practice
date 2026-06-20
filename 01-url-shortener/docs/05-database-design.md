<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Database Design - URL Shortener](#database-design---url-shortener)
  - [Goal](#goal)
  - [Design Requirements](#design-requirements)
  - [Primary Access Patterns](#primary-access-patterns)
    - [Write](#write)
    - [Read (Most Frequent)](#read-most-frequent)
    - [Analytics](#analytics)
  - [URL Mapping Table](#url-mapping-table)
    - [Schema](#schema)
  - [Field Definitions](#field-definitions)
  - [Example Record](#example-record)
  - [Indexing Strategy](#indexing-strategy)
    - [Primary Key Index](#primary-key-index)
      - [Purpose](#purpose)
    - [Expiration Index](#expiration-index)
      - [Purpose](#purpose-1)
    - [Analytics Index (Optional)](#analytics-index-optional)
      - [Purpose](#purpose-2)
  - [Scaling Considerations](#scaling-considerations)
    - [Read-Heavy Workload](#read-heavy-workload)
    - [Click Counter Optimization](#click-counter-optimization)
    - [Expired URLs](#expired-urls)
  - [Future Enhancements](#future-enhancements)
    - [Custom Aliases](#custom-aliases)
    - [User Ownership](#user-ownership)
    - [Analytics Table](#analytics-table)
  - [Summary](#summary)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Database Design - URL Shortener

## Goal

Design a database schema that efficiently stores URL mappings and supports fast lookups for redirection.

---

## Design Requirements

The database must support:

- Creating shortened URLs
- Looking up original URLs by short ID
- Tracking click counts
- Supporting URL expiration

---

## Primary Access Patterns

### Write

Create a new URL mapping

```text
Long URL -> Short ID
```

### Read (Most Frequent)

Retrieve original URL.

```text
Short ID -> Long URL
```

### Analytics

Retrive click statistics.

```text
Short ID -> Click Count
```

---

## URL Mapping Table

### Schema

```sql
CREATE TABLE url_mappings (
    short_id        VARCHAR(10) PRIMARY KEY,
    original_url    TEXT NOT NULL,
    click_count     BIGINT DEFAULT 0,
    expires_at      TIMESTAMP NULL,
    created_at      TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

---

## Field Definitions

| Column       | Type         | Description                  |
| ------------ | ------------ | ---------------------------- |
| short_id     | VARCHAR(10)  | Unique short URL identifier  |
| original_url | TEXT         | Original destination URL     |
| click_count  | BIGINT       | Number of redirects          |
| expires_at   | TIMESTAMP    | Optional expiration date     |
| created_at   | TIMESTAMP    | Creation timestamp           |
| updated_at   | TIMESTAMP    | Last update timestamp        |

---

## Example Record

```json
{
    "short_id": "aB3xYz",
    "original_url": "https://www.example.com/articles/system-design/url-shortener",
    "click_count": 1250,
    "expires_at": null,
    "created_at": "2026-06-14T10:00:00Z",
    "updated_at": "2026-06-14T10:00:00Z"
}
```

---

## Indexing Strategy

### Primary Key Index

```sql
PRIMARY KEY (short_id)
```

#### Purpose

Supports the most common operation:

```text
GET /{shortId}
```

Lookup complexity:

```text
O(log n)
```

### Expiration Index

```sql
CREATE INDEX idx_expires_at
ON url_mappings(expires_at);
```

#### Purpose

Allows efficient identication of expired URLs.

Example query:

```sql
SELECT *
FROM url_mappings
WHERE expires_at < NOW();
```

Used by:

- Cleanup jobs
- Archival processes
- Retention policies

---

### Analytics Index (Optional)

```sql
CREATE INDEX idx_click_count
ON url_mappings(click_count);
```

#### Purpose

Supports reporting queries such as:

```sql
SELECT *
FROM url_mappings
ORDER BY click_count DESC
LIMIT 100;
```

Useful for:

- Most-clicked URLs
- Trending links
- Reporting dashboards

---

## Scaling Considerations


### Read-Heavy Workload

The system is approximately:

```text
10 : 1
Reads : Writes
```

Therefore:

- Reads should be served from cache whenever possible
- Database becomes source of truth
- Cache reduces lookup latency

---

### Click Counter Optimization

Updating click_count on every redirect can create excessive write load.

Instead:

1. Increment counters in Redis
2. Periodically flush aggregated counts to the database

Benefits:

- Reduced database writes
- Better scalability
- Lower latency

---

### Expired URLs

Expired URLs should not be deleted immediately.

Recommended approach:

1. Mark as expired
2. Background cleanup job removes old records
3. Maintain auditability and recovery options

---

## Future Enhancements

As the system grows, additional features may require schema changes:

### Custom Aliases

```sql
custom_alias VARCHAR(100)
```

Example:

```text
https://short.ly/my-blog
```

---

### User Ownership

```sql
user_id UUID
```

Allows:

- User dashboards
- Link management
- Access control

---

### Analytics Table

Move analytics into a dedicated table:

```sql
CREATE TABLE url_click_events (
    id BIGSERIAL PRIMARY KEY,
    short_id VARCHAR(10),
    clicked_at TIMESTAMP
);
```

Benefits:

- Detailed click history
- Geographic analytics
- Device analytics

---

## Summary

The database schema is intentionally simple:

- URL mappings stored in a single table
- Primary key optimized for redirects
- Secondary indexes support expiration and analytics
- Click tracking designed for scalability
- Flexibility enough to support future features
