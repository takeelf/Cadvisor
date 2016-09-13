# Cadvisor

### 1. Install InfluxDB with a db named cadvisor

```
docker run \
 -d \
 -p 8083:8083 \
 -p 8086:8086 \
 --expose 8090 \
 --expose 8099 \
 -v /disk/monitoring/influxdb:/data \
 -e PRE_CREATE_DB=cadvisor \
 -e ADMIN_USER="root" \
 -e INFLUXDB_INIT_PWD="we-need-lastpass" \
 --name influxdb \
 tutum/influxdb
```

---

### 2. Install cAdvisor to run with a link to the influxdb and storage drivers to write to it

```
docker run \
--volume=/:/rootfs:ro \
--volume=/var/run:/var/run:rw \
--volume=/sys:/sys:ro \
--volume=/var/lib/docker/:/var/lib/docker:ro \
--publish=8081:8080 \
--detach=true \
--link influxdb:influxdb \
--name=cadvisor \
google/cadvisor:latest \
-storage_driver=influxdb \
-storage_driver_db=cadvisor \
-storage_driver_host=influxdb:8086
```

---

### 3. Setup grafana with persistent storage and link the container to the influxdb container

```
docker run \
  -d \
  -p 3000:3000 \
  -e INFLUXDB_HOST=localhost \
  -e INFLUXDB_PORT=8086 \
  -e INFLUXDB_NAME=cadvisor \
  -e INFLUXDB_USER=root \
  -e INFLUXDB_PASS=we-need-lastpass \
  --link influxdb:influxdb \
  -v /disk/monitoring/grafana:/var/lib/grafana \
  --name grafana \
  grafana/grafana
```





```
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8999:8080 \
  --detach=true \
  --name=cadvisor \
  --link atsd:atsd \
  axibase/cadvisor:latest \
  --storage_driver=atsd \
  --storage_driver_atsd_protocol=tcp \
  --storage_driver_host=atsd \
  --storage_driver_buffer_duration=15s \
  --housekeeping_interval=15s 
```



```
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8999:8080 \
  --detach=true \
  --name=cadvisor \
  --link atsd:atsd \
  axibase/cadvisor:latest \
  --storage_driver=atsd \
  --storage_driver_atsd_protocol=http \
  --storage_driver_host=52.79.151.187:8088 \
  --storage_driver_user=admin \
  --storage_driver_password=admin \
  --storage_driver_buffer_duration=15s \
  --housekeeping_interval=15s 
  ```



