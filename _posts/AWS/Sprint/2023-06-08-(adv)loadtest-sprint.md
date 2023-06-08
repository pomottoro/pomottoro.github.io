---
title: "(Advanced)AWS EC2와 k6를 이용한 성능 테스트"
excerpt: "성능 테스트"

categories: ["Sprint"]
tags: ["성능 테스트", "load test", "k6", "Prometheus", "Grafana"]

date: 2023-06-08
last_modified_at: 2023-06-08
---

# 성능 테스트를 위한 서비스 모니터링

---

## 성능 테스트를 위한 서비스 모니터링

 Prometheus와 Grafana는 오픈소스로 제공되는 모니터링 서비스로서 많은 개발자들에게 큰 인기를 얻고 있습니다. 이와 마찬가지로 k6 역시 오픈소스로 제공되는 툴로서 소프트웨어 엔지니어링 커뮤니티에서 아주 큰 인기를 얻고 있습니다. 특히 개발자 친화적으로 설계가 되어 있고 스크립트 언어로 javascript를 사용하므로 개발자들이 성능 테스르를 쉽게 작성하고 유지할 수 있습니다. 

---

## Setup & Preparation

### 1. k6확장 리포지토리 clone

```bash
git clone git@github.com:grafana/xk6-output-prometheus-remote.git  # ssh 접속
# 또는
git clone https://github.com/grafana/xk6-output-prometheus-remote.git  # HTTPS 접속
```

### 2. 성능테스트를 위한 tests 폴더를 clone해 온 폴더 안에 생성

```bash
~grafana/xk6-output-prometheus-remote.git 
$ mkdir /tests
```

### 3. tests 폴더 안에 k6 성능 테스트를 위한 basic_k6_test.js 파일 생성

```javascript
// basic_k6_test.js
import http from 'k6/http';
import { describe } from 'https://jslib.k6.io/expect/0.0.4/index.js';
import { check, sleep } from 'k6';
export default function () {
    describe('Basic k6 test', () => {
        let res = http.get('https://test.k6.io');
        check(res, {
            'is status 200': (r) => r.status === 200
        });
        sleep(1);
    });
}
```

### 4. (상위폴더로 돌아가기) Dockerfile 생성

```dockerfile
FROM golang:1.17-alpine as builder
WORKDIR "$GOPATH"/src/go.k6.io/k6
COPY . .
RUN apk --no-cache add git &&\
    go install -trimpath go.k6.io/xk6/cmd/xk6@latest &&\
    xk6 build --with github.com/grafana/xk6-output-prometheus-remote@latest &&\
    cp k6 "$GOPATH"/bin/k6
FROM alpine:3.13
RUN apk add --no-cache ca-certificates && \
    adduser -D -u 12345 -g 12345 k6
COPY --from=builder /go/bin/k6 /usr/bin/k6
USER 12345
ENTRYPOINT ["k6"]
CMD [""]
```

> 제일 첫 줄에 FROM 부분에 버전이 레포와 일치하지 않으면 빌드 자체가 안된다. 항상 최신 버전으로 고쳐 써야함.
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/e00f3078-fd67-4ec8-93dc-b5cd3ebdd389)![image](https://github.com/pomottoro/comments/assets/58872932/67dd31a9-4a56-43c5-8d4f-c5f7cc793a7a)
>
> - 이런 에러와 마주하게 된다.
>
> ``` dockerfile
> RUN apk --no-cache add git &&\
>     go install -trimpath go.k6.io/xk6/cmd/xk6@latest &&\
>     xk6 build --with github.com/grafana/xk6-output-prometheus-remote@latest &&\
>     cp k6 "$GOPATH"/bin/k6
>     # 해결하기 위해 이 여러 커맨드가 묶여 있는 부분을 아래 처럼 쪼갰다.
> #########################################
> 
> RUN apk --no-cache add git
> RUN go install -trimpath go.k6.io/xk6/cmd/xk6@latest
> RUN xk6 build --with github.com/grafana/xk6-output-prometheus-remote@latest
> RUN cp k6 "$GOPATH"/bin/k6
> ```
>
> - 쪼개고 나서 보니
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/bb75baa5-5dc7-4cb1-b45f-3738c79c93ec)
>
> ``` dockerfile
> FROM golang:1.17-alpine as builder
> # 이 부분의 1.17 버전이 문제가 되어
> FROM golang:1.18-alpine as builder
> # 1.18버전으로 고쳐주니 정상적으로 잘 작동한다.
> ```
>
> 

