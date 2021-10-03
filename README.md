# Spring-Boot with Prometheus and Grafana

## Spring-Boot Web with Actuator

- Start Spring-Boot ด้วยคำสั่ง

  ```shell
  ./mvnw spring-boot:run
  ```

- ลองเข้า [http://localhost:8080/actuator](http://localhost:8080/actuator) จะเห็น Endpoint ในส่วนของ health เท่านั้น

  http://localhost:8080/actuator
  
  ```json
  {
    "_links": {
      "self": {
        "href": "http://localhost:8080/actuator",
        "templated": false
      },
      "health-path": {
        "href": "http://localhost:8080/actuator/health/{*path}",
        "templated": true
      },
      "health": {
        "href": "http://localhost:8080/actuator/health",
        "templated": false
      }
    }
  }
  ```
  
  ปกติ มันจะเปิด expose Endpoint ในส่วนของ health ออกมาทางหน้า web ส่วนอื่น ๆ จะ expose ออกทาง jmx เท่านั้น ตารางนี้

  |ID|JMX|Web|
  |---|---|---|
  |auditevents|Yes|No|
  |beans|Yes|No|
  |caches|Yes|No|
  |conditions|Yes|No|
  |configprops|Yes|No|
  |env|Yes|No|
  |flyway|Yes|No|
  |health|Yes|Yes|
  |heapdump|N/A|No|
  |httptrace|Yes|No|
  |info|Yes|No|
  |integrationgraph|Yes|No|
  |jolokia|N/A|No|
  |logfile|N/A|No|
  |loggers|Yes|No|
  |liquibase|Yes|No|
  |metrics|Yes|No|
  |mappings|Yes|No|
  |prometheus|N/A|No|
  |quartz|Yes|No|
  |scheduledtasks|Yes|No|
  |sessions|Yes|No|
  |shutdown|Yes|No|
  |startup|Yes|No|
  |threaddump|Yes|No|  
  
  ที่มา [Spring Boot Actuator: Production-ready Features - 2.2. Exposing Endpoints](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints.exposing)

  ซึ่งถ้าต้องการให้ Endpoint ในส่วนของ Metrics ออกมาmที่ Web ต้อง config เพิ่ม

- เปิดให้ metrics เข้าได้จาก Web ต้องเพิ่ม Config เข้าไป ใน application.properties
  
  ```properties
  # src/main/resources/application.properties
  
  management.endpoints.web.exposure.include=health,metrics
  ```

  ```json
  {
    "_links": {
      "self": {
        "href": "http://localhost:8080/actuator",
        "templated": false
      },
      "health": {
        "href": "http://localhost:8080/actuator/health",
        "templated": false
      },
      "health-path": {
        "href": "http://localhost:8080/actuator/health/{*path}",
        "templated": true
      },
      "metrics-requiredMetricName": {
        "href": "http://localhost:8080/actuator/metrics/{requiredMetricName}",
        "templated": true
      },
      "metrics": {
        "href": "http://localhost:8080/actuator/metrics",
        "templated": false
      }
    }
  }
  ```

  เข้าไปดูที่ จะเห็นรายชื่อของ metrics ที่ actuator เปิดให้เราเห็น

  ```json
  
  {
    "names": [
      "http.server.requests",
      "jvm.buffer.count",
      "jvm.buffer.memory.used",
      "jvm.buffer.total.capacity",
      "jvm.classes.loaded",
      "jvm.classes.unloaded",
      "jvm.gc.live.data.size",
      "jvm.gc.max.data.size",
      "jvm.gc.memory.allocated",
      "jvm.gc.memory.promoted",
      "jvm.gc.pause",
      "jvm.memory.committed",
      "jvm.memory.max",
      "jvm.memory.used",
      "jvm.threads.daemon",
      "jvm.threads.live",
      "jvm.threads.peak",
      "jvm.threads.states",
      "logback.events",
      "process.cpu.usage",
      "process.files.max",
      "process.files.open",
      "process.start.time",
      "process.uptime",
      "system.cpu.count",
      "system.cpu.usage",
      "system.load.average.1m",
      "tomcat.sessions.active.current",
      "tomcat.sessions.active.max",
      "tomcat.sessions.alive.max",
      "tomcat.sessions.created",
      "tomcat.sessions.expired",
      "tomcat.sessions.rejected"
    ]
  }
  ```

  ลองเอา บางตัวมาเปิดดู จะเห็นว่า เราจะสามารถเห็น Metrics Data ในแต่ละเรื่อง

  [http://localhost:8080/actuator/metrics/http.server.requests](http://localhost:8080/actuator/metrics/http.server.requests)
  
  ```json
  {
    "name": "http.server.requests",
    "description": null,
    "baseUnit": "seconds",
    "measurements": [
      {
        "statistic": "COUNT",
        "value": 7.0
      },
      {
        "statistic": "TOTAL_TIME",
        "value": 0.13740365799999998
      },
      {
        "statistic": "MAX",
        "value": 0.004001397
      }
    ],
    "availableTags": [
      {
        "tag": "exception",
        "values": [
          "None"
        ]
      },
      {
        "tag": "method",
        "values": [
          "GET"
        ]
      },
      {
        "tag": "uri",
        "values": [
          "/actuator/metrics/{requiredMetricName}",
          "/actuator",
          "/actuator/metrics"
        ]
      },
      {
        "tag": "outcome",
        "values": [
          "CLIENT_ERROR",
          "SUCCESS"
        ]
      },
      {
        "tag": "status",
        "values": [
          "404",
          "200"
        ]
      }
    ]
  }
  ```

  [http://localhost:8080/actuator/metrics/jvm.memory.used](http://localhost:8080/actuator/metrics/jvm.memory.used)

  ```json
  {
    "name": "jvm.memory.used",
    "description": "The amount of used memory",
    "baseUnit": "bytes",
    "measurements": [
      {
        "statistic": "VALUE",
        "value": 59318424
      }
    ],
    "availableTags": [
      {
        "tag": "area",
        "values": [
          "heap",
          "nonheap"
        ]
      },
      {
        "tag": "id",
        "values": [
          "G1 Old Gen",
          "CodeHeap 'non-profiled nmethods'",
          "G1 Survivor Space",
          "Compressed Class Space",
          "Metaspace",
          "G1 Eden Space",
          "CodeHeap 'non-nmethods'"
        ]
      }
    ]
  }
  ```

## Expose Endpoints เพื่อรองรับ Prometheus ผ่าน /actuator/prometheus

เนื่องจากโดย Default แล้ว actuator ไม่ได้เปิดให้ใช้งาน Prometheus Endpoints และเพื่อให้เราสามารถใช้ Prometheus มาอ่านค่า metrics เราจำเป็นต้องเปิดมันขึ้นมา โดย

- เพิ่ม dependency micrometer-registry-prometheus ใน pom.xml

  ```xml  
  <dependency>
      <groupId>io.micrometer</groupId>
      <artifactId>micrometer-registry-prometheus</artifactId>
  </dependency>
  ```

- เพิ่ม prometheus เข้าไปใน application.properties
  
  ```properties
  management.endpoints.web.exposure.include=health,metrics,prometheus
  ```

- ลองเข้า [http://localhost:8080/actuator/](http://localhost:8080/actuator/) จะเห็น Endpoints prometheus เพิ่มเข้ามา
  
  ```json
  {
    "_links": {
      "self": {
        "href": "http://localhost:8080/actuator",
        "templated": false
      },
      "health": {
        "href": "http://localhost:8080/actuator/health",
        "templated": false
      },
      "health-path": {
        "href": "http://localhost:8080/actuator/health/{*path}",
        "templated": true
      },
      "prometheus": {
        "href": "http://localhost:8080/actuator/prometheus",
        "templated": false
      },
      "metrics-requiredMetricName": {
        "href": "http://localhost:8080/actuator/metrics/{requiredMetricName}",
        "templated": true
      },
      "metrics": {
        "href": "http://localhost:8080/actuator/metrics",
        "templated": false
      }
    }
  }
  ```

- ลองเข้า [http://localhost:8080/actuator/prometheus](http://localhost:8080/actuator/prometheus) จะเห็น metrices ต่าง ๆ ที่ actuator เปิดให้ prometheus เข้ามาดูได้

  ```text
  # HELP system_cpu_usage The "recent cpu usage" for the whole system
  # TYPE system_cpu_usage gauge
  system_cpu_usage 0.12420701352050287
  # HELP jvm_buffer_total_capacity_bytes An estimate of the total capacity of the buffers in this pool
  # TYPE jvm_buffer_total_capacity_bytes gauge
  jvm_buffer_total_capacity_bytes{id="mapped - 'non-volatile memory'",} 0.0
  jvm_buffer_total_capacity_bytes{id="mapped",} 0.0
  jvm_buffer_total_capacity_bytes{id="direct",} 24576.0
  # HELP jvm_threads_peak_threads The peak live thread count since the Java virtual machine started or peak was reset
  # TYPE jvm_threads_peak_threads gauge
  jvm_threads_peak_threads 16.0
  # HELP tomcat_sessions_created_sessions_total
  # TYPE tomcat_sessions_created_sessions_total counter
  tomcat_sessions_created_sessions_total 0.0
  # HELP jvm_gc_live_data_size_bytes Size of long-lived heap memory pool after reclamation
  # TYPE jvm_gc_live_data_size_bytes gauge
  jvm_gc_live_data_size_bytes 0.0
  # HELP jvm_gc_memory_allocated_bytes_total Incremented for an increase in the size of the (young) heap memory pool after one GC to before the next
  # TYPE jvm_gc_memory_allocated_bytes_total counter
  jvm_gc_memory_allocated_bytes_total 4.2991616E7
  # HELP logback_events_total Number of error level events that made it to the logs
  # TYPE logback_events_total counter
  logback_events_total{level="warn",} 0.0
  logback_events_total{level="debug",} 0.0
  logback_events_total{level="error",} 0.0
  logback_events_total{level="trace",} 0.0
  logback_events_total{level="info",} 6.0
  # HELP jvm_threads_live_threads The current number of live threads including both daemon and non-daemon threads
  # TYPE jvm_threads_live_threads gauge
  jvm_threads_live_threads 16.0
  # HELP system_cpu_count The number of processors available to the Java virtual machine
  # TYPE system_cpu_count gauge
  system_cpu_count 8.0
  # HELP jvm_memory_max_bytes The maximum amount of memory in bytes that can be used for memory management
  # TYPE jvm_memory_max_bytes gauge
  jvm_memory_max_bytes{area="heap",id="G1 Survivor Space",} -1.0
  jvm_memory_max_bytes{area="heap",id="G1 Old Gen",} 2.147483648E9
  jvm_memory_max_bytes{area="nonheap",id="Metaspace",} -1.0
  jvm_memory_max_bytes{area="nonheap",id="CodeHeap 'non-nmethods'",} 7553024.0
  jvm_memory_max_bytes{area="heap",id="G1 Eden Space",} -1.0
  jvm_memory_max_bytes{area="nonheap",id="Compressed Class Space",} 1.073741824E9
  jvm_memory_max_bytes{area="nonheap",id="CodeHeap 'non-profiled nmethods'",} 2.44105216E8
  # HELP jvm_gc_max_data_size_bytes Max size of long-lived heap memory pool
  # TYPE jvm_gc_max_data_size_bytes gauge
  jvm_gc_max_data_size_bytes 2.147483648E9
  # HELP jvm_classes_loaded_classes The number of classes that are currently loaded in the Java virtual machine
  # TYPE jvm_classes_loaded_classes gauge
  jvm_classes_loaded_classes 7239.0
  # HELP jvm_gc_memory_promoted_bytes_total Count of positive increases in the size of the old generation memory pool before GC to after GC
  # TYPE jvm_gc_memory_promoted_bytes_total counter
  jvm_gc_memory_promoted_bytes_total 8055808.0
  # HELP tomcat_sessions_rejected_sessions_total
  # TYPE tomcat_sessions_rejected_sessions_total counter
  tomcat_sessions_rejected_sessions_total 0.0
  # HELP jvm_buffer_memory_used_bytes An estimate of the memory that the Java virtual machine is using for this buffer pool
  # TYPE jvm_buffer_memory_used_bytes gauge
  jvm_buffer_memory_used_bytes{id="mapped - 'non-volatile memory'",} 0.0
  jvm_buffer_memory_used_bytes{id="mapped",} 0.0
  jvm_buffer_memory_used_bytes{id="direct",} 24576.0
  # HELP jvm_memory_used_bytes The amount of used memory
  # TYPE jvm_memory_used_bytes gauge
  jvm_memory_used_bytes{area="heap",id="G1 Survivor Space",} 2245568.0
  jvm_memory_used_bytes{area="heap",id="G1 Old Gen",} 1.3412352E7
  jvm_memory_used_bytes{area="nonheap",id="Metaspace",} 3.0860304E7
  jvm_memory_used_bytes{area="nonheap",id="CodeHeap 'non-nmethods'",} 1253120.0
  jvm_memory_used_bytes{area="heap",id="G1 Eden Space",} 3145728.0
  jvm_memory_used_bytes{area="nonheap",id="Compressed Class Space",} 4163848.0
  jvm_memory_used_bytes{area="nonheap",id="CodeHeap 'non-profiled nmethods'",} 6005760.0
  # HELP tomcat_sessions_active_current_sessions
  # TYPE tomcat_sessions_active_current_sessions gauge
  tomcat_sessions_active_current_sessions 0.0
  # HELP http_server_requests_seconds
  # TYPE http_server_requests_seconds summary
  http_server_requests_seconds_count{exception="None",method="GET",outcome="SUCCESS",status="200",uri="/actuator/prometheus",} 1.0
  http_server_requests_seconds_sum{exception="None",method="GET",outcome="SUCCESS",status="200",uri="/actuator/prometheus",} 0.047826556
  http_server_requests_seconds_count{exception="None",method="GET",outcome="SUCCESS",status="200",uri="/actuator",} 1.0
  http_server_requests_seconds_sum{exception="None",method="GET",outcome="SUCCESS",status="200",uri="/actuator",} 0.098665748
  # HELP http_server_requests_seconds_max
  # TYPE http_server_requests_seconds_max gauge
  http_server_requests_seconds_max{exception="None",method="GET",outcome="SUCCESS",status="200",uri="/actuator/prometheus",} 0.0
  http_server_requests_seconds_max{exception="None",method="GET",outcome="SUCCESS",status="200",uri="/actuator",} 0.0
  # HELP process_files_max_files The maximum file descriptor count
  # TYPE process_files_max_files gauge
  process_files_max_files 10240.0
  # HELP tomcat_sessions_active_max_sessions
  # TYPE tomcat_sessions_active_max_sessions gauge
  tomcat_sessions_active_max_sessions 0.0
  # HELP process_start_time_seconds Start time of the process since unix epoch.
  # TYPE process_start_time_seconds gauge
  process_start_time_seconds 1.633228780989E9
  # HELP tomcat_sessions_expired_sessions_total
  # TYPE tomcat_sessions_expired_sessions_total counter
  tomcat_sessions_expired_sessions_total 0.0
  # HELP process_uptime_seconds The uptime of the Java virtual machine
  # TYPE process_uptime_seconds gauge
  process_uptime_seconds 1607.517
  # HELP tomcat_sessions_alive_max_seconds
  # TYPE tomcat_sessions_alive_max_seconds gauge
  tomcat_sessions_alive_max_seconds 0.0
  # HELP jvm_buffer_count_buffers An estimate of the number of buffers in the pool
  # TYPE jvm_buffer_count_buffers gauge
  jvm_buffer_count_buffers{id="mapped - 'non-volatile memory'",} 0.0
  jvm_buffer_count_buffers{id="mapped",} 0.0
  jvm_buffer_count_buffers{id="direct",} 3.0
  # HELP process_files_open_files The open file descriptor count
  # TYPE process_files_open_files gauge
  process_files_open_files 49.0
  # HELP jvm_classes_unloaded_classes_total The total number of classes unloaded since the Java virtual machine has started execution
  # TYPE jvm_classes_unloaded_classes_total counter
  jvm_classes_unloaded_classes_total 0.0
  # HELP jvm_threads_states_threads The current number of threads having NEW state
  # TYPE jvm_threads_states_threads gauge
  jvm_threads_states_threads{state="runnable",} 7.0
  jvm_threads_states_threads{state="blocked",} 0.0
  jvm_threads_states_threads{state="waiting",} 6.0
  jvm_threads_states_threads{state="timed-waiting",} 3.0
  jvm_threads_states_threads{state="new",} 0.0
  jvm_threads_states_threads{state="terminated",} 0.0
  # HELP jvm_gc_pause_seconds Time spent in GC pause
  # TYPE jvm_gc_pause_seconds summary
  jvm_gc_pause_seconds_count{action="end of minor GC",cause="G1 Evacuation Pause",} 2.0
  jvm_gc_pause_seconds_sum{action="end of minor GC",cause="G1 Evacuation Pause",} 0.012
  # HELP jvm_gc_pause_seconds_max Time spent in GC pause
  # TYPE jvm_gc_pause_seconds_max gauge
  jvm_gc_pause_seconds_max{action="end of minor GC",cause="G1 Evacuation Pause",} 0.0
  # HELP system_load_average_1m The sum of the number of runnable entities queued to available processors and the number of runnable entities running on the available processors averaged over a period of time
  # TYPE system_load_average_1m gauge
  system_load_average_1m 2.35693359375
  # HELP jvm_memory_committed_bytes The amount of memory in bytes that is committed for the Java virtual machine to use
  # TYPE jvm_memory_committed_bytes gauge
  jvm_memory_committed_bytes{area="heap",id="G1 Survivor Space",} 3145728.0
  jvm_memory_committed_bytes{area="heap",id="G1 Old Gen",} 2.5165824E7
  jvm_memory_committed_bytes{area="nonheap",id="Metaspace",} 3.1195136E7
  jvm_memory_committed_bytes{area="nonheap",id="CodeHeap 'non-nmethods'",} 2555904.0
  jvm_memory_committed_bytes{area="heap",id="G1 Eden Space",} 2.097152E7
  jvm_memory_committed_bytes{area="nonheap",id="Compressed Class Space",} 4325376.0
  jvm_memory_committed_bytes{area="nonheap",id="CodeHeap 'non-profiled nmethods'",} 6029312.0
  # HELP process_cpu_usage The "recent cpu usage" for the Java Virtual Machine process
  # TYPE process_cpu_usage gauge
  process_cpu_usage 2.0626097235321682E-4
  # HELP jvm_threads_daemon_threads The current number of live daemon threads
  # TYPE jvm_threads_daemon_threads gauge
  jvm_threads_daemon_threads 12.0
  ```

## Start Prometheus เพื่อ เข้ามาอ่าน metrics ของ Actuator

- เขียน Config Prometheus 
  
  ```yaml
  global:
    scrape_interval:     15s # By default, scrape targets every 15 seconds.
  
    # Attach these labels to any time series or alerts when communicating with
    # external systems (federation, remote storage, Alertmanager).
    external_labels:
      monitor: 'spring-boot-monitor'
  
  # A scrape configuration containing exactly one endpoint to scrape:
  # Here it's Prometheus itself.
  scrape_configs:
  
    - job_name: 'spring-actuator'
  
      metrics_path: '/actuator/prometheus'
  
      scrape_interval: 5s
  
      static_configs:
        - targets: ['host.docker.internal:8080']
  
  ```

- รัน Prometheus ด้วย Docker

  ```shell
  docker run --name prometheus --rm --detach --volume $(pwd)/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro --publish 9090:9090 prom/prometheus:latest --config.file=/etc/prometheus/prometheus.yml
  ```

- ลองเข้า Prometheus ผ่าน [http://localhost:9090/](http://localhost:9090/) ดู

  ![Prometheus Start on http://localhost:9090/](docs/images/prometheus-started.png)

  กรอกเพือ Query ข้อมูล 'cpu'

  ![Prometheus can see CPU](docs/images/prometheus-cpu.png)

  เลือก 'system_cpu_usage' กด `Execute` แล้วไปที่ Tab `Graph`
  
  ![Prometheus saw System CPU Usage for Spring-Boot](docs/images/prometheus-system-cpu-usage.png)

  ![Prometheus saw System CPU Usage Graph](docs/images/prometheus-system-cpu-usage-graph.png)

- stop Prometheus

  ```shell
  docker stop prometheus
  ```

- เขียน Docker-Compose เพื่อจะได้ไม่ต้อง Run Docker ด้วยคำสั่ง ยาว ๆ

  ```yaml
  version: "3.5"
  
  services:
    prometheus:
      image: prom/prometheus:latest
      container_name: prometheus
      ports:
        - 9090:9090
      command:
        - --config.file=/etc/prometheus/prometheus.yml
      volumes:
        - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
  ```

- สั่ง Docker Compose Up เพื่อ Start Prometheus

  ```shell
  docker-compose up -d
  ```

  ลองเข้า [http://localhost:9090/](http://localhost:9090/) ดู

- สั่ง Docker Compose down เพื่อ Stop Service

  ```shell
  docker-compose down
  ```
  
## สร้าง Dockerfile แล้วเอา Spring-Boot ใส่เข้า Docker-Compose เพื่อรันพร้อมกันไปเลย

- สร้าง Jar file แบบ Manual เพื่อ ใช้ในการรัน

  ```shell
  ./mvnw install
  ```
  
  จะได้ Jar file ของ Spring-Boot อยู่ใน `target/` ซึ่งในกรณีนี้จะเป็น File `target/demo-actuator-0.0.1-SNAPSHOT.jar` (ตาม pom.xml)
  
- สร้าง Dockerfile เพื่อ Build Image โดย Copy Jar File ที่ได้จาก Step ก่อนหน้า ไปรันผ่าน JRE ของ Java

  ```dockerfile
  FROM openjdk:8-jre
  ARG JAR_FILE=target/*.jar
  COPY ${JAR_FILE} app.jar
  ENTRYPOINT ["java","-jar","/app.jar"]
  ```

- สั่ง Build Docker Image ผ่านคำสั่ง (tags เป็น bomb0069/spring-boot-actuator)
  
 ```shell
  docker build . --tag bomb0069/spring-boot-actuator   
  ```

  ลอง List Docker Image ที่สร้างขึ้นมาดู

  ```shell
  docker image ls                                                                                     
  REPOSITORY                      TAG            IMAGE ID       CREATED          SIZE
  bomb0069/spring-boot-actuator   latest         80531b75fb44   15 minutes ago   293MB
  ```

- สั่ง Run Docker ที่สร้างไว้ดู

  ```shell
  docker run --name spring-application --detach --rm --publish 8080:8080 bomb0069/spring-boot-actuator 
  ```

  ลองเข้า App ดู ผ่าน [http://localhost:8080/actuator/](http://localhost:8080/actuator/) จะเห็น Endpoints เหมือนตอนเข้ามาตอน Run Spring-Boot

- สั่ง Stop Docker

  ```shell
  docker stop spring-application
  ```

- ปรับ Docker Compose ให้สามารถรัน Prometheus และ Spring-Boot ไปพร้อม ๆ กัน
    
  ```yaml
  version: "3.5"
  
  services:
    prometheus:
      image: prom/prometheus:latest
      container_name: prometheus
      ports:
        - 9090:9090
      command:
        - --config.file=/etc/prometheus/prometheus.yml
      volumes:
        - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      depends_on:
        spring-application # เพิ่มให้รัน spring-application ก่อนค่อยรัน prometheus
  
    spring-application: # Service ใหม่สำหรับ Spring-Boot
      image: bomb0069/spring-boot-actuator
      ports:
        - 8080:8080
  ```

- ปรับ Prometheus config ให้ชี้ไปที่ Service spring-application:8080 แทน
  
  ```yaml
  global:
    scrape_interval:     15s
    
    external_labels:
      monitor: 'codelab-monitor'
  
  scrape_configs:
  
    - job_name: 'spring-actuator'
  
      metrics_path: '/actuator/prometheus'
  
      scrape_interval: 5s
  
      static_configs:
        #เปลี่ยนให้ไปที่ spring-appliction ซึ่งเป็นชื่อ Service ที่กำหนดไว้ที่ docker-compose.yml แทน
        - targets: ['spring-application:8080']
  ```

  ลองเข้า App ที่ [http://localhost:8080/actuator/](http://localhost:8080/actuator/) และ Prometheus ที่ [http://localhost:9090/](http://localhost:9090/) เพื่อตรวจสอบว่า ใช้งานได้ไหม

- ปรับ Dockerfile ให้เป็น Multi-Staged เพื่อให้ Build Image จาก Source ได้เลย โดยที่ตอน Run ยังใช้ Base Image เป็น JRE

  ```dockerfile
  FROM openjdk:8-jdk-alpine as build
  WORKDIR /workspace/app
  
  COPY mvnw .
  COPY .mvn .mvn
  COPY pom.xml .
  COPY src src
  
  RUN ./mvnw install -DskipTests
  
  FROM openjdk:8-jre
  ARG JAR_FILE=/workspace/app/target/*.jar
  COPY --from=build ${JAR_FILE} app.jar
  ENTRYPOINT ["java","-jar","/app.jar"]
  ```

  สั่ง Build Docker เพื่อดูว่ามันทำ Multi-Staged ไหม (Build นานหน่อย)
  
  ```shell
  docker build . --tag bomb0069/spring-boot-actuator   
  
  [+] Building 466.2s (15/15) FINISHED                                                                                                                                        
  => [internal] load build definition from Dockerfile                                                                                                                   0.0s
  => => transferring dockerfile: 37B                                                                                                                                    0.0s
  => [internal] load .dockerignore                                                                                                                                      0.0s
  => => transferring context: 2B                                                                                                                                        0.0s
  => [internal] load metadata for docker.io/library/openjdk:8-jre                                                                                                       2.4s
  => [internal] load metadata for docker.io/library/openjdk:8-jdk-alpine                                                                                                2.2s
  => [internal] load build context                                                                                                                                      0.0s
  => => transferring context: 3.12kB                                                                                                                                    0.0s
  => CACHED [stage-1 1/2] FROM docker.io/library/openjdk:8-jre@sha256:3a20c93f6e8c1fc41a4d1828b608ce6eb1a45767ba15e42f3bf53bc18f9a677d                                  0.0s
  => [build 1/7] FROM docker.io/library/openjdk:8-jdk-alpine@sha256:94792824df2df33402f201713f932b58cb9de94a0cd524164a0f2283343547b3                                   30.3s
  => => resolve docker.io/library/openjdk:8-jdk-alpine@sha256:94792824df2df33402f201713f932b58cb9de94a0cd524164a0f2283343547b3                                          0.0s
  => => sha256:94792824df2df33402f201713f932b58cb9de94a0cd524164a0f2283343547b3 1.64kB / 1.64kB                                                                         0.0s
  => => sha256:44b3cea369c947527e266275cee85c71a81f20fc5076f6ebb5a13f19015dce71 947B / 947B                                                                             0.0s
  => => sha256:a3562aa0b991a80cfe8172847c8be6dbf6e46340b759c2b782f8b8be45342717 3.40kB / 3.40kB                                                                         0.0s
  => => sha256:f910a506b6cb1dbec766725d70356f695ae2bf2bea6224dbe8c7c6ad4f3664a2 238B / 238B                                                                             1.4s
  => => sha256:c2274a1a0e2786ee9101b08f76111f9ab8019e368dce1e325d3c284a0ca33397 70.73MB / 70.73MB                                                                      28.1s
  => => extracting sha256:f910a506b6cb1dbec766725d70356f695ae2bf2bea6224dbe8c7c6ad4f3664a2                                                                              0.0s
  => => extracting sha256:c2274a1a0e2786ee9101b08f76111f9ab8019e368dce1e325d3c284a0ca33397                                                                              1.9s
  => [build 2/7] WORKDIR /workspace/app                                                                                                                                 0.3s
  => [build 3/7] COPY mvnw .                                                                                                                                            0.0s
  => [build 4/7] COPY .mvn .mvn                                                                                                                                         0.0s
  => [build 5/7] COPY pom.xml .                                                                                                                                         0.0s
  => [build 6/7] COPY src src                                                                                                                                           0.0s
  => [build 7/7] RUN ./mvnw install -DskipTests                                                                                                                       432.4s
  => [stage-1 2/2] COPY --from=build /workspace/app/target/*.jar app.jar                                                                                                0.1s
  => exporting to image                                                                                                                                                 0.1s
  => => exporting layers                                                                                                                                                0.1s
  => => writing image sha256:1b6dc0748cf64fd74c349ba578a8889f51391120e9229be34ecfe2b254567e3e                                                                           0.0s
  => => naming to docker.io/bomb0069/spring-boot-actuator
  ```

- ใส่ Context เข้าไปใน `docker-compose.yaml` เพื่อให้สามารถ build ได้ผ่าน `docker-compose build`
  
  ```yaml
  version: "3.5"
  
  services:
    prometheus:
      image: prom/prometheus:latest
      container_name: prometheus
      ports:
        - 9090:9090
      command:
        - --config.file=/etc/prometheus/prometheus.yml
      volumes:
        - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      depends_on:
        - spring-application
  
    spring-application:
      image: bomb0069/spring-boot-actuator
      # เพิ่ม Build Context ให้อ่าน Dockerfile ได้
      build:
        context: .
      ports:
        - 8080:8080
  ```
  ลง Build Docker ผ่าน docker-compose

  ```shell
  docker-compose build
  ```

## Start Grafana เพื่อเอา ข้อมูลจาก Prometheus มา Display บน Dashboard

- รัน Grafana ด้วย Docker

  ```shell
  docker run --name=grafana --detach --rm -p 3000:3000 grafana/grafana
  ```

- เข้า Grafana ผ่าน URL [http://localhost:3000/](http://localhost:3000/) 
  
  ![Grafana Login Page](./docs/images/grafana-login-page.png)
  
  Login ด้วย admin/admin (default user/pass ของ grafana image)

  ![Grafana Login with Admin](./docs/images/grafana-login-with-default-user.png)

  มันจะขึ้นให้เป็น Password ซึ่ง Skip ไปก่อน

  ![Grafana Skip Password change](./docs/images/grafana-skip-password-change.png)
  
  จะเห็นหน้า Home ของ Grafana

  ![Grafana Home Page](./docs/images/grafana-home-page.png)

- เพิ่ม Datasource เพื่อให้ Grafana ไปเอาข้อมูลจาก Prometeus ที่ Start ไว้

  ไปที่ Menu `Configurations` -> `Data sources`

  ![Grafana Configuration -> Data Sources](./docs/images/grafana-config-data-source.png)
  
  เพิ่ม Data Sources ผ่านปุ่ม `Add Data Source`

  ![Grafana Add Data Source](./docs/images/grafana-add-data-source.png)

  Set Prometheus URL ไปที่ [http://host.docker.internal:9090](http://host.docker.internal:9090) ให้ต่อออกมากนอก Container ไปก่อน เดี๋ยวค่อยย้าย ไปที่ docker-compose ค่อยเปลี่ยนเป็นชื่อ Service

  ![Grafana Set Prometheus URL](./docs/images/grafana-set-prometheus-url.png)

  ทดสอบการเชื่อมต่อไปยัง Prometheus ด้วยปุ่ม `Save & Test`
  
  ![Grafana Save and Test Prometheus](./docs/images/grafana-prometheus-save-and-test.png)

  ถ้าเชื่อมต่อ และบันทึกได้ ถูกต้องจะขึ้น `Datasource updated`

  ![Grafana Datasource Updated](./docs/images/grafana-prometheus-datasource-updated.png) 

- Import Dashboard for Spring-Boot to Grafana
  
  ไปที่ปุ่ม `+` เลือก `Create` -> `Import`

  ![Grafana Dashboard Create -> Import](./docs/images/grafana-dashboard-create-import.png)

  เลือก `Upload JSON file`

  ![Grafana Dashboard Upload JSON File](./docs/images/grafana-dashboard-upload-json.png)

  เลือก Upload จาก File [spring-boot-statistics_rev2.json](grafana/spring-boot-statistics_rev2.json) ซึ่งอยู่ใน Folder grafana ของ project นี้

  ![Grafana Select File](./docs/images/grafana-dashboard-select-file.png)

  ```text
  หมายเหตุ : Dashboard ต้นแบบได้มาจาก Grafana Dashboard 
  [หมายเลข 6756 - Spring Boot Statistics by sinsengumi](https://grafana.com/grafana/dashboards/6756)
  แต่เนื่องจาก Micrometer ที่ใช้ในการดึงข้อมูล JVM มีประเด็นของการเปลี่ยน metrics name 
  ทำให้ไม่สามารถใช้งานได้ ผลเลยปรับชื่อ Metrics Name แล้ว Save เก็บไว้ก่อน เพื่อหาหา Publish อีกที
  ```

  เลือก Datasource เป็น `Prometheus`

  ![Grafana Select Datasource](./docs/images/grafana-dashboard-select-datasource.png)

  ก็จะได้หน้า Dashboard สำหรับการดูข้อมูลที่ได้จาก Spring-Boot Actuator แล้ว

  ![Grafana Spring-Boot Statistics](./docs/images/grafana-dashboard-spring-boot-statistics.png)

## Start Grafana ด้วย Docker Compose แบบ Automatic
  
- เอา Grafana เข้า Docker Compose
  
  ```yaml
  version: "3.5"
  
  services:
  
    spring-application:
      image: bomb0069/spring-boot-actuator
      build:
        context: .
      ports:
        - 8080:8080
  
    prometheus:
      image: prom/prometheus:latest
      container_name: prometheus
      ports:
        - 9090:9090
      command:
        - --config.file=/etc/prometheus/prometheus.yml
      volumes:
        - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      depends_on:
        - spring-application
  
    grafana: # เพิ่ม Grafana  เข้ามา
      image: grafana/grafana
      ports:
        - 3000:3000 # เปิด Port 3000
      depends_on:
        - prometheus # ให้ Start Prometheus ด้วย
  ```

  หลังจากนั้น Start ทั้งหมดด้วยคำสั่ง `docker-compose up -d` ซึ่งจะพบว่า Datasource และ Dashboard ที่เราสร้างไว้ หายเกลี้ยงหมดเลย เพราะงั้นเดี๋ยวเราจะ Setup ให้มัน Load ของพวกนี้จาก Config โดยที่เราไม่ต้อง Manual สร้างใหม่ทุกครั้ง

- เริ่มด้วยการกำหนด Admin User/Password
  
  ```yaml
    grafana: # ดูเฉพาะส่วนของ Grafana Service
      image: grafana/grafana
      environment:
        - GF_SECURITY_ADMIN_USER=admin         # Env สำหรับกำหนด admin username
        - GF_SECURITY_ADMIN_PASSWORD=password  # Env สำหรับกำหนด admin password
      ports:
        - 3000:3000
      depends_on:
        - prometheus
  ```
  
  หลังจากนั้นลอง Start Grafana ใหม่ด้วยคำสั่ง `docker-compose up -d grafana` ซึ่งจะพบว่า สามารถ Login ด้วย user/password ที่ต้องการ
  
- Load Data Source แบบ Auto

  Grafana สามารถ Load Config ต่าง ๆ ผ่าน Folder `/etc/grafana/provisioning` [Provisioning Grafana](https://grafana.com/docs/grafana/latest/administration/provisioning/) 
  
  เพราะฉะนั้น เราจะสร้าง `datasource.yml` เพื่อให้ Granafa เข้ามา Load Datasource จาก Config แทนที่จะสร้างด้วย Manual ผ่านหน้า UI โดยใน project นี้เก็บอยู่ใน Folder `./grafana`

  ```yaml
  # ./garfana/datasource.yml
  apiVersion: 1
  
  datasources:
    - name: Prometheus
      type: prometheus
      access: server
      url: http://prometheus:9090
  ```
  
  ทำการ Mount Volume ผ่าน Docker Compose
  
  ```yaml
    grafana:
      image: grafana/grafana
      environment:
        - GF_SECURITY_ADMIN_USER=admin
        - GF_SECURITY_ADMIN_PASSWORD=password
      ports:
        - 3000:3000
      volumes:
        # Mount Volume ให้กับ File datasource.yml
        - ./grafana/datasource.yml:/etc/grafana/provisioning/datasources/datasource.yml
      depends_on:
        - prometheus
  ```

  สั่ง Stop, Remove และ Start Grafana ขึ้นมาใหม่
  
  ```shell
  docker-compose stop grafana
  docker-compose rm grafana
  docker-compose up -d grafana
  ```
  
  Login Grafana และเข้าไปดูที่ Datasource จะเห็น Datasource ที่ ชื่อ Prometheus ทันที

  ![Datasource ที่ ชื่อ Prometheus](./docs/images/grafana-provisioning-prometheus.png)

- Import Dashboard แบบ Auto

  ด้วยหลักการเดียวกันกับ Datasource เราสามารถ import Dashboard ผ่าน config ได้เช่นเดียวกัน โดยที่มีขั้นตอนดังนี้
  
  สร้าง `dashboard.yml`

  ```yaml
  # ./garfana/dashboard.yml
  
  apiVersion: 1
  
  providers:
    - name: 'Spring-Boot Statistics'
      options:
        path: /etc/grafana/provisioning/dashboards
  ```

  Download Dashboard file มาใส่ใน Folder `./garfana/` ในกรณีนี้ ผมใช้ `spring-boot-statistics_rev2.json` จากก่อนหน้านี้ เพียงแต่เข้าไป แก้ให้ Dashboard นี้ไปใช้ Datasource ที่ชื่อ Promethues ตามที่เราสร้างไว้เลย

  ```text
  #  ตัวอย่างที่แก้ใน File spring-boot-statistics_rev2.json
  ...
  "datasource": "${DS_PROMETHEUS}", # เดิมสร้างตัวแปรให้เลือกจากหน้า Web
  "decimals": 1,
  "editable": true,
  ...
  
  # แก้เป็น 
  ...
  "datasource": "Prometheus", # Fix ชื่อของ Datasource เลย
  "decimals": 1,
  "editable": true,
  ...
  ```

  ทำการ Mount Volume ผ่าน Docker Compose

  ```yaml
    grafana:
      image: grafana/grafana
      environment:
        - GF_SECURITY_ADMIN_USER=admin
        - GF_SECURITY_ADMIN_PASSWORD=password
      ports:
        - 3000:3000
      volumes:
        - ./grafana/datasource.yml:/etc/grafana/provisioning/datasources/datasource.yml
        # Mount Volume ให้กับ File dashboard.yml และ JSON File
        - ./grafana/dashboard.yml:/etc/grafana/provisioning/dashboards/dashboard.yml
        - ./grafana/spring-boot-statistics_rev2.json:/etc/grafana/provisioning/dashboards/spring-boot-statistics_rev2.json
      depends_on:
        - prometheus
  ```

  สั่ง Stop, Remove และ Start Grafana ขึ้นมาใหม่

  ```shell
  docker-compose stop grafana
  docker-compose rm grafana
  docker-compose up -d grafana
  ```

  Login Grafana และเข้าไปดูที่ Dashboard `Spring-Boot Statistics` ตามที่เรา Config ได้เลย

  ![Dashboard ที่ชื่อ Spring-Boot Statistics](./docs/images/grafana-provisioning-dashboard.png)

## Reference

- [Spring Boot Actuator: Production-ready Features](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)
- [Spring Boot Actuator metrics monitoring with Prometheus and Grafana](https://www.callicoder.com/spring-boot-actuator-metrics-monitoring-dashboard-prometheus-grafana/)
- [Spring Boot Docker - Multi-Stage Build](https://spring.io/guides/topicals/spring-boot-docker/)
- [Micrometer Issue - when i upgrade to 1.1.3, metric name was changed #1267](https://github.com/micrometer-metrics/micrometer/issues/1267)
- [Provisioning Grafana](https://grafana.com/docs/grafana/latest/administration/provisioning/)
- [Grafana provisioning – How to configure data sources and dashboards](https://keepgrowing.in/tools/grafana-provisioning-how-to-configure-data-sources-and-dashboards/)
