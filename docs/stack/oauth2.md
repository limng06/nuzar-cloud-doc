# OAUTH2-SERVICE

```yml
version: '3.8'
networks:
  micro_network:
    driver: overlay
services:
  oauth2_0:
    image: 10.122.0.56:5000/com.nuzar.cloud/auth:1.1.17.release
    networks:
      micro_network:
        aliases:
            - oauth2_0
    hostname: oauth2_0
    healthcheck:
      test: ["CMD-SHELL", "curl -f http://localhost:30100/actuator/health || false"]
      interval: 5s
      timeout: 2s
      retries: 3
    # 端口
    ports:
      - 30100:30100
    environment:
      PROFILES_OPTS: -Dspring.profiles.active=dev
    volumes:
      - "/tmp:/tmp"
    deploy:
      mode: replicated
      replicas: 1
      update_config:
        parallelism: 1
        order: start-first
```
