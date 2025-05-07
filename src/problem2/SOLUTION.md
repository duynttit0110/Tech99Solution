# 📈 High Availability Trading Platform on AWS

This project presents an architecture for a scalable, resilient, and cost-effective trading system inspired by Binance, designed to handle **500 requests per second** with a **p99 latency under 100ms** using **AWS services**.

---

## 🎯 Features Demonstrated

- Spot Market Order Matching
- User Authentication & Account Management
- Trade Execution & Order Book Updates
- Real-time WebSocket Feed
- Historical Trade/Order Data Querying

---

## 🏗️ Architecture Overview
- Route 53
- WAF
- Application Load Balancer
- ECS Fargate
- Secret Manager
- S3
- Athena
- Aurora Postgres
- SNS
- CloudWatch 
- ElastiCache
- Github / Gitlab Action (CICD)

---

## ✅ Services Used & Justification

| Component | Purpose | Alternatives / Notes |
|-|-|-|
| Route 53 + WAF | DNS routing + protection from common web attacks | Cloudflare (not natively integrated with AWS) |
| Application Load Balancer | Traffic routing and health checking | NGINX (manual scaling and maintenance) |
| ECS Fargate | Serverless containers for API, Auth, WebSocket | EKS (more complex), Lambda (latency overhead) |
| Aurora PostgreSQL | RDBMS for users, orders, balances | OpenSearch |
| ElastiCache (Redis) | Session cache, rate limiting, price caching | DynamoDB (slower for these use cases) |
| SNS | Event stream for trade events | Kinesis (simpler, but less flexible), RabbitMQ |
| Order Matcher Service | High-speed, stateless matching engine | Lambda (too slow for sub-100ms matching) |
| WebSocket Gateway (ECS) | Real-time price updates to clients | API Gateway WebSocket (limited flexibility, higher cost) |
| Amazon S3 + Athena | Queryable storage for historical trades | Redshift (higher cost), Aurora (not ideal for analytics) |
| CloudWatch | Observability, tracing, error logging | Datadog |
| Secrets Manager | Secure management of API keys and credentials | Env vars (not secure/scalable) |

---

## 📈 Scaling Plan

| Layer | Current Setup | Scale Strategy |
|-|-|-|
| API (Auth/Trade) | ECS with ALB | Auto-scale tasks by CPU/memory/latency |
| Order Matching | One ECS service per symbol group | Partition by trading pair, deploy per pair |
| Aurora DB | Writer + Reader Replica | Add read replicas, use RDS Proxy, shard by market |
| SNS | Topic, Decsription | Kafka (self-managed) with 3-node MSK  and Increase brokers/partitions, mirror for disaster recovery |
| Redis | Clustered ElastiCache | Add shards, enable cluster mode |
| WebSocket | ECS service | Auto-scale by connection count, use CloudFront if needed  |
| Trade History | S3 + Athena | Datadog (Alert, Monitoring) |

---

## ⚡ Performance Strategy (p99 < 100ms)

- In-memory order book for ultra-low latency
- ElastiCache for hot path reads and rate limiting
- Aurora Proxy to reduce DB connection latency
- ECS services tuned for minimal cold start
- Datadog tracing for profiling bottlenecks (Scale up)

---

## 💰 Cost Optimization

| Component     | Optimization Approach                        |
|---------------|----------------------------------------------|
| ECS           | Use Fargate Spot for non-critical workloads  |
| Aurora        | Use Serverless v2 for automatic scaling      |
| SNS           | Scale brokers gradually, use tiered storage  |
| ElastiCache   | TTLs and memory-efficient caching strategy   |
| Analytics     | S3 + Athena instead of persistent compute DB |

---

## 📦 Tech Stack Summary

- **Compute:** ECS Fargate (with Spot where appropriate)
- **Data:** Aurora PostgreSQL, ElastiCache Redis
- **Messaging:** SNS
- **Realtime:** WebSocket over ECS
- **Storage:** S3 + Athena (for historical data)
- **Security:** AWS WAF, Secrets Manager
- **Monitoring:** CloudWatch

---

## 🚀 Future Improvements

- Add support for margin and futures markets
- Integrate AI-based fraud detection
- Deploy analytics dashboards with Quicksight
- Introduce mobile/web client frontends
