# Spring-Boot with Prometheus and Grafana

## Spring-Boot Web with Actuator

- ปกติ มันจะเปิด jmx เท่านั้น ถ้าต้องการ เปิดเข้าจากหน้า Web ต้องเพิ่ม Config เข้าไป

```properties
# src/main/resources/application.properties

management.endpoints.web.exposure.include=health,metrics
```
