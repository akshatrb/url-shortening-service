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
