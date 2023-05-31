---
title: "모니터링의 목표와 측정 항목"
excerpt: "서비스 모니터링"

categories: `서비스 모니터링`
tags: ["서비스 모니터링", "메트릭", "SRE", "블랙박스", "화이트박스"]

date: 2023-05-31
last_modified_at: 2023-05-31
---

# 	메트릭이란?

---

> 메트릭은 **시간에 따라 측정한 결과값**입니다. 보다 넓은 의미로는 **비즈니스 개념을 나타내는 수치 측정**을 의미하기도 합니다.
>  예를 들어, 시간당 CPU 사용률, 연간 순매출과 같이 시간이라는 차원이 함께 적용되어야 합니다. 시간이 아닌 다른 차원(예를 들어, 서비스 별 매출)을 기준으로 삼을 수도 있습니다.



# 	모니터링의 목표와 메트릭

---

> 1. 시간을 기준으로 측정되는 주요 메트릭을 최소화하여 **고가용성**을 달성
> 2. 사용량을 추적하여, 배포에 앞서 세운 **가설을 검증하고 개선**
>    - 애자일에서는 “[검증된 학습(Validated learning)](https://www.boldare.com/blog/lean-startup-validated-learning/)을 적용한다”라고 합니다.



## 구글이 이야기하는 모니터링의 목표

- 장기적인 트렌드 분석 (Analyzing long-term trends)
  - 데이터베이스가 얼마만큼의 용량을 차지하며, 얼마나 빨리 용량이 증가하는가?
  - DAU(일간 활성 사용자수)는 얼마나 빨리 증가하는가?
- 시간의 경과 및 실험 그룹 간의 비교 (Comparing over time or experiment groups)
  - 어떤 데이터베이스를 썼을 때 쿼리가 빠른가?
  - 캐시용 노드를 추가했을 때, 캐시 적중률(hit rate)이 얼마나 향상되는가?
  - 지난주보다 사이트가 얼마나 느려졌는가?
- 경고 (Alerting)
  - 인프라의 어떤 부분이 고장 났는가? 혹은 고장 날 수 있는가?

레퍼런스: [https://sre.google/sre-book/monitoring-distributed-systems/](https://sre.google/sre-book/monitoring-distributed-systems/)



## Azure 서비스에서 측정하는 메트릭의 주요 예

- 캐시 사용률
- CPU, Memory
- 인스턴스의 개수
- 연결 유지

레퍼런스: [https://docs.microsoft.com/ko-kr/azure/data-explorer/using-metrics](https://docs.microsoft.com/ko-kr/azure/data-explorer/using-metrics)

# 모니터링 구분

---

## 블랙박스 모니터링과 화이트박스 모니터링

- 블랙박스 모니터링은 CPU/메모리/스토리지 등 인프라 수준의 모니터링에 유용합니다. 쿠버네티스 시스템의 경우, 클러스터 정상 작동 여부 등 쿠버네티스 컴포넌트 그 자체를 모니터링하는 것도 블랙박스 모니터링에 해당합니다. 그러나, 애플리케이션이 왜 오류를 내는지는 알 수 없습니다.

- 화이트박스 모니터링은 시스템 내부의 측정 기준에 따라 모니터링하는 것을 의미합니다. 예를 들면, HTTP 요청, 500 에러의 발생 횟수, 레이턴시 등이 이에 해당합니다. 단순히 현상만 바라보는 것이 아닌, 현상이 발생한 근거를 알 수 있는 모니터링 방식입니다.

## 계층에 따른 모니터링 구분

- 쿠버네티스
  - 노드 > 클러스터 컴포넌트 > 파드
- ECS
  - 클러스터 > 서비스 > 태스크
- EC2: 인스턴스에 대한 메트릭만 볼 수 있습니다.
- Lambda: 함수에 대한 메트릭만 볼 수 있습니다.

#*(왼쪽으로 갈수록 상위 계층입니다)*

## Proxy 서버의 메트릭

>  애플리케이션 서버(WAS)의 앞단에 캐시 서버 혹은 인증 서버, 로드 밸런서와 같은 Proxy 서버가 존재한다면, 이는 애플리케이션 서버와는 별도로 모니터링해야 합니다. 애플리케이션 서버가 각 노드의 컴퓨팅 자원을 모니터링하는 데에 중점을 두었다면, Proxy 서버, 그중에서도 HTTP 라우팅을 다루고 있는 서버는 요청 그 자체와 연관된 메트릭을 위주로 모니터링해야 합니다.

> ###  HTTP 요청/응답 관련 모니터링 대상
>
> - 쿠버네티스의 경우 **인그레스**
> -  AWS 생태계에서는 **Application Load Balancer**



---



# 계층별 메트릭과 메트릭 구분

## 메트릭 한눈에 보기

|            | 컴퓨팅 유닛 관련 메트릭                                      | 요청/응답 관련 메트릭                                        | 스케일링 관련 메트릭                                         |
| :--------: | ------------------------------------------------------------ | ------------------------------------------------------------ | :----------------------------------------------------------- |
|  **k8s**   | - CPU 사용량 (utilization) <br />- 메모리 사용량<br />- 네트워크 in/out <br />- 디스크 사용량  (노드 및 파드 별) | - etcd latency <br />- ingress <br />- 요청 개수 <br />- 요청 latency <br />- 에러율 | - 디플로이먼트 상황                                          |
|  **ECS**   | - CPU 사용량 <br />- 메모리 사용량 <br />- 네트워크 in/out  (클러스터 및 서비스 별) | 해당 사항 없음 (ALB와 사용하여 분석해야 함)                  | - 서비스 개수 <br />- (원하는/실행 중인/보류 중인) 작업 개수 <br />- 컨테이너 인스턴스 개수 |
|  **EC2**   | - CPU 사용량 <br />- 네트워크 in/out <br />- 네트워크 패킷 in/out <br />- 디스크 읽기/쓰기 (바이트), 작업 개수 <br />- CPU 크레딧 사용량, 밸런스 | - 상태 검사 실패 횟수                                        | 해당 사항 없음                                               |
| **Lambda** | 해당 사항 없음                                               | - 호출 개수 <br />- 실행 시간 <br />- 에러 개수 및 성공률 <br />- throttles <br />- async delivery failures <br />- IteratorAge | - 동시 실행 횟수                                             |
|  **ALB**   | 해당 사항 없음                                               | - 대상 응답 시간 <br />- 요청 개수 <br />- 응답 코드 개수 (2xx, 4xx, 5xx) <br />- 대상 연결 오류 <br />- 거부된 연결 개수 합계 <br />- 대상 TLS 협상 오류 <br />- 클라이언트 TLS 협상 오류 <br />- 활성 연결 개수 <br />- 새 연결 개수 <br />- 처리된 바이트 <br />- 사용된 Load Balancer 용량 단위 | 해당 사항 없음                                               |

# 레퍼런스 모음

- [Application Load Balancer](https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/load-balancer-cloudwatch-metrics.html) 공식 문서
- [ECS](https://docs.aws.amazon.com/ko_kr/AmazonECS/latest/developerguide/cloudwatch-metrics.html) 공식문서
- [AWS 키 메트릭](https://www.datadoghq.com/blog/aws-monitoring/)
- [ELB 키 메트릭](https://www.datadoghq.com/blog/top-elb-health-and-performance-metrics/)
- [EC2 키 메트릭](https://www.datadoghq.com/blog/ec2-monitoring/)
- [AWS 키 메트릭](https://www.datadoghq.com/blog/aws-fargate-metrics/)

---



# 사이트 신뢰성 엔지니어링 (SRE) 관련 메트릭

---

  **“사이트 신뢰성 엔지니어링(Site Reliability Engineering, SRE)?”**

> 어떤 서비스(웹사이트)가 온전히 사용자에게 전달될 수 있도록 가용성을 극대화하는 기술/문화

 CPU 및 메모리, 사용량 등을 파악하는 것 외에도 네트워크 요청에 따른 응답 상태, 요청의 횟수나 시간 등도 중요한 지표가 될 수 있습니다.



---

## 구글의 SRE 조직에서 정의한 “[네 가지 황금 시그널(The Four Golden Signals)](https://sre.google/sre-book/monitoring-distributed-systems/#xref_monitoring_golden-signals)” SRE 모니터링의 주요 측정 항목

- ### 대기 시간 (Latency)

  - 대기 시간은 **서비스가 요청에 응답하는 데 걸리는 시간**을 나타냅니다. 핵심은 지속 시간뿐만 아니라 성공적인 요청의 대기 시간과, 실패한 요청의 대기 시간을 구별하는 데에도 중점을 두어야 합니다.

- ### 트래픽 (Traffic)

  - 트래픽은 서비스에 대한 수요 측정입니다. 대표적인 예로는, **초당 HTTP 요청 수**가 있습니다.

- ### 오류 (Errors) 

  -  오류는 **`실패한 요청/전체 요청`** 의 비율로 측정됩니다. 대부분의 경우 이러한 실패는 명시적이지만(예: HTTP 500 오류) 암시적일 수도 있습니다(예: "결과 없음"이라는 메시지를 본문으로 전달하는 HTTP 200 응답).

- ### 포화 수준 (Saturation)

  -  포화는 서비스 또는 시스템 리소스를 “얼마나 가득 채워서 사용하는가”로 설명할 수 있습니다. 전형적인 예로는 **과도한 CPU 자원 사용**이 있습니다. CPU 자원이 부족하면, 스로틀링을 초래하고 결과적으로 응용 프로그램의 성능을 저하시킵니다.

---

## 주요 모니터링 패턴

### USE 패턴

- USE 패턴은 모든 리소스에 대한 사용률(**U**tilization), 포화도(**S**aturation), 오류(**E**rrors)를 체크하는 패턴을 의미합니다.

### RED 패턴

- RED 패턴은 비율(Rate), 오류(Errors) 및 기간(Duration)을 주요 메트릭으로 정의하는 패턴입니다.
