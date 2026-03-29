# URL Shortener - Requirements

---

## Goal
Clearly define what the system should do (functional requirements) and how it should behave (non-functional requirements), while distinguishing between core and optional features.

---

## Functional Requirements

### Core Features (MVP)

These are must-have for a working URL shortener:

1. Shorten URL
    - Users can submit a long URL and receive a shortened version.
    - Example: `https://example.com/very/long/url` -> `https://short.ly/abc123`

2. Redirect to Original URL
    - When a user accesses the short URL, they are redirected to the original long URL

3. Unique Short Code Generation
    - Each shortened URL must have a unique identifier (e.g., `abc123`).

4. URL Persistence
    - Store mapping between short URL and original URL in a database

5. Basic Validation
    - Ensure submitted URLs are valid before shortening

### Optional Features (Nice-to-have)

1. Custom Alias
    - Users can define their own short code (e.g., `/my-link`).

2. Expiration
    - Links can expire after a certain time or number of clicks.

3. Analytics
    - Track number of clicks per short URL.
    - Capture metadata (IP, location, device type).

4. User Accounts
    - Users can register/login to manage their links.

5. Dashboard
    - View created links and analytics.

6. QR Code Generation
    - Generate QR codes for shortened URLs.

7. Rate Limiting
    - Prevent abuse by limiting number of requests per user/IP.

---

## Non-Functional Requirements

### Core (Critical System Qualities)
1. Scalability
    - System should handle high read traffic (redirections).
    - Must scale horizontally.

2. High Availability
    - Redirection service should have minimal downtime.

3. Low Latency
    - Redirection should be very fast (<100ms ideally).

4. Reliability
    - URLs should consistently redirect correctly.

5. Durability
    - Stored URLs must not be lost (persistent storage).

### Optional (Advanced Improvements)
1. Security
    - Prevent malicious URLs (phishing, malware).
    - HTTPS support.

2. Observability
    - Logging, monitoring, and alerting.

3. Consistency
    - Eventual consistency is acceptable for analytics.
    - Strong consistency required for URL mapping.

4. Cost Efficiency
    - Optimize storage and compute for large-scale usage.

5. Maintainability
    - Clean architecture for easy updates and feature additions.
