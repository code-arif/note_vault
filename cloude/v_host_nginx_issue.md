# Nginx vhost issue

## Problem
When serving a single-page application with Nginx, direct routes such as `/about` or `/contact` may return a 404 unless the server is configured to fall back to `index.html`.

## Solution
Use the following configuration inside your server block:

```nginx
index index.html;

location / {
    try_files $uri /index.html;
}
```

## Why this works
- `index index.html;` sets the default page to load.
- `try_files $uri /index.html;` serves existing files first and redirects unknown routes to `index.html`.
