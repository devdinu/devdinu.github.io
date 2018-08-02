# Official

There are few things we learned by building distributed systems with huge scale. The following has plenty of articles/content online. This is just a summary.

Rather than just code, your architecture or design of system evolves.

- multiple instances, with loadbalancer to deal with scale.
- circuit breakers
- master,slave/cluster for db
- archiving data
- cache (performance, proper caching, invalidation)
- read only API's with slave db
- event driven architecture (kafka, events)
- sharding
- monitoring, alerts
    - performance (percentiles), or request, db, cache
- load testing
- proper indexes, tweak resources vm
    - ulimit
    - postgres config for performance tuning
    - redis maxmemory
- workers, async processing
- failure isolation
- ci/cd
- TDD
