### Grafana : Docker-compose를 이용한 설치

* 설치 명령어 참고
```bash
mkdir -p grafana/data grafana/config
touch grafana/config/grafana.ini
vi docker-compose.yml
docker-compose up –d
docker-compose logs
docker ps (또는 docker-compose ps 로 실행된 컨테이너 확인)
ss –nltp (실행된 프로세스 포트 확인)
```
<img width="171" alt="image" src="https://user-images.githubusercontent.com/83269234/167578445-5d826899-f1ed-4576-8a4b-a878b4c755d6.png">

<img width="503" alt="image" src="https://user-images.githubusercontent.com/83269234/167578461-c0380eeb-68b1-4965-86ac-277eb904524c.png">

* 접속 확인 \
예 : http://[target ip]:3000/ \
초기 ID/PW : admin/admin



### 샘플 앱 연동 (Spring boot 기반)

* docker-compose.yml에 추가
```bash
java:
    image: joypark9/o11y-sample-app:latest
    container_name: sample-app
    user: "root"
    ports:
      - 8888:8888
    restart: always
    network_mode: host
```

* prometheus.yml에 추가
```bash
- job_name: 'sample_app'
    metrics_path: '/actuator/prometheus'
    static_configs:
    - targets: ['localhost:8888’]
```

* 실행 
```bash
docker-compose up –d
```

* 지표 확인 \
예 : http://[target ip]:8888/actuator/prometheus 

* Grafana.com 제공되는 JVM 대시보드 추가 \
https://grafana.com/grafana/dashboards/4701 \
대시보드 ID : 4701

* Spring boot application 설정 참고
```bash
# Dependency 추가 (build.gradle)
dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-web'
  implementation 'org.springframework.boot:spring-boot-starter-actuator'
  implementation 'io.micrometer:micrometer-registry-prometheus'
  }
```


```bash
# prometheus endpoint 노출 (application.yml)

management:
  endpoints:
    web:
      exposure:
        include: "prometheus"
```

