
# Load Testing

## Vegeta

```shell
echo GET http://127.0.0.1/auth -d "{"user_id": "$(uuidgen)", "password": "random"}" | vegeta attack  -rate=10000 -duration=10m  -timeout=5m  | vegeta report
```

## Ddosify

Random ID DDoS

```shell
 ddosify -t http://127.0.0.1/auth -d 30 -n 1000 -b '{"user_id": "{{_randomString}}", "password": "some_password"}'
```

# Go

```bash
fname=service1-$(date +%F)-01
curl http://localhost:9101/debug/pprof/heap?seconds=60 -o $fname
go tool pprof -http=:3333 ./bin/api-server $fname
```


# Profiling

service 0: 
http://localhost:9100/metrics?debug=1
http://localhost:9100/debug/pprof/

service 1:  
http://localhost:9101/metrics?debug=1
http://localhost:9101/debug/pprof/


[zoom](https://diligent.zoom.us/j/95100776756?pwd=amhQWVZOT2tFYkRDa2dNUmNyMHUzUT09#success)


Hey folks,
Thanks for joining, Hope the session was helpful.
Happy to chat more about it https://www.linkedin.com/in/dinesh-kumar

I'll share the information here https://devdinu.github.io/talks.html
I've talked more about kafka and go in the past which you can see here https://youtube.com/@relyonmetrics

Startup : https://relyonmetrics.com/
We help with managed (OSS) solution for monitoring stack to prevent downtimes, kafka/databases  majorly focused on scalability/cost cutting and helping with reliability.
If you're interested would love to chat here https://calendly.com/relyonmetrics

Thanks Yury and team for setting up this session. Looking forward for in-person/meet with community in future.