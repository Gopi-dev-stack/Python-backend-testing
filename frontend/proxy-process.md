# FrontendâBackend Communication Using Nginx Reverse Proxy

## Architecture

```text
                Internet
                    â
                    â¼
        http://98.91.246.171
                    â
                    â¼
          Frontend EC2 (Nginx)
                    â
        âââââââââââââ´ââââââââââââ
        â                       â
        â¼                       â¼
 Serves index.html        Proxies /users
                                 â
                                 â¼
                 Backend EC2 (Flask API)
                  http://172.31.42.151:5000
                                 â
                                 â¼
                            Amazon RDS
```

---

# Why use a Reverse Proxy?

The backend is running on a **private IP**:

```
172.31.42.151:5000
```

Private IPs are **not accessible** from the Internet.

Instead of exposing the backend directly, Nginx acts as a **Reverse Proxy**.

Benefits:

- Backend remains private.
- Only Nginx is exposed publicly.
- Easier SSL (HTTPS) configuration.
- Better security.
- Single public endpoint for frontend and backend.

---

# Frontend Configuration

Instead of calling the backend directly:

```javascript
const backendIP = "http://172.31.42.151:5000";
```

Use:

```javascript
const backendIP = "";
```

or

```javascript
const backendIP = "/api";
```

Then the API call becomes:

```javascript
fetch(`${backendIP}/users`)
```

which resolves to

```
GET /users
```

or

```
GET /api/users
```

depending on your configuration.

---

# Browser Request Flow

When a user opens:

```
http://98.91.246.171
```

the browser downloads:

```
index.html
```

Inside JavaScript:

```javascript
fetch("/users")
```

The browser automatically sends:

```
GET http://98.91.246.171/users
```

Notice:

It **does not** contact the backend directly.

---

# Nginx Configuration

```nginx
server {
    listen 80;
    server_name _;

    root /usr/share/nginx/html;
    index index.html;

    location /users {

        proxy_pass http://172.31.42.151:5000;

        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

---

# How Nginx Works

### Step 1

Browser requests:

```
GET /
```

Nginx returns:

```
index.html
```

---

### Step 2

JavaScript executes:

```javascript
fetch("/users")
```

Browser sends:

```
GET /users
```

---

### Step 3

Nginx matches:

```nginx
location /users
```

---

### Step 4

Instead of serving a file,

Nginx forwards the request to:

```
http://172.31.42.151:5000/users
```

---

### Step 5

Flask receives:

```python
@app.route("/users")
```

---

### Step 6

Flask queries Amazon RDS.

```
SELECT * FROM users;
```

---

### Step 7

Flask returns JSON.

Example:

```json
[
  {
    "id": 1,
    "name": "John",
    "email": "john@gmail.com"
  }
]
```

---

### Step 8

Nginx forwards the JSON response back to the browser.

---

# Complete Request Flow

```text
Browser
   â
   â GET /
   â¼
Nginx
   â
   â¼
index.html
   â
   â¼
JavaScript

fetch("/users")
        â
        â¼
GET /users
        â
        â¼
Nginx
        â
        â location /users
        â¼
Proxy Request
        â
        â¼
Flask
172.31.42.151:5000/users
        â
        â¼
Amazon RDS
        â
        â¼
JSON Response
        â
        â¼
Flask
        â
        â¼
Nginx
        â
        â¼
Browser
```

---

# Advantages

- Backend is not publicly accessible.
- No CORS issues because requests originate from the same domain.
- Improved security.
- Easier HTTPS configuration.
- Centralized routing through Nginx.
- Scalable architecture for production deployments.

---

# Summary

```
Browser
    â
    â¼
Frontend (Nginx)
    â
    âââ "/"      â index.html
    â
    âââ "/users" â Flask Backend
                    â
                    â¼
                  Amazon RDS
```

**Key Point:** The browser never communicates directly with the private backend (`172.31.42.151:5000`). All API requests first go to the **Nginx server** on the frontend EC2, which securely proxies them to the backend Flask application.
