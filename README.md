# Spring-Boot with Prometheus and Grafana

## Spring-Boot Web with Actuator

- Start Spring-Boot ด้วยคำสั่ง

  ```shell
  ./mvnw spring-boot:run
  ```

- ลองเข้า [http://localhost:8080/actuator](http://localhost:8080/actuator) จะเห็น

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
  
  ปกติ มันจะเปิด expose ในส่วนของ health ออกมาทางหน้า web ส่วนอื่น ๆ จะ expose ออกทาง jmx เท่านั้น ซึ่งถ้าต้องการในในส่วนของ Metrics ออกมาต้อง config เพิ่ม

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