위 도커파일을 실행하기 전에 꼼꼼히 읽어보시기 바랍니다. 내용을 이해하고 나서 실행을 해야 본인의 결과물이 됩니다. 도커파일의 내용은 "프로메테우스와 새로운 XK6 바이너리 데이터들을 빌드한다"는 내용입니다. 자세한 내용은 [도커허브](https://docs.docker.com/engine/reference/builder/)와 [k6 공식사이트](https://k6.io/docs/extensions/#xk6-makes-custom-binaries)를 참고하시기 바랍니다.

### 5. docker-compose.yaml 작성

```yaml
version: '3.7'
volumes:
  prometheus-data:
  grafana-data-storage:
services:
  k6-prom:
    build: .
    depends_on:
      - grafana
    volumes:
      - .:/app:delegated
    environment:
      - K6_PROMETHEUS_RW_SERVER_URL=http://prometheus:9090/api/v1/write
    command: run --vus 150 --duration 30s -o xk6-prometheus-rw /app/tests/basic_k6_test.js
  prometheus:
    image: prom/prometheus:latest
    depends_on:
      - grafana
    ports:
      - 9000:9090
    volumes:
      - ./prometheus.yaml:/etc/prometheus/prometheus.yaml
      - prometheus-data:/prometheus
    command: --web.enable-remote-write-receiver --config.file=/etc/prometheus/prometheus.yaml
  grafana:
    image: grafana/grafana:latest
    volumes:
      - grafana-data-storage:/var/lib/grafana
    ports:
      - 4000:4000
    environment:
      - GF_SERVER_HTTP_PORT=4000
```

> 기존 코드에 경로문제 등의 오류가 많아 수정 했다.

### 6. prometheus.yaml 작성

```yaml
# prometheus.yml
global:
  scrape_interval: 30s
  scrape_timeout: 10s
scrape_configs:
  - job_name: services
    metrics_path: /metrics
    static_configs:
      - targets:
          - 'prometheus:9090'
```

## 시작하기

### 1. 도커 파일 실행

```bash
sudo docker compose up
```

> ![image](https://github.com/pomottoro/comments/assets/58872932/4255f720-d68c-48dd-b554-9902d76cc12c)
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/685dd696-3875-4a38-ba0e-2b0bd21bc7e2)

### 2. 프로메테우스 연결 - http://localhost: 9000 

![image](https://github.com/pomottoro/comments/assets/58872932/81853411-57cf-46ac-bd77-38359f1b4f9e)

**프로메테우스**에서 타깃을 조사해 보면 k6에 관한 메트릭이 표시됩니다. 이 중에서 원하는 내용을 추가 할 수 있습니다.

**add Panal 키**와 Execute 버튼으로 내용을 추가해 보세요. 

![image](https://github.com/pomottoro/comments/assets/58872932/0061d22b-1c13-47b4-a638-631269b3939b)

![image](https://github.com/pomottoro/comments/assets/58872932/21a4af89-e982-4ab5-b2bb-50f8a055bdc8)

### 3. 그라파나 접속 http://localhost:4000/

- 최초 접속 시 id/비번 모두 admin
- configuration(톱니바퀴 모양) 찾아서 데이터 소스 찾기

![image](https://github.com/pomottoro/comments/assets/58872932/0752c8ce-c10b-45ee-b6f2-5eb5d5e222bd)

![image](https://github.com/pomottoro/comments/assets/58872932/ec51be71-a84a-4918-b105-51da0d5ba4aa) 

반드시 URL을 http://prometheus:9090으로 작성하여야 합니다. 

![image](https://github.com/pomottoro/comments/assets/58872932/a3a6a5c8-55c3-4d2e-b617-d2a30e75ab09)

### 4. 대시보드 확인 : 그라파나의 대시보드 확인

![image](https://github.com/pomottoro/comments/assets/58872932/c9e7763e-b5a2-4013-9072-6407dfac3b93)

![image](https://github.com/pomottoro/comments/assets/58872932/de7caaa8-152a-4e5f-a52f-f5dbf9e6abbc)

<br>

---

<br>