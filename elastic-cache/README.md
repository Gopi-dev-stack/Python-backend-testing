Elastic Cache (ElastiCache) + RDS example

This directory shows how to connect an EC2-hosted Python app to an RDS MySQL database and cache query results in Redis (ElastiCache).

Overview
- Launch an EC2 instance (Amazon Linux) and connect to it via SSH.
- Create an RDS MySQL database and a `users` table.
- Create an ElastiCache (Redis) cluster and copy its endpoint.
- Install dependencies on the EC2 host and run `cache.py` to demonstrate caching.

Quick steps (summary)
1. Create EC2 (Amazon Linux) and SSH in.
2. Create an RDS MySQL database (allow access from your EC2 security group).
3. Create an ElastiCache Redis cluster (note the endpoint, without port).
4. On EC2, install packages and run `cache.py`.

Install dependencies on Amazon Linux

```bash
sudo yum update -y
sudo yum install mariadb105-server -y   # optional, client tools
sudo yum install python3-pip -y
python3 -m pip install --upgrade pip
pip3 install pymysql redis
```

Connect to RDS from server (example)

```bash
mysql -h <rds-endpoint> -u admin -p<rds-password>
```

Create DB and table (run in MySQL client)

```sql
CREATE DATABASE test;
USE test;

CREATE TABLE users (
    user_id VARCHAR(50) PRIMARY KEY,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    email VARCHAR(100)
);

INSERT INTO users (user_id, first_name, last_name, email)
VALUES ('1245', 'John', 'Doe', 'john.doe@example.com');
```

Create ElastiCache Redis
- In the AWS Console: Services â ElastiCache â Redis â Create.
- Give a name and create the cluster.
- Open the created cache and copy the primary endpoint (without the :port).

Configure and run
- Place `cache.py` (provided below) in this directory on your EC2 instance.
- Edit the RDS and Redis connection values in the `RDS CONFIG` and `REDIS CONFIG` sections of `cache.py`, or set the corresponding environment variables as shown.

Environment variables supported (preferred)
- DB_HOST, DB_USER, DB_PASS, DB_NAME
- REDIS_HOST, REDIS_PORT (defaults to 6379)
- REDIS_TTL (seconds, defaults to 90)

Run

```bash
python3 cache.py
```

Behavior
- On first run the script queries RDS and stores the result in Redis with a TTL (default 90s).
- Subsequent runs (within TTL) will return results from Redis cache.
- After the TTL expires, the next run will reload data from RDS and refresh the cache.

Notes
- Ensure your EC2 security group can reach RDS on port 3306 and ElastiCache on port 6379.
- For production, use IAM, parameter stores, or secret managers for secrets.

`cache.py` (placed in this directory)

File: [Elastic-cache/cache.py](Elastic-cache/cache.py#L1)
