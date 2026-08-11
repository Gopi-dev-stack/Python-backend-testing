# JavaScript vs Flask Frontend Communication

This document explains how a browser communicates with a backend API using two different approaches:

1. JavaScript (`fetch()`)
2. Flask (`requests.get()`)

---

# 1. JavaScript Architecture

In this approach, JavaScript runs inside the browser.

```text
Browser
â
âââ HTML
âââ CSS
âââ JavaScript
      â
      âââ fetch("/users")
              â
              â¼
          Backend API
              â
              â¼
            Database
```

## Flow

### Step 1

User opens:

```
http://frontend-server
```

Browser downloads:

- HTML
- CSS
- JavaScript

---

### Step 2

JavaScript executes inside the browser.

Example:

```javascript
fetch("/users")
```

---

### Step 3

The browser sends:

```
GET /users
```

to the backend.

---

### Step 4

Backend queries the database.

```
SELECT * FROM users;
```

---

### Step 5

Backend returns JSON.

Example:

```json
[
  {
    "id":1,
    "name":"John",
    "email":"john@gmail.com"
  }
]
```

---

### Step 6

JavaScript updates the webpage.

---

# JavaScript Request Flow

```text
Browser
    â
    â¼
HTML + CSS + JavaScript
    â
    â¼
JavaScript executes
    â
    â¼
fetch("/users")
    â
    â¼
Backend API
    â
    â¼
Database
    â
    â¼
JSON Response
    â
    â¼
Browser updates UI
```

---

# Where does JavaScript run?

Inside:

- Chrome
- Edge
- Firefox
- Safari

The browser executes JavaScript.

---

# 2. Flask Architecture

In this approach, Python executes on the server.

The browser never communicates directly with the backend.

```text
Browser
     â
     â¼
Frontend Flask
     â
     âââ render_template()
     âââ requests.get("/users")
               â
               â¼
         Backend API
               â
               â¼
            Database
```

---

# Flow

### Step 1

Browser opens:

```
http://frontend-server:81
```

Browser sends

```
GET /
```

---

### Step 2

Frontend Flask receives the request.

```python
@app.route("/")
def index():
```

---

### Step 3

Python executes:

```python
requests.get("http://backend:5000/users")
```

This code runs **inside the frontend server**, not inside the browser.

---

### Step 4

Backend returns JSON.

```json
[
  {
    "id":1,
    "name":"John"
  }
]
```

---

### Step 5

Frontend Flask renders HTML.

```python
return render_template(
    "index.html",
    users=users
)
```

---

### Step 6

Browser receives HTML.

The browser never knows another API call happened.

---

# Flask Request Flow

```text
Browser
    â
GET /
    â
    â¼
Frontend Flask
    â
    âââ requests.get()
    â
    â¼
Backend API
    â
    â¼
Database
    â
    â¼
JSON
    â
    â¼
Frontend Flask
    â
render_template()
    â
    â¼
HTML
    â
    â¼
Browser
```

---

# Who Executes the Code?

## JavaScript

```text
Browser
   â
   â¼
JavaScript
   â
fetch()
   â
   â¼
Backend
```

JavaScript executes inside the browser.

---

## Flask

```text
Browser
   â
GET /
   â
   â¼
Frontend Flask
   â
Python requests.get()
   â
   â¼
Backend
```

Python executes inside the server.

---

# Browser vs Server

| Feature | JavaScript | Flask |
|----------|------------|--------|
| Executes in | Browser | Server |
| API Call | Browser â Backend | Frontend Flask â Backend |
| Uses | fetch() | requests.get() |
| HTML Rendering | Browser | Flask (`render_template`) |
| Browser talks to backend | Yes | No |

---

# Easy Way to Remember

## JavaScript

```
Browser
    â
    â¼
JavaScript
    â
    â¼
Backend API
```

The browser makes the API call.

---

## Flask

```
Browser
    â
    â¼
Frontend Flask
    â
    â¼
Backend API
```

The frontend server makes the API call.

---

# Real-World Analogy

## JavaScript

```
Customer
     â
     â¼
Directly talks to Chef
```

The customer (browser) communicates directly with the chef (backend).

---

## Flask

```
Customer
     â
     â¼
Receptionist (Frontend Flask)
     â
     â¼
Chef (Backend API)
```

The customer only talks to the receptionist (frontend Flask).

The receptionist communicates with the chef (backend).

---

# Summary

## JavaScript

- Runs inside the browser.
- Uses `fetch()`.
- Browser directly communicates with the backend.
- Suitable for SPAs and dynamic frontends.

## Flask

- Runs inside the server.
- Uses `requests.get()` or `requests.post()`.
- Frontend Flask communicates with the backend.
- Browser only receives the final HTML.
- Useful for server-side rendering and internal API communication.

**Key Difference:**

- **JavaScript (`fetch()`)** â Executes in the **browser** (client-side).
- **Flask (`requests.get()`)** â Executes on the **server** (server-side).
