<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [API Design - URL Shortener](#api-design---url-shortener)
  - [Goal](#goal)
  - [Design Principles](#design-principles)
  - [1. Create Short URL](#1-create-short-url)
    - [Endpoint](#endpoint)
    - [Request](#request)
      - [Headers](#headers)
      - [Body](#body)
    - [Success Response](#success-response)
      - [Status](#status)
      - [Body](#body-1)
    - [Client Errors](#client-errors)
      - [Invalid URL](#invalid-url)
    - [Server Errors](#server-errors)
      - [Internal Error](#internal-error)
  - [2. Redirect to Original URL](#2-redirect-to-original-url)
    - [Endpoint](#endpoint-1)
    - [Success Response](#success-response-1)
      - [Status](#status-1)
      - [Headers](#headers-1)
      - [Behavior](#behavior)
    - [URL Not Found](#url-not-found)
      - [Status](#status-2)
      - [Body](#body-2)
  - [HTTP Status Codes](#http-status-codes)
  - [Rate Limiting](#rate-limiting)
    - [Status](#status-3)
    - [Response](#response)
  - [Resource Model](#resource-model)
    - [URL Mapping](#url-mapping)
  - [Future Enhancements](#future-enhancements)
  - [Summary](#summary)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# API Design - URL Shortener

## Goal

Define the REST APIs used by clients to create and access shortened URLs.

## Design Principles

- RESTful endpoints
- JSON request/response format
- Predictable HTTP status codes
- Clear error handling
- Simple API surface

## 1. Create Short URL

Creates a new shortened URL.

### Endpoint
```
POST /shorten
```

### Request

#### Headers
```
Content-Type: application/json
```

#### Body
```
{
    "url": "https://www.example.com/articles/system-design/url-shortener"
}
```

### Success Response

#### Status
```
201 Created
Location: https://short.ly/aB3xYz
```

#### Body
```
{
    "shortId": "aB3xYz",
    "shortUrl": "https://short.ly/aB3xYz",
    "originalUrl": "https://www.example.com/articles/system-design/url-shortener",
    "createdAt": "2026-06-13T17:39:00Z"
}
```

### Client Errors

#### Invalid URL

400 Bad Request
```
{
    "error": {
        "code": "INVALID_URL",
        "message": "Provided URL is not valid."
    }
}
```

### Server Errors

#### Internal Error

500 Internal Server Error
```
{
    "error": {
        "code": "INTERNAL_ERROR",
        "message": "Something went wrong."
    }
}
```

## 2. Redirect to Original URL

Retrieves the original URL and redirects the user.

### Endpoint
```
GET /{shortId}
```

Example
```
GET /aB3xYz
```
---

### Success Response

#### Status
```
302 Found
```

#### Headers
```
Location: https://www.example.com/articles/system-design/url-shortener
Cache-Control: no-store
```

#### Behavior

The client is automatically redirected to the original URL.

**Design Decision:** The service uses **302 Found** instead of **301 Moved Permanently**.

While 301 can improve performance through browser caching, 302 provides better support for click analytics because clients are less likely to permanently cache the redirect.

To ensure accurate analytics and abuse detection, redirect responses should include cache-control headers that discourage browser and intermediary caching.

Example:

```http
Cache-Control: no-store
```

### URL Not Found

#### Status
```
404 Not Found
```

#### Body
```
{
    "error": {
        "code": "SHORT_URL_NOT_FOUND",
        "message": "Short URL does not exist."
    }
}
```

## HTTP Status Codes

| Status Code | Meaning                          |
|-------------|----------------------------------|
| 201         | Short URL created successfully   |
| 302         | Redirect to original URL         |
| 400         | Invalid request                  |
| 404         | Short URL not found              |
| 429         | Too many requests (rate limited) |
| 500         | Internal server error            |

## Rate Limiting

### Status

```http
429 Too Many Requests
```

### Response

```
{
    "error": {
        "code": "RATE_LIMIT_EXCEEDED",
        "message": "Too many requests. Please try again later."
    }
}
```

## Resource Model

### URL Mapping
```
{ 
    "shortId": "aB3xYz", 
    "originalUrl": "https://www.example.com/articles/system-design/url-shortener", 
    "createdAt": "2026-04-18T10:00:00Z" 
}
```

## Future Enhancements

The initial API is intentionally minimal.

Possible future endpoints:

- `GET /urls/{shortId}`: Retrieve URL details
- `DELETE /urls/{shortId}`: Remove a shortened URL
- `PUT /urls/{shortId}`: Update a URL
- `GET /urls/{shortId}/analytics`: Track clicks/usage

Examples of future features

- URL expiration
- Custom aliases
- Click analytics
- User ownership
- Bulk URL shortening


## Summary

The URL Shortener API exposes two core endpoints:

- `POST /shorten` -> Create a short URL
- `GET /{shortId}` -> Redirect to the original URL

This API design is simple, RESTful, and sufficient for the initial version of the system.
