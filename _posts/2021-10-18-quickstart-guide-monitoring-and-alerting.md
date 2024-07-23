---
title: "Monitoring and Alerting"
tags: ["kickstart", "monitoring", "alerting"]
toc: true
---

# Introduction

Observability: monitoring, alerting are critical for production systems. We'll discuss how we can get started and reap the benefits.
we won't touch on complex SLAs/Uptime etc as those deserve separate blogs & meant for systems at a high scale.

**Monitoring**
Measure and track the metrics which are relevant to your business, resources (will define this below). You can view them in a graph to gain insights or look at them to figure out the cause for any downtimes.

**Alerting**
Once we have the monitoring in place, we can set up alerts to notify us when the metrics are over a threshold or found anomalies.


# What to monitor
To start with, we could monitor 
* valuable business metrics (eg. No of orders made per hours/day, No of successful and failed signups)
* whether my website is up
* resources (once you scale up or you're tech-heavy)
* failures on API calls
* Time taken by critical code pieces/components

## Resources
resources cover multiple aspects of the components/systems

* resources your system depends on
    * Disk size (or any other storage dependency)
    * memory
    * CPU
    * limits (file descriptor / open connections depending on the component)

The metrics differ based on component and your use case, (eg. database, Kafka, k8s, ...)

## Business metrics
This is more valuable to your org, and DevOps/team who's responsible for maintaining systems will care about resource metrics. If you're an early startup
this could be easily defined from a single backend, if you're dealing with a large system, you might have to aggregate and get a meaningful metric to track.

Some valuable metrics which common and can be expanded are
* User signups 
* Successful orders (this could be whatever your core product)
* Payments received (to track revenue/MRR)

## Time Taken
If you have a system which needs to be performant, time should be measured so we could optimise. The following could be monitored to start with
* DB calls
* External service calls
* Complex computation logics
* E2E order creation time (more aligned with business you're doing)
* API calls
 
# Alerting
Once you've monitoring in place, you can use it passively, to view historical data. for instance how many users logged in to our system. To actively maintain
systems health you would have set up alerts.

Most broadly falls into these categories
* Thresholds
* Anomalies
* Zero metrics (this's often missed/not mentioned in alerting ecosystem)

> It doesn't matter what metric you're setting an alert on, ensure you look at historical data, and set up a threshold based on that to avoid noisy / non-useful frequent alerts

## Threshold
If you are relying on a disk of 250GB when you consume 100% of it, it'll go down, so to avoid it, we usually set an alert when disk usage
goes beyond say 80% (this threshold depends on the resource consumption rate, how easy it's to recover/expand etc)

Wrt resources, the following are few examples.
```
Disk - disk usage > 80%
Memory - memory usage > 80%
CPU - 5/15m load > 80%
File descriptors - open file descriptors > 2000
```

## Anomalies

Anomalies are metrics that fall outside of your expectation / historical data range [boundary](boundary). 
* If your system has a 10,000 orders/ hour avg, if you fall below say 7000 then there could be something wrong in the system.
* If your avg order is 1000/min if it suddenly spikes to 10k/min that needs to be verified as it could be a hack.

Though one could assume this could be tracked by failure metrics, sometimes it could be an issue on say proxy/other components which could cause this without any explicit failure metrics.


## Zero Metrics
All other alerting would not help when your system goes down. To deal with that, similar to anomalies but when your metrics are zero. 
In scaled business, if your system always receives data/API calls, if you don't have any requests for 10min then it's an issue that needs to be looked at.

You can just start with statsd and grafana or leverage suitable tools.

Available tools/solutions in ecosystem
* Influx - [Tick stack](https://www.influxdata.com/blog/introduction-to-influxdatas-influxdb-and-tick-stack/)
* [Prometheus](https://prometheus.io/docs/introduction/overview/) and [Alertmanager](https://prometheus.io/docs/alerting/latest/overview/)
* [Elastic](https://www.elastic.co/observability)
* [Datadog](https://www.datadoghq.com/product/infrastructure-monitoring/)
