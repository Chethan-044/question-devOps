# Section 15: Observability — Prometheus, Grafana, Alertmanager, Elasticsearch, Beats, Kibana

---

## Q140. What is observability?

**🔑 Keywords:** three pillars (metrics, logs, traces), internal state from external outputs, telemetry, debugging unknowns

**Beginner-Friendly Answer:**
Observability is the ability to understand what is happening deep inside a complex system based on the signals it emits from the outside. In modern distributed microservices, things will inevitably break in ways you never predicted. Observability uses three main pillars — **Metrics** (numbers), **Logs** (text events), and **Traces** (the journey of a request across services) — to let you pinpoint any failure in seconds.

**Detailed Answer:**
Observability is the ability to understand a system's internal behavior using the outputs it produces, especially metrics, logs, and traces.

---

## Q141. What is the difference between monitoring and observability?

**🔑 Keywords:** monitoring (detecting known failures / symptoms) vs observability (debugging unknown failures / root cause)

**Beginner-Friendly Answer:**
- **Monitoring:** Tells you *THAT* something is broken: "Warning: Service CPU > 90%!" or "Alert: Server is down!" (Checks known problems with predefined thresholds).
- **Observability:** Gives you the tools and data to ask *WHY* it broke: "Why did 2% of checkout requests fail only for users in Mumbai between 2:10 PM and 2:15 PM?" (Allows you to explore and debug unknown unknowns).

**Detailed Answer:**
Monitoring checks known conditions and helps detect failures. Observability also helps investigate unfamiliar problems by allowing engineers to examine the system's behavior from its outputs.

---

## Q142. What is Prometheus?

**🔑 Keywords:** time-series database (TSDB), pull/scrape model, PromQL, Alertmanager integration, service discovery

**Beginner-Friendly Answer:**
Prometheus is an open-source monitoring system and time-series database. Every 15 or 30 seconds, it visits all your microservices and infrastructure components, "scrapes" their metrics (like CPU usage, active HTTP requests, response times), and stores those numbers over time with timestamped labels. You can query this data using its powerful query language, PromQL.

**Detailed Answer:**
Prometheus is a monitoring and time-series database system that collects and stores metrics. It supports querying metrics and evaluating alerting rules.

---

## Q143. What is a metric?

**🔑 Keywords:** numerical time-series, counter, gauge, histogram, summary, dimensional labels

**Beginner-Friendly Answer:**
A metric is a raw numerical measurement recorded over time. For example:
- **Counter:** A number that only goes up (e.g. `total_orders_placed_count = 1420`).
- **Gauge:** A number that goes up and down (e.g. `memory_usage_percent = 72%`).
- **Histogram:** Distribution of values (e.g. `request_duration_seconds`).

**Detailed Answer:**
A metric is a numerical measurement observed over time, such as request count, latency, CPU utilization, memory consumption, or error rate.

---

## Q144. What is Grafana?

**🔑 Keywords:** visualization platform, dashboards, panels, multi-data source, PromQL / Elasticsearch queries

**Beginner-Friendly Answer:**
If Prometheus collects the numbers, Grafana is the beautiful dashboard that shows them to you. Grafana turns raw queries into visual charts, graphs, heatmaps, and alerts. It connects to multiple data sources (Prometheus, Elasticsearch, CloudWatch, PostgreSQL) so your team can view the health of your entire architecture on a single glass screen.

**Detailed Answer:**
Grafana is a visualization and analytics platform that can query supported data sources and present dashboards, charts, and alerts.

### Cross-question: Does Grafana normally replace Prometheus?

**Answer:**
No. Prometheus commonly collects and stores metrics, while Grafana visualizes data from Prometheus and other supported sources.

---

## Q145. What is Alertmanager?

**🔑 Keywords:** alert deduplication, grouping, silencing, routing, inhibition, Slack / PagerDuty / Email

**Beginner-Friendly Answer:**
Alertmanager handles alerts fired by Prometheus. When an issue occurs, Alertmanager prevents your on-call phone from receiving 500 duplicate alerts for the same underlying failure: it **groups** alerts together into a single summary, **silences** alerts during planned maintenance, and **routes** the message to the right channel (e.g., Slack for warnings, PagerDuty for critical outages).

**Detailed Answer:**
Alertmanager handles alert grouping, deduplication, routing, silencing, and inhibition for alerts sent to it, commonly from Prometheus.

---

## Q146. What is Elasticsearch?

