---
title: "서비스 수준 목표"
excerpt: "서비스 수준 목표"

categories: ["Service Monitoring"]
tags: ["서비스 모니터링", "SLI", "SLO", "SLA"]

date: 2023-06-05
last_modified_at: 2023-06-05
---

# ✔ 서비스 수준 관련 용어

---

## SLI (서비스 수준 척도, Service Level Indicator)

서비스 수준을 판단할 수 있는 몇 가지를 정량적으로 측정한 값으로, 주요 SLI는 다음과 같습니다.

- 응답 속도: 요청에 대한 응답이 리턴되기까지의 시간
- 에러율: 전체 요청 수 대비
- 처리량(throughput): 초당 처리할 수 있는 요청 수
- 가용성: 서비스가 사용 가능한 상태로 존재하는 시간의 비율
- 내구성: 데이터 저장이 중요한 목적인 서비스의 경우 특히 중요

## SLO (서비스 수준 목표, Service Level Objectives)

SLI에 의해 측정된 서비스 수준의 목푯 값, 또는 일정 범위의 값을 의미합니다. 즉 SLO는 다음과 같이 표현됩니다.

```
SLI ≤ 목표치
최소값 ≤ SLI ≤ 최댓값
```

## SLA (서비스 수준 협약, Service Level Agreements)

"SLO를 달성하지 못하면 어떻게 되는지"를 적어놓은 약속입니다. SRE가 직접 관여하지는 않습니다.

<br>

---

<br>

# ✔ 지표 설정과 주요 목표 설정

---

## 지표 설정

서비스나 시스템에 있어 중요한 지표를 판단하는 근거가 있어야 합니다. 적절한 SLI의 선정은 시스템의 분류에 따라 달라질 수 있습니다.

- 사용자가 직접 대면하는 시스템
  - 보통 프론트엔드에 해당하며, 이 경우 **가용성, 응답 시간, 처리량**이 중요합니다.
- 저장소 시스템
  - **응답 시간, 가용성, 내구성**이 중요합니다.
- 빅데이터 시스템
  - 데이터 파이프라인이 이에 해당하며, **처리량**, 그리고 **엔드포인트 간 응답 시간**이 중요합니다.

## 척도 수집

측정 원본을 합산하거나, 평균을 내거나 하는 방법이 있겠지만, 대부분의 경우 **분포**가 중요합니다. 일부 요청이 빠르게 처리되어도, 나머지 요청이 균일하게 느리다면 실제로 서비스는 느린 것으로 간주할 수 있습니다. 평균은 이러한 흐름의 변화를 감지하기 어렵습니다.

## 척도의 표준화

SLO를 설정할 때, 주요 SLI의 정의를 표준화시키면 편리합니다. 예를 들어 다음과 같습니다.

- 집계 간격: 1분
- 집계 범위: 하나의 클러스터에서 수행되는 모든 태스크
- 측정 빈도: 매 10분
- 집계에 포함할 요청: 전체 HTTP GET 요청

## 목표 설정하기

다음과 같은 목표를 설정할 수 있습니다. 다음은 성능에 중점을 둔 SLO입니다.

- GET 호출의 90%는 1ms 이내에 수행되어야 한다.
- GET 호출의 99%는 10ms 이내에 수행되어야 한다.
- GET 호출의 99.9%는 100ms 이내에 수행되어야 한다.

<br>

---

<br>