
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