# ThingsBoard 3.4.3 Monolithic Dev Setup (Docker Compose)

This setup provides a **monolithic ThingsBoard CE 3.4.3** development environment with PostgreSQL, Cassandra, Redis, **pgAdmin** (Postgres GUI), and **RedisInsight** (Redis GUI).  

---

## 🚀 Usage

### 1. Initialize database schema
Run this step **once** to create the ThingsBoard schema in PostgreSQL and Cassandra:

```bash
docker compose -f docker-compose.yml -f docker-compose-init.yml up thingsboard-init
```

Once initialization completes successfully, you can stop the init container.  

---

### 2. Start the stack
Run all services (ThingsBoard, Postgres, Cassandra, Redis, pgAdmin, RedisInsight):

```bash
docker compose up -d
```

---

### 3. Check logs (if needed)
To view logs from the ThingsBoard service:

```bash
docker compose logs tb-node
```

---

## 🌐 Access UIs

- **ThingsBoard GUI** → [http://localhost:8080](http://localhost:8080)  
  - Default login:  
    - `tenant@thingsboard.org` / `tenant`  
    - `sysadmin@thingsboard.org` / `sysadmin`

- **pgAdmin (PostgreSQL)** → [http://localhost:5050](http://localhost:5050)  
  - Login: `admin@admin.com` / `admin`  
  - Add a new connection:  
    - Host: `postgres`  
    - Port: `5432`  
    - User: `postgres`  
    - Password: `postgres`

- **RedisInsight (Redis)** → [http://localhost:5540](http://localhost:5540)  
  - Add a new connection:  
    - Host: `redis`  
    - Port: `6379`

---

## 🗄️ Cassandra Quick Check

You can verify that Cassandra is running and the ThingsBoard schema exists.

### 1. Open CQLSH client inside the container
```bash
docker compose exec -it cassandra cqlsh
```

If successful, you should see something like:
```
Connected to Thingsboard Cluster at 127.0.0.1:9042
[cqlsh 6.1.0 | Cassandra 4.1.3 | CQL spec 3.4.6 | Native protocol v5]
```

### 2. List keyspaces
```sql
describe keyspaces;
```

Example output:
```
system  system_schema  system_auth  thingsboard ...
```

Confirm that the `thingsboard` keyspace exists.

### 3. Use the `thingsboard` keyspace
```sql
use thingsboard;
```

### 4. List tables
```sql
describe tables;
```

Example output:
```
ts_kv_cf  ts_kv_partitions_cf
```

### 5. Inspect the `ts_kv_cf` table
```sql
select * from ts_kv_cf limit 10;
```

Example output:
```
 entity_type | entity_id | key | partition | ts | bool_v | dbl_v | json_v | long_v | str_v
-------------+-----------+-----+-----------+----+--------+-------+--------+--------+-------
      DEVICE | ...       | ... | ...       | .. |   null |  null |   null |      8 |  null
```

---

## 🛑 Stop the stack

To stop all containers:

```bash
docker compose down
```

To stop and remove volumes (clear databases):

```bash
docker compose down -v
```
