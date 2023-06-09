---
title: "부하 테스트 계획"
excerpt: "성능 테스트"

categories: ["Performance Test"]
tags: ["부하 테스트", "Throughput", "Latency", "Load test tools"]

date: 2023-06-08
last_modified_at: 2023-06-08

header:
  overlay_image: assets\images\Thumbnail\infinite.jpg
  overlay_filter: 0.5 
  teaser: assets\images\Thumbnail\Sprint-thumbnail.png
published: true
---

# 부하 테스트 계획

---

- ## Latency에 중점을 둔 SLO 예시

  - GET 호출의 90%는 1ms 이내에 완료해야 합니다.

  - GET 호출의 99%는 10ms 이내에 완료해야 합니다.

  - GET 호출의 99.9%는 100ms 이내에 완료해야 합니다.


- ## Throughput에 중점을 둔 SLO 예시


결국 Throughput은 순간적으로 요청이 치솟는 피크(peak) 트래픽에서의 처리량을 바탕으로 합니다.

예를 들어, 1일 기준으로 RPS를 계산하고자 할 때, 다음과 같은 시나리오를 생각해 볼 수 있습니다.

- DAU(Daily Active User: 1일 접속자 수): 5만 명
- 1명당 평균 접속 횟수: 20회
- 1일 평균 접속 수에 대한 피크 트래픽 배율: 3배 (보통 평균의 2~3배를 곱합니다)
- 안전 계수 (얼마만큼 넉넉하게 프로비저닝 할 것인가): 3배

이 경우 하루가 총 86400초이므로, 다음과 같이 RPS를 생각해 볼 수 있습니다.

```
50000 x 20 / 86400 x 3 x 3 = 104RPS (약 100RPS)
```

이를 바탕으로 도출한 SLO는 다음과 같습니다.

- 서비스는 100RPS를 달성해야 합니다

<br>

---

<br>

# 부하 테스트 도구

---

## AWS에서 제공 하는 도구

- [AWS에서 소개하는 부하 테스트 도구](https://aws.amazon.com/ko/blogs/korea/how-to-loading-test-based-on-aws/)
- **[AWS에서의 분산 로드 테스트](https://aws.amazon.com/solutions/implementations/distributed-load-testing-on-aws/)**: 이 서비스는 AWS에서 제공하는 솔루션으로, 사용자가 AWS 환경에서 대규모 로드 테스트를 수행할 수 있게 해줍니다. 이 도구는 Amazon EC2 인스턴스를 사용하여 테스트를 분산시키고, Amazon CloudWatch를 통해 실시간으로 테스트 결과를 모니터링할 수 있습니다.

## 그 외의 다른 Load Test 도구들

1. **[k6](https://k6.io/)**: k6는 개발자 친화적인 오픈 소스 부하 테스트 도구로, 코드 중심의 접근 방식을 사용하여 테스트를 작성하고 실행합니다. 이 도구는 테스트를 자바스크립트로 작성하며, 사용자 시나리오를 쉽게 모델링하고 로드 테스트를 자동화하는 데 유용합니다.
2. **[Artillery.io](https://www.artillery.io/)**: Artillery는 강력하고 유연한 오픈 소스 부하 테스트 도구로, HTTP, WebSocket, Socket.io 등 다양한 프로토콜을 지원합니다. 이 도구는 테스트를 YAML 또는 JSON으로 작성하며, 사용자 정의 메트릭, 고급 시나리오 모델링 등 다양한 기능을 제공합니다.
3. **[JMeter](https://jmeter.apache.org/)**: 가장 인기 있는 오픈 소스 툴 중 하나로, 다양한 프로토콜을 사용하여 다양한 기술의 성능을 테스트할 수 있습니다. 이 도구는 웹 HTTP/HTTPS, SOAP 및 Rest 서비스, FTP, JDBC를 통한 데이터베이스 등을 테스트할 수 있습니다.
4. **[Taurus](https://gettaurus.org/)**: 로드 테스트 도구라기보다는 다른 솔루션 위의 래퍼로 작동하며, 성능 테스트를 실행하는 복잡성을 숨깁니다. Taurus는 테스트를 YAML로 작성할 수 있게 해줍니다. 
5. **[Locust](https://locust.io/)**: 사용자 부하 테스트 도구로, 응답 시간을 캡처하는 데 도움이 됩니다. Python을 사용하여 테스트 시나리오를 생성할 수 있습니다. 
6. **[Gatling](https://gatling.io/)**: Gatling은 대규모 부하 테스트를 위한 강력한 도구로, 복잡한 시나리오를 쉽게 설계하고 실행할 수 있습니다. 
7. **[LoadRunner](https://www.microfocus.com/en-us/products/loadrunner-load-testing/overview)**: Micro Focus의 LoadRunner는 업계에서 가장 널리 사용되는 로드 테스트 도구 중 하나로, 다양한 프로토콜과 애플리케이션 유형을 지원합니다.
8. **[NeoLoad](https://www.neotys.com/neoload/overview)**: Neotys의 NeoLoad는 웹 및 모바일 애플리케이션의 성능 테스트를 위한 도구로, 사용자 친화적인 GUI와 함께 복잡한 애플리케이션의 성능 테스트를 쉽게 만들어줍니다. 

<br>

---

<br>
