# Advanced Python, Backend Development & System Design Notes

---

## 1. Advanced Python Concepts

### 🔒 Global Interpreter Lock (GIL)
The **GIL** is a mutex that protects access to Python objects, preventing multiple threads from executing Python bytecodes simultaneously.

- Limits true parallelism for **CPU-bound** tasks
- Suitable for **I/O-bound** tasks
- For CPU-bound work, use:
  - `multiprocessing`
  - `concurrent.futures.ProcessPoolExecutor`
  - Libraries like **NumPy** (releases GIL)

---

### 🧬 Metaclasses
Metaclasses are **classes of classes** that control how classes are created.

**Use cases:**
- ORMs (e.g., Django models)
- Singleton patterns
- Automatic subclass registration

```python
class SingletonMeta(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class Database(metaclass=SingletonMeta):
    pass
```

---

### 🧠 Python Memory Management
- **Reference counting** is primary
- **Cyclic garbage collector** handles reference cycles

**Common memory leak sources:**
- Reference cycles
- Global caches

**Debugging tools:**
- `gc`
- `objgraph`
- `heapy`

---

### 🎯 Decorators (Aspect-Oriented Programming)
Decorators add behavior without modifying function logic.

**Common uses:**
- Logging
- Authentication
- Caching

```python
from functools import wraps

def token_required(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        token = request.headers.get('Authorization')
        if not token or not validate_jwt(token):
            return {'error': 'Invalid token'}, 401
        return f(*args, **kwargs)
    return decorated
```

---

### ⚡ async/await vs Generators vs Coroutines

| Feature | Use Case |
|------|--------|
| Generators | Lazy iteration |
| Coroutines (`async def`) | Async I/O |
| asyncio | High-concurrency I/O |

⚠️ Avoid blocking calls inside async code.

---

### 🏷️ Type Hinting (mypy)
- Improves readability
- Enables static analysis
- No runtime overhead

Best practice: enforce `mypy` in CI/CD pipelines.

---

### 🔍 LEGB Rule
Scope resolution order:

**Local → Enclosing → Global → Built-in**

- `nonlocal` modifies enclosing scope
- Name mangling: `_ClassName__var`

---

### 📦 List Comprehension vs Generator vs map/filter

| Method | Behavior |
|-----|-------|
| List Comprehension | Eager |
| Generator Expression | Lazy, memory efficient |
| map/filter | Functional, often slower |

---

### 🔐 Thread Safety

- Use `threading.Lock`, `RLock`, `queue.Queue`
- Prefer `multiprocessing` for CPU-bound tasks
- In distributed systems, use message queues

---

### 📂 Context Managers
Ensure proper resource handling using `__enter__` and `__exit__`.

```python
class Transaction:
    def __init__(self, session):
        self.session = session

    def __enter__(self):
        return self.session

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is None:
            self.session.commit()
        else:
            self.session.rollback()
```

---

## 2. Backend Development & Architecture

### 🔑 Authentication (REST)
- `POST /login`
- JWT-based auth
- Use HTTPS, bcrypt
- Refresh tokens
- Store revoked tokens in Redis

---

### 🧩 Monolith → Microservices
- Domain-based decomposition
- **Strangler pattern**
- API Gateway: Kong / Traefik
- Service discovery: Consul / Eureka
- Inter-service: REST / gRPC

---

### 🗃️ Database Migrations
- Use **Alembic**
- Rolling deployments
- Expandable schema strategy
- Blue-Green deployments

---

### 🚀 Caching Strategies

| Tool | Features |
|----|--------|
| Redis | Rich data structures |
| Memcached | Simple key-value |

Invalidate cache via write-through or pub/sub.

---

### 🚦 Rate Limiting
- Token Bucket / Leaky Bucket
- Redis-based counters
- Middleware or decorators in FastAPI

---

### 🐢 Query Optimization
- `EXPLAIN ANALYZE`
- Proper indexing
- Avoid `SELECT *`
- Fix ORM N+1 issues

---

### 📣 Event-Driven Architecture
- Celery + Redis/RabbitMQ
- Kafka for streaming
- Idempotent consumers
- Transaction outbox pattern

---

### 🐳 Containerization & Deployment
- Docker (multi-stage builds)
- Kubernetes (Deployment, Service)
- Helm for configs
- CI/CD: GitHub Actions + Terraform

---

### 📊 Logging & Monitoring
- Sentry → Errors
- ELK → Logs
- Prometheus + Grafana → Metrics

---

### 🔄 API Versioning
- URL versioning (`/v1/users`)
- Header-based versioning
- Backward compatibility

---

## 3. System Design Questions

> Conceptual discussions only (no code required)

- Scalability
- High availability
- Fault tolerance
- Consistency models

---

## 4. Problem-Solving Questions

### ✅ Two Sum Variant

```python
def find_pairs(transactions, target):
    seen = {}
    pairs = []
    for num in transactions:
        complement = target - num
        if complement in seen:
            pairs.append((complement, num))
        seen[num] = True
    return pairs
```

---

### ♻️ LRU Cache

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cache = OrderedDict()
        self.capacity = capacity

    def get(self, key):
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)
```

---

### 🔀 Merge Intervals

```python
def merge(intervals):
    if not intervals:
        return []
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    for current in intervals[1:]:
        last = merged[-1]
        if current[0] <= last[1]:
            merged[-1] = [last[0], max(last[1], current[1])]
        else:
            merged.append(current)
    return merged
```

---

### 🧩 Word Break

```python
def wordBreak(s: str, wordDict: list) -> bool:
    word_set = set(wordDict)
    dp = [False] * (len(s) + 1)
    dp[0] = True
    for i in range(1, len(s) + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    return dp[-1]
```

---

### 🔤 Longest Substring Without Repeating Characters

```python
def lengthOfLongestSubstring(s: str) -> int:
    char_set = set()
    left = 0
    max_len = 0
    for right in range(len(s)):
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        char_set.add(s[right])
        max_len = max(max_len, right - left + 1)
    return max_len
```

---

### 🪣 Token Bucket Rate Limiter

```python
import time

class TokenBucket:
    def __init__(self, capacity, refill_rate):
        self.capacity = capacity
        self.tokens = capacity
        self.refill_rate = refill_rate
        self.last_refill = time.time()

    def allow_request(self):
        now = time.time()
        self.tokens += (now - self.last_refill) * self.refill_rate
        self.tokens = min(self.tokens, self.capacity)
        self.last_refill = now
        if self.tokens >= 1:
            self.tokens -= 1
            return True
        return False
```

---

### 🔗 Topological Sort (Kahn's Algorithm)

```python
from collections import deque, defaultdict

def topological_sort(nodes, edges):
    graph = defaultdict(list)
    indegree = {node: 0 for node in nodes}
    for u, v in edges:
        graph[u].append(v)
        indegree[v] += 1

    queue = deque([node for node in nodes if indegree[node] == 0])
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)

    return order if len(order) == len(nodes) else []
```

---

### 🌳 Validate Binary Search Tree

```python
def isValidBST(root, low=float('-inf'), high=float('inf')):
    if not root:
        return True
    if not (low < root.val < high):
        return False
    return isValidBST(root.left, low, root.val) and isValidBST(root.right, root.val, high)
```

---

### 📌 Conceptual Topics (No Code)
- Deadlock resolution
- Distributed consensus (Raft / Paxos)

---

📁 **Usage:**
- Save as `README.md`
- Upload directly to GitHub
- Ideal for interview prep & backend study