**🔑 Keywords:** distributed search engine, JSON document store, inverted index, Lucene, real-time log analysis

**Beginner-Friendly Answer:**
Elasticsearch is a distributed, lightning-fast search and analytics engine. When you feed it millions of log lines from your microservices, it indexes every word using an "inverted index" so you can search through gigabytes of logs in milliseconds just like using Google.

**Detailed Answer:**
Elasticsearch is a distributed search and analytics engine commonly used to index and query log data and other documents.

---

## Q147. What is Kibana?

**🔑 Keywords:** Elastic UI, log discovery, Lucene query syntax, dashboard visualizations, index patterns

**Beginner-Friendly Answer:**
Kibana is the web-based visualization and exploration tool for data stored in Elasticsearch. It gives developers a search bar where they can type `order_id: 12345 AND status: error` and immediately see all matching log lines across all microservices, complete with timestamps and stack traces.

**Detailed Answer:**
Kibana provides a user interface for exploring and visualizing data stored in Elasticsearch, including log searches and dashboards.

---

## Q148. What are Beats?

**🔑 Keywords:** lightweight data shippers, Filebeat (logs), Metricbeat (system metrics), Packetbeat (network), low resource footprint

**Beginner-Friendly Answer:**
Beats are tiny, lightweight data-collecting agents installed on servers and container nodes. For example, **Filebeat** tails log files on your Kubernetes worker nodes and forwards them directly to Elasticsearch or Logstash. Because they are written in Go, they consume almost no CPU and memory.

**Detailed Answer:**
Beats are lightweight data shippers in the Elastic ecosystem. Depending on the Beat, they collect and forward information such as logs or system metrics to supported destinations.

---

## Q149. What is the difference between logs and metrics?

**🔑 Keywords:** metrics (numbers / trends / alerting) vs logs (detailed text / context / root cause)

**Beginner-Friendly Answer:**
- **Metrics (Numbers):** Tell you *WHEN* something went wrong: "HTTP 500 error count spiked to 200/sec at 14:02". Metrics are small, cheap to store, and ideal for triggering alerts.
- **Logs (Text):** Tell you *WHY* it went wrong: `"NullPointerException in UserService.java at line 45: Database connection timed out"`. Logs provide the rich contextual narrative needed to fix the bug.

**Detailed Answer:**
Logs record individual events or messages, while metrics represent numerical measurements aggregated or observed over time. Logs provide detailed context; metrics are often useful for identifying patterns and alerting.

---

## Q150. How would you investigate a sudden increase in HTTP 500 errors?

**🔑 Keywords:** error rate spike, deployment correlation, application logs (Kibana), Pod restarts, dependency health

**Beginner-Friendly Answer:**
My systematic investigation steps:
1. **Check Grafana:** Look at the error rate graph. Did it start spiking right after a new deployment was synced by Argo CD?
2. **Check Logs in Kibana:** Filter for `status: 500` or `level: ERROR` to find the exact stack traces.
3. **Check Dependencies:** Are calls to PostgreSQL or the Storage Service timing out?
4. **Check Pod Health:** Run `kubectl get pods` to see if Pods are restarting due to memory limits.
5. **Mitigate:** If tied to a bad release, immediately roll back via GitOps while investigating the root cause.

**Detailed Answer:**
I would check the error-rate and latency metrics, correlate the incident with deployment changes, inspect application and dependency logs, examine Pod restarts and resource pressure, and verify the health of the routing and backend components.

---

## Q151. What is an SLI, SLO, and SLA?

**🔑 Keywords:** SLI (Service Level Indicator / reality), SLO (Service Level Objective / internal target), SLA (Service Level Agreement / external legal contract)

**Beginner-Friendly Answer:**
- **SLI (Indicator):** What is *actually happening right now* (e.g., "99.8% of our API requests currently succeed in under 200ms").
- **SLO (Objective):** The internal goal your engineering team sets (e.g., "We aim to maintain 99.9% uptime this month").
- **SLA (Agreement):** The legal contract with your paying customers (e.g., "If our uptime drops below 99.5%, we give you a 20% refund on your bill").

**Detailed Answer:**
- SLI: A measured indicator of service performance, such as successful-request percentage or latency.
- SLO: A target for an SLI over a defined period.
- SLA: An agreement that may specify service commitments and consequences if they are not met.

### Cross-question: Why are these useful in production?

**Answer:**
They help teams define reliability goals, prioritize engineering work, and understand whether the service is meeting its expected performance.
