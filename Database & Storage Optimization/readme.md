Here’s a structured approach to optimizing **PostgreSQL** and **MongoDB** for performance:

---

## **1. PostgreSQL Optimization**
### **A. Indexing Strategies**
1. **Primary & Unique Indexes**: Automatically created for `PRIMARY KEY` and `UNIQUE` constraints.
2. **B-Tree Index**: Default index type, best for equality and range queries.
   ```sql
   CREATE INDEX idx_users_email ON users(email);
   ```
3. **GIN Index**: Best for full-text search and JSONB fields.
   ```sql
   CREATE INDEX idx_users_metadata ON users USING GIN (metadata);
   ```
4. **BRIN Index**: Efficient for large, sequential data.
   ```sql
   CREATE INDEX idx_users_created_at ON users USING BRIN (created_at);
   ```

### **B. Query Optimization**
1. **Before Optimization (Slow Query)**:
   ```sql
   SELECT * FROM orders WHERE status = 'pending' AND created_at > NOW() - INTERVAL '7 days';
   ```
2. **After Optimization (Using Index & Partitioning)**:
   ```sql
   CREATE INDEX idx_orders_status_created ON orders(status, created_at);
   ```
   ```sql
   SELECT * FROM orders WHERE status = 'pending' AND created_at > NOW() - INTERVAL '7 days';
   ```

### **C. Partitioning Strategies**
1. **Range Partitioning** (e.g., partition by date):
   ```sql
   CREATE TABLE orders_2024_01 PARTITION OF orders
   FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
   ```
2. **Hash Partitioning** (distributes data evenly):
   ```sql
   CREATE TABLE users PARTITION BY HASH (id);
   ```

---

## **2. MongoDB Optimization**
### **A. Indexing Strategies**
1. **Single Field Index**:
   ```javascript
   db.users.createIndex({ email: 1 });
   ```
2. **Compound Index**:
   ```javascript
   db.orders.createIndex({ status: 1, createdAt: -1 });
   ```
3. **Text Index**:
   ```javascript
   db.articles.createIndex({ content: "text" });
   ```

### **B. Query Optimization**
1. **Before Optimization (Slow Query)**
   ```javascript
   db.orders.find({ status: "pending", createdAt: { $gt: ISODate("2024-01-01") } });
   ```
2. **After Optimization (Using Index)**
   ```javascript
   db.orders.createIndex({ status: 1, createdAt: -1 });
   db.orders.find({ status: "pending", createdAt: { $gt: ISODate("2024-01-01") } }).hint({ status: 1, createdAt: -1 });
   ```

### **C. Sharding (Partitioning in MongoDB)**
- **Enable Sharding**:
  ```javascript
  sh.enableSharding("myDatabase");
  ```
- **Shard a Collection**:
  ```javascript
  db.orders.createIndex({ customerId: "hashed" });
  sh.shardCollection("myDatabase.orders", { customerId: "hashed" });
  ```

---

### ✅ **Deliverables**
✔ **Explanation of optimization techniques** → ✅ Covered for PostgreSQL & MongoDB  
✔ **Example queries before and after optimization** → ✅ Provided for both databases  

Would you like me to generate performance benchmarks or provide additional **real-world scenarios**? 🚀
