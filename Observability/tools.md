The real-world observability stack most cloud-native teams use:

Prometheus → Metrics

Grafana → Visualization

ELK → Logs

We’ll explain this in a production mindset.

🧠 Big Picture First

Modern production stack typically looks like:

Applications
↓
Metrics → Prometheus
Logs → ELK / Loki
↓
Grafana dashboards
↓
Alertmanager / Slack / PagerDuty

1️⃣ Prometheus (Metrics Engine)
Simple Definition

Prometheus is a time-series database that collects and stores metrics.

It answers:

Is the system healthy?

How fast is it?

How much load is it handling?

How Prometheus Works

Applications expose metrics at /metrics

Prometheus scrapes them periodically

Stores time-series data

You query using PromQL

Alertmanager sends alerts

Example Metrics
http_requests_total
http_request_duration_seconds
container_cpu_usage_seconds_total

In Kubernetes

Prometheus monitors:

Nodes

Pods

Containers

API server

kubelet

etcd

Custom app metrics

Usually deployed via:

kube-prometheus-stack (Helm chart)

Why Prometheus Is Powerful

Pull-based model (safer than push)

Native Kubernetes integration

Label-based querying

Works well with autoscaling

2️⃣ Grafana (Visualization Layer)
Simple Definition

Grafana visualizes data.

It connects to:

Prometheus

Elasticsearch

Loki

CloudWatch

Many others

What Grafana Does

Dashboards

Alert visualization

Drill-down exploration

Multi-data source integration

Example dashboard:

CPU usage

Memory usage

Request latency

Error rate

All in one screen.

3️⃣ ELK Stack (Logging)

ELK =

Elasticsearch (storage + search)

Logstash (log processor)

Kibana (UI)

How ELK Works

App logs
↓
Fluentd / Filebeat
↓
Logstash
↓
Elasticsearch
↓
Kibana UI

What ELK Solves

Centralized logs

Full-text search

Root cause debugging

Audit compliance

4️⃣ Real Production Architecture

In Kubernetes:

Pods
↓
stdout logs
↓
FluentBit DaemonSet
↓
Elasticsearch
↓
Kibana

Metrics:

Pods
↓
Prometheus scrapes
↓
Stores time-series
↓
Grafana dashboards

5️⃣ Real Incident Scenario

Problem:
API latency spike.

Step 1:
Grafana dashboard → latency increased.

Step 2:
Prometheus → error rate rising.

Step 3:
Kibana → DB connection timeout logs.

Root cause identified.

Without observability:
You’d be guessing.

6️⃣ Why Not Just Logs?

Logs are detailed but:

High volume

Hard to aggregate

Slower to query

Metrics:

Lightweight

Fast

Better for alerting

Logs:

Better for root cause

Traces:

Best for distributed debugging

All three are needed.

7️⃣ Production Best Practices
Metrics

Instrument app (Prometheus client)

Track golden signals

Use labels wisely (avoid cardinality explosion)

Logging

Structured JSON logs

Correlation ID per request

No secrets in logs

Set retention policy

Alerts

Alert on:

Error rate

Latency SLO violation

Saturation

Pod crash loops

Avoid alerting on:

Every pod restart

Every CPU spike

8️⃣ Common Production Mistakes

No metric instrumentation in app

Too many logs (cost explosion)

High-cardinality metrics

No retention strategy

No runbooks

Alert fatigue

9️⃣ Prometheus vs ELK
Feature	Prometheus	ELK
Purpose	Metrics	Logs
Storage	Time-series	Document-based
Alerting	Built-in	Not native
Query	PromQL	Lucene DSL
Volume	Low-medium	High

They complement each other.

🔟 Modern Alternative Stack

Many teams now use:

Prometheus + Grafana + Loki + Tempo

Instead of full ELK.

Loki is lighter than Elasticsearch.

🔥 Real-World Enterprise Setup

EKS Cluster
↓
kube-prometheus-stack
↓
Prometheus + Alertmanager
↓
Grafana
↓
FluentBit
↓
Elasticsearch (or Loki)
↓
Slack / PagerDuty alerts

Everything centralized.

🎯 Final Understanding

Prometheus = numerical health
Grafana = visualization
ELK = event details

Together:
They give production confidence.