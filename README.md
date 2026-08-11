# Python-backend-testing####### Backend-flask project #############

process::
step-1
-->>Create rds with security group "port 3306 and all traffic enabled" 
-->>now connect sql work bench with your rds credentials
--->>now create database and run through workbench

# Python Backend Testing (Flask)

A small Flask backend used for testing MySQL/RDS connectivity and basic CRUD operations on a `users` table.

## Overview

This repository contains a minimal Flask app (`app.py`) that demonstrates connecting to a MySQL-compatible database (local or AWS RDS) and exposes simple REST endpoints to manage users.

## Prerequisites
- Python 3.8 or newer
- pip
- MySQL / MariaDB server or AWS RDS instance
- git (optional)

## Quickstart (local)

1. Clone the repository:

```bash
git clone https://github.com/CloudTechDevOps/python-backend-testing.git
cd python-backend-testing
```


3. Create the database and table (example):

```sql
CREATE DATABASE dev;
USE dev;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) NOT NULL UNIQUE
);
```

4. Configure database connection:

Edit `app.py` and set the `db_config` values, or export environment variables before running (example shown for Linux/macOS):

```bash
DB_HOST=your-db-host
DB_USER=your-db-user
DB_PASS=your-db-password
DB_NAME=dev
```
5. Run the app:

```bash
python app.py
```

The app listens on port 5000 by default (http://127.0.0.1:5000).

## API Endpoints

- GET /users â return all users
- GET /users/<id> â return user by id
- POST /users/add â add a new user (JSON body: `{"name":"..","email":".."}`)
- PUT /users/update/<id> â update a user (JSON body with `name` and/or `email`)
- DELETE /users/delete/<id> â delete a user

Examples:

```bash
curl -X GET http://localhost:5000/users

curl -X POST http://localhost:5000/users/add \
  -H "Content-Type: application/json" \
  -d '{"name":"John Doe","email":"john@example.com"}'

curl -X PUT http://localhost:5000/users/update/1 \
  -H "Content-Type: application/json" \
  -d '{"name":"John Updated","email":"john.updated@example.com"}'

curl -X DELETE http://localhost:5000/users/delete/1
```

## Running on a server (example notes)
- Use a production WSGI server (gunicorn, uWSGI) behind a reverse proxy for production.
- For AWS: run the Flask app on an EC2 instance and use an RDS MySQL instance for the DB. Ensure security groups allow the required traffic (DB port 3306 allowed from the app host).

To run the app in background on Linux:

```bash
# start
nohup python3 app.py > flask.log 2>&1 &
# check
ps aux | grep app.py
# stop
pkill -f app.py
```

## Files

- `app.py` â main Flask application
- `requirements.txt` â Python dependencies
- `test.sql` â example SQL (if present)

