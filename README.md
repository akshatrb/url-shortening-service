# url-shortening-service
Highly Scalable URL Shortener Service like TinyURL

A URL shortener service creates a short url/aliases/tiny url against a long url.Moreover, when user click on the tiny url, he gets redirected to original url.

## Requirements
- Functional Requirements:
    - Given a URL, generate a shortened link for it. This has to be unique.
    - When users access a short link, redirect to the original link.
    - Users should optionally be able to pick a custom short link for their URL.
    - Links will expire after a standard default timespan. Users should also be able to set an expiration time.

- Non Functional Requirements:
    - The system should be highly scalable. We're targetting a scale of 100B redirections per month.
    - The system should be highly available. If service goes down, all the URL redirections will start failing.
    - Shortened links should be arbitrary in orderd to be unpredictable.
    - URL redirection to occur in real time with minimal latency.

- Extended Requirements (Further Scope):
    - Setting up the analytics; eg., how many times a particular URL got redirected; Redirection distribution across different days of the week, etc.
    - Service functionalities should be exposed through REST APIs to other services.

## Capacity Estimation + Constraints
- Assumption
    - The system appears to be read-heavy. We will have much higher requests for redirection as compared to new URL shortenings.
    - We can safely assume a **100:1** ratio between redirection and shortening (read/write).

- Traffic estimates
    - **1B** new URL shortenings per month.
    - Based on above assumed ratio, 100 * 1B => 100B redirections per month.
    - New URL shortenings per second
        - 1B/( 30 days * 24 hours * 3600 seconds) = **~400 URLs/s**
    - URLs redirections per second
        - 100B/( 30 days * 24 hours * 3600 seconds) = **39,000 Redirections/s**

- Storage estimates
    - Assume storing every URL shortening request for 5 years.
    - Assume each object takes **700 bytes**
    - Total such objects = 1 Billion * 5 years * 12 months = **60 Billion Objects**
    - Total storage = 60 Billion * 700 bytes = **~50 TB**

- Bandwidth estimates
    - Write (URL Shortenings): 400 URL/s * 700 bytes/URL = **~300 KB/s**
    - Read (Redirections): 39,000 Redirections/s * 700 bytes/URL = **~25 MB/s**

- Cache estimates
    - We can have multiple ways to decide which URL redirections to cache and how.
    - Assuming that top 10% of URL redirections take up the 90% of the traffic, we cache 10% hit URLs.
    - Requests per day: 39,000 Redirections/s * 3600s * 24 hours = **~3.5 Billion Requests/ day**
    - Cache top 10% of this daily traffic: 0.1 * 3.5 Billion * 700 bytes = **~120 GB**

- Summary
    - Assuming 1 Billion new URL shortenings per month and 100:1 read:write (Shortening/Redirection) ratio

    Category | Calculation | Estimate
    ---- | ---- | ----
    New URLs | 1 Billion / (30 days * 24 hours * 3600 seconds) | 400 /s
    URL redirections | 1 Billion * 100 / (30 days * 24 hours * 3600 seconds) | 39,000/s
    Incoming data | 700 bytes/URL * 400 URL/s | 300 KB/s
    Outgoing data | 700 bytes/URL * 39,000 URL/s | 25 MB/s
    Storage for 5 years | 700 bytes/URL * 1 Billion * 60 months | 50 TB
    Memory for cache | 39,000 URL * 3600 seconds * 24 hours * 700 bytes * 10% | 120 GB
