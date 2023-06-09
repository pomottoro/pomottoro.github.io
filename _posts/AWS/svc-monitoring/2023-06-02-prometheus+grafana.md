---
title: "Prometheus 모니터링 시스템"
excerpt: "Prometheus, Grafana"

categories: ["Service Monitoring"]
tags: ["서비스 모니터링", "Prometheus", "Grafana"]

date: 2023-06-02
last_modified_at: 2023-06-02

header:
  overlay_image: assets\images\Thumbnail\infinite.jpg
  overlay_filter: 0.5 
  teaser: assets\images\Thumbnail\prom-grafana-thumb.png
published: true
---

# 💡 [Prometheus](https://prometheus.io/docs/introduction/overview/) 모니터링 시스템

  프로메테우스는 오픈소스 모니터링/알림 시스템입니다. 프로메테우스는 쿠버네티스, 노드, 프로메테우스 자체를 모니터링할 수 있습니다. 쿠버네티스를 지원하고 관리하는 재단인 CNCF에서 프로메테우스 역시 관리하고 있으며, 이 두 도구를 비롯해 시각화를 담당하는 Grafana와 함께 세 도구 조합은 정석적으로 널리 사용되고 있습니다.

## 🏷 프로메테우스 구성 요소

- 프로메테우스는 **시계열**(time series) 데이터를 저장합니다.

- 프로메테우스 서버는 다양한 exporter로부터 각 대상의 **메트릭을 pull**하여 주기적으로 가져오는 **모니터링 시스템**입니다.

  >   쿠버네티스 관련 메트릭을 가져오고 싶다면 쿠버네티스 exporter를, mongoDB 관련 메트릭을 가져오고 싶다면, mongodb exporter를 사용하면 됩니다.

- **Alert manager**는 경고 및 알람을 담당합니다.

- 사용자가 데이터를 질의할 수 있는 Web UI가 존재합니다.

  - 이때 질의 언어로는 **[PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/)**(Prometheus Query Language)을 사용합니다.

**[Grafana](https://prometheus.io/docs/tutorials/visualizing_metrics_using_grafana/)**는 프로메테우스의 구성요소는 아니지만, 프로메테우스에서 권장하는 **시각화 도구**입니다.



- ### 프로메테우스의 기본 구조

![image](https://github.com/pomottoro/comments/assets/58872932/223c7acd-7d77-422d-b4df-e93757bf202a)



## 🏷 쿠버네티스 exporter

 프로메테우스에서 쿠버네티스 관련 메트릭을 가져올 수 있는 쿠버네티스 exporter가 존재합니다. 이때 프로메테우스 exporter는 **[Kube API](https://kubernetes.io/ko/docs/concepts/overview/kubernetes-api/)**를 사용합니다. 대부분의 필요한 정보는 가져올 수 있습니다.

쿠버네티스에서 프로메테우스와 그라파나, 그리고 슬랙과 같은 메신저와의 연동은 보통 다음 그림과 같이 구성합니다.

![제목 없는 다이어그램-페이지-2 drawio](https://github.com/pomottoro/comments/assets/58872932/1b16de3a-59c7-4dbd-9bb2-d823d7811a11)

<br>

---

# 💡 Action Items

- Q. 모니터링 시스템에는 메트릭 수집을 위한 두 가지 방식의 메커니즘이 존재합니다. 바로 Pull 방식과 Push 방식입니다. 프로메테우스는 어떤 방식의 메커니즘을 사용하나요?

  - A. 프로메테우스(Prometheus)는 메트릭을 수집하는 데 주로 'Pull' 방식을 사용합니다.

    Pull 방식이란, 프로메테우스와 같은 모니터링 시스템이 주기적으로 대상 시스템에서 메트릭을 가져오는 방식을 말합니다. 이 방식은 모니터링 시스템이 언제, 어떤 데이터를 수집할지에 대한 완전한 통제권을 가집니다. 또한, 네트워크 구성이나 보안 상의 이유로 대상 시스템이 직접적으로 데이터를 밀어넣을 수 없는 경우에 유용합니다.

    반면, Push 방식은 대상 시스템이 스스로 모니터링 시스템에게 데이터를 전송하는 방식입니다. 이 방식은 주로 불규칙적인 인터벌로 메트릭이 생성되거나, 장시간 동안 메트릭을 보관해야 하는 경우 사용됩니다.

    프로메테우스는 기본적으로 Pull 방식을 사용하지만, Push 방식이 필요한 경우에는 Pushgateway라는 컴포넌트를 통해 Push 방식을 지원합니다. Pushgateway는 짧은 시간동안만 존재하거나 배치 작업과 같이 주기적인 Pull 방식으로 수집하기 어려운 메트릭을 수집하는데 사용됩니다.



- Q. Pull 방식과 Push 방식은 어떻게 다르며, 장단점은 무엇인지, 또한 해당 방식을 사용하는 모니터링 도구는 어떤 것들이 있는지 연구해 보세요.

  - A. **Pull 방식**

    Pull 방식은 모니터링 시스템이 주기적으로 대상 시스템에서 메트릭을 '가져오는' 방식입니다.

    

    장점:
    1. 모니터링 시스템이 언제, 어떤 데이터를 수집할지에 대한 완전한 통제권을 가집니다.
    2. 네트워크 구성이나 보안 상의 이유로 대상 시스템이 직접적으로 데이터를 밀어넣을 수 없는 경우에 유용합니다.

    단점:
    1. 서비스가 중단된 경우, 모니터링 시스템이 데이터를 가져올 수 없어서 데이터 손실이 발생할 수 있습니다.
    2. 주기적인 데이터 수집 방식이므로 불규칙적이거나 빠른 간격의 메트릭을 잘 수집하지 못할 수 있습니다.

    

    사용하는 모니터링 도구:
    1. Prometheus
    2. Zabbix
    3. Nagios

    

    **Push 방식**

    Push 방식은 대상 시스템이 스스로 모니터링 시스템에게 데이터를 '밀어넣는' 방식입니다.

    

    장점:
    1. 불규칙적인 간격이나 빠른 간격으로 메트릭이 생성되는 경우에 유용합니다.
    2. 서비스가 중단되어도 데이터를 모니터링 시스템에 밀어넣었다면, 데이터 손실이 덜 발생합니다.
    3. 서비스가 중단되는 즉시 모니터링 시스템이 알 수 있어, 빠른 알림이 가능합니다.

    단점:
    1. 대상 시스템에 부하를 줄 수 있으며, 메트릭 데이터가 크면 모니터링 시스템이 과부하될 위험이 있습니다.
    2. 네트워크 이슈로 인해 메트릭 전송이 실패할 경우, 데이터 손실이 발생할 수 있습니다.

    

    사용하는 모니터링 도구:
    1. Graphite
    2. InfluxDB
    3. StatsD

<br>

---

<br>