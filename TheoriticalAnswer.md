# Backend & Python Interview Notes

---

## 1. Advanced Python Concepts

1. **GIL in Python**  
   The GIL is a mutex that protects access to Python objects, preventing multiple threads from executing Python bytecodes simultaneously. It limits true parallelism in multi-threaded CPU-bound tasks. For CPU-bound work, use `multiprocessing`, `concurrent.futures.ProcessPoolExecutor`, or external libraries like NumPy that release the GIL.

2. **Metaclasses**  
   Metaclasses are classes of classes that define how classes behave. They customize class creation (`__new__`, `__init__`). Common production uses include ORMs like Django’s model system, singleton enforcement, and automatic subclass registration.

3. **Python Memory Management**  
   Python uses reference counting for primary memory management and a cyclic garbage collector for detecting reference cycles. Memory leaks often occur from cycles or global caches. Debug using `objgraph`, `heapy`, or the `gc` module.

4. **Decorators for AOP**  
   Decorators wrap functions to add cross-cutting behavior like logging, authentication, or caching. Examples include `@lru_cache` for memoization or custom decorators for JWT validation in Flask/FastAPI.

5. **async/await vs Generators vs Coroutines**  
   Generators yield values lazily. Coroutines (`async def`) can await other coroutines for asynchronous I/O. Use `asyncio` for I/O-bound tasks. Avoid blocking calls inside async code and improper event-loop usage.

6. **Type Hinting with mypy**  
   Type hints improve readability and enable static analysis. In large projects, enforce `mypy` in CI/CD for early bug detection and better IDE support without runtime overhead.

7. **LEGB Rule**  
   Scope resolution follows: **Local → Enclosing → Global → Built-in**. Use `nonlocal` to modify enclosing-scope variables. Name mangling (`_Class__var`) provides pseudo-private attributes.

8. **List Comprehension vs Generator vs map/filter**  
   List comprehensions are eager. Generator expressions are lazy and memory-efficient. `map`/`filter` are functional but often slower due to function-call overhead.

9. **Thread Safety**  
   Use `threading.Lock`, `RLock`, or `queue.Queue` for synchronization. For true parallelism, prefer `multiprocessing`. In distributed systems, avoid shared state; use message queues or actor models.

10. **Context Managers**  
    Context managers define `__enter__` and `__exit__` for safe resource handling, such as database transactions that commit on success or roll back on failure.

---

## 2. Backend Development and Architecture

1. **RESTful Authentication**  
   Use `POST /login`, return JWT on success. Secure with HTTPS, bcrypt hashing, refresh tokens, and store revoked tokens in Redis for logout.

2. **Monolith to Microservices Migration**  
   Decompose by business domains using the strangler pattern. Introduce API gateways (Kong/Traefik), service discovery (Consul/Eureka), and REST/gRPC for inter-service communication.

3. **Database Migrations in Production**  
   Use Alembic with rolling deployments. Apply expandable schema changes first and use blue-green deployments for zero downtime.

4. **Caching Strategies**  
   Redis for distributed caching with rich data structures; Memcached for simple key-value use cases. Invalidate caches on writes using write-through or event-driven approaches.

5. **Rate Limiting**  
   Implement token bucket or leaky bucket algorithms. In FastAPI, use Redis-based middleware or decorators keyed by IP or API key.

6. **Database Query Optimization**  
   Use `EXPLAIN ANALYZE`, proper indexing, avoid `SELECT *`, fix ORM N+1 queries, and profile queries with debugging tools.

7. **Event-Driven Architecture**  
   Use Celery with RabbitMQ/Redis or Kafka for high-throughput streaming. Ensure idempotency and exactly-once processing with the transaction outbox pattern.

8. **Containerization & Deployment**  
   Dockerize applications using multi-stage builds. Orchestrate with Kubernetes, manage configs with Helm, and automate CI/CD with GitHub Actions and Terraform.

9. **Error Logging & Monitoring**  
   Use Sentry for exception tracking, ELK for centralized logging, and Prometheus + Grafana for metrics and alerting.

10. **API Versioning**  
    Use URL versioning (`/v1/users`) or headers. Prefer backward-compatible changes and deprecate older versions gradually.

---

## 3. System Design Questions

1. **URL Shortener**  
   Use Base62 encoding of an auto-increment ID or hash-based mapping. Store mappings in NoSQL (Cassandra/DynamoDB). Handle collisions via retries.

2. **Notification System**  
   Queue messages with Kafka or Celery. Workers deliver notifications (FCM/APNs, SES). Use retries and dead-letter queues for failures.

3. **E-commerce Inventory**  
   Apply optimistic locking or row-level locks. Reserve stock atomically within DB transactions and confirm via payment webhooks.

4. **Recommendation Engine**  
   Build ETL pipelines with Airflow/Pandas, train collaborative filtering models, and serve results via APIs. Use Redis for real-time lookups.

5. **Resumable File Upload**  
   Implement chunked uploads (Tus or S3 multipart). Track progress in Redis/DB and validate integrity using checksums.

6. **Chat Application Backend**  
   Use WebSockets (FastAPI + Socket.IO or Django Channels). Store messages in SQL/NoSQL DBs and broadcast via Redis pub/sub.

7. **Distributed Logging**  
   Ship logs using Fluentd/Filebeat to Elasticsearch. Use correlation IDs for tracing and rotate logs for compliance.

8. **Scaling to 1M RPM**  
   Horizontal scaling with load balancers, auto-scaling groups, read replicas, database sharding, and CDN for static assets.

9. **Third-Party API Integration**  
   Use retries, circuit breakers, caching, and exponential backoff to handle failures and rate limits.

10. **CMS Backend**  
    Implement RBAC, content versioning, draft/published states, and SEO-friendly slugs with sitemaps.

---

## 4. Problem-Solving Concepts

1. **Two Sum Variant** – Hashmap lookup for complements (O(n) time, O(n) space).
2. **LRU Cache** – OrderedDict with O(1) get/put operations.
3. **Merge Intervals** – Sort and merge overlapping ranges (O(n log n)).
4. **Word Break** – Dynamic programming with prefix checks.
5. **Longest Substring Without Repeats** – Sliding window technique.
6. **Token Bucket Rate Limiter** – Refill tokens over time and consume per request.
7. **Topological Sort** – Kahn’s algorithm using indegrees.
8. **Deadlock Resolution** – Lock ordering, timeouts, retries, or optimistic concurrency.
9. **BST Validation** – Recursive min/max bounds or in-order traversal.
10. **Distributed Consensus** – Raft uses leader election, quorum, and log replication.

---

📌 **Usage**: Save this file as `README.md` and upload directly to GitHub.

