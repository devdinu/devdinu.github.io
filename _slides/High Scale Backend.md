---
slideNumber: true
theme: "serif"
highlightTheme: monokai
layout: slides
width: 1200
height: 960
---

## Building High Scale Backend Systems

*11 January 2022*

Dineshkumar,
Founder @ relyonmetrics

[@devdineshkumar](https://twitter.com/devdineshkumar)
[https://devdinu.github.io](https://devdinu.github.io/)

---
# About

Founder @ [relyonmetrics](http://relyonmetrics.com/)
- Built backend for security platform @ [Elastic](https://www.elastic.co/), backend system for cmd.com
- Built event driven system for high scale @ [Gojek](https://www.gojek.io/)
- Go, Kafka, distributed-systems enthusiast

---
# Agenda
- Basics
- Production Readiness
- Service Level
- Observability
- System Level / Architecture
- Best Practices
---

# Basics

Assume everything will fail!

---

# Production Readiness

---
## Service Level
- Load Testing
- Logging 
- Metrics
- Circuit breaker to prevent cascading failure
---
## System
- Replication
- Sharding
- Load Balancer
- Metrics
- Event Driven Systems
- Architecture optimisation
---

*Monolith service with DB*

![[00 simple service bottleneck.png]]


---

*Database becoming a bottleneck*

![[01 services with loadbalancer transparent.png]]

---

*Simple service with DB replication*

![[02 read write replicated database.png]]


---

## HowTo / Tools

- Kubernetes
- HaProxy
- Nginx
- GLB / AWS LB

---

# LoadBalancer

Sample load balancer config[^1] for nginx with multiple backend instances as upstream

```
http {
    upstream myapp1 {
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}
```

[^1]: [Nginx Documentation for load balancing](https://nginx.org/en/docs/http/load_balancing.html)

---

# Load Testing

Ensure whether the service will be fine for higher production load. Do 10x of estimated traffic for a future timeline 

Tools: 
*[wrk2](https://github.com/giltene/wrk2), gatling, ab, vegeta, k6, ...*

---

## Sample Vegeta Report

```
vegeta attack -targets backend  -duration=5s  -timeout=300s \
| vegeta plot > output.html
```


![[vegeta-latency-plot.png]]


---

*Service metrics plotted as histogram*

- service-0 has high latency

![[go service dashboard.png]]



---
# Observability

- Monitoring
- Alerting
- Centralized logging 
	- log properly with required information and additional metadata eg: status, method, order ...)
	-  log stitching
 - Tracing
	 - As we scale systems with multiple components & dependencies it's required for finding component responsible for the latency/error

*topic deems separte discussion or session*

---

# Architecture

As we build systems, we accomodate *hacks, tech debts and legacy decisions*.

> We have to rearchitecture or rebuild or remove complexity and extend architecture at times to scale our system further

Adopting different stack (e.g, Go, rust as per need)

Adopting technologies (*kafka/rabbitmq/pubsub, BigQuery, etc*)

---
*Monolith service*

considering a domain like food ordering, ride hailing, etc.

![[00 monolith service.png]]

---

![[01 microservices.png]]

- Easy to scale independently (infrastructure)
- Teams can develop independently
- Data & complexity is isolated

---

Leveraging pub-system like kafka will enable us to extend features, independent functionalities etc. 

Also keeping the system performant and stable.

![[03 event driven architecture.png]]

---
*more extensible and scaleable architecture for org*

![[04 event driven architecture extended.png]]


---
# Best Practices

- Sensible Timeouts
- Retries (assuming it'll fail) / leverage workers
	- Must be configured with retry limit
	- exponential backoffs
- Testing (TDD, Service Level, Contract)
- Backward compatibility
- Rollbacks
- IAC / Automation
---

# Thanks

## Questions

*Twitter: [@devdineshkumar](https://twitter.com/devdineshkumar)*

*Github: [@devdinu](https://github.com/devdinu)*

