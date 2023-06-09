---
title: "모니터링 시스템 구축"
excerpt: "nginx ingress controller"

categories: ["Sprint"]
tags: ["서비스 모니터링", "nginx ingress"]

date: 2023-06-05
last_modified_at: 2023-06-05

header:
  overlay_image: assets/images/Thumbnail/infinite.jpg
  overlay_filter: 0.5 
  teaser: assets/images/Thumbnail/Sprint-thumbnail.png
published: true
---

# Getting Started

---

## 1. nginx 인그레스 컨트롤러 설치

- `minikube start` 명령어 실행

  ```bash
  minikube start
  ```

![image](https://github.com/pomottoro/comments/assets/58872932/82a1f6de-2f56-4a6e-b473-5d1f7349a821)

- [Helm](https://artifacthub.io/packages/helm/ingress-nginx/ingress-nginx)을 이용해서 설치합니다.  

``` bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

![image](https://github.com/pomottoro/comments/assets/58872932/77604c6b-a334-4155-9e10-bd49a523b953)

- Version 명령어로 설치 확인

```bash
helm version
```

![image](https://github.com/pomottoro/comments/assets/58872932/675b56a9-8368-43b7-ab87-1bad4fce6c85)

---

nginx 인그레스 컨트롤러가 프로메테우스용 메트릭을 노출해야 하므로, `helm install` 과정에서 반드시 설정해야 하는 옵션이 있습니다.

[레퍼런스](https://artifacthub.io/packages/helm/ingress-nginx/ingress-nginx#prometheus-metrics), [레퍼런스2](https://kubernetes.github.io/ingress-nginx/user-guide/monitoring/#re-configure-nginx-ingress-controller)를 참고해서, 어떤 옵션을 설정해 주어야 프로메테우스에서 nginx의 메트릭을 볼 수 있는지 고민해 보세요. 다음과 같이 환경설정을 지정할 수 있습니다.

```
$ helm install ingress-nginx ingress-nginx/ingress-nginx \
--set 환경설정키=값 \
--set 환경설정키=값
```

프로메테우스 웹 UI (`http://localhost:9090`)에서  ingress-nginx-controller가 Target으로 설정되면 성공입니다.

---

> ```bash
> helm install ingress-nginx ingress-nginx/ingress-nginx \
> --namespace monitoring \
> --set controller.metrics.enabled=true \
> --set controller.metrics.serviceMonitor.enabled=true \
> --set controller.metrics.serviceMonitor.additionalLabels.release="prometheus"
> ```
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/8bc27820-3296-4ee7-8b9f-c2f0773e4339)
>
> - `helm ls` 명령어로 리스트 확인 가능
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/08327675-19df-4761-86b8-388d07b1e93b)

- 포트포워드 명령어로 프로메테우스 노출

``` bash
kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090
```

![image](https://github.com/pomottoro/comments/assets/58872932/69ae54a7-698a-42dd-ac39-c0b142b4b31e)

- localhost:9090으로 접속 확인

![image](https://github.com/pomottoro/comments/assets/58872932/ab074e6a-0fe2-4923-9a10-3555430a24e1)

- 프로메테우스 웹 UI에 타겟으로 잡힌 모습 확인.

---

<br>

## 2. 인그레스 생성

[레퍼런스](https://kubernetes.io/ko/docs/concepts/services-networking/ingress/)를 참고해서, 인그레스를 만들고 적용합니다.

> [cozserver (v2)](https://github.com/cs-devops-bootcamp/sprint-k8s-rollout-reference)의 디플로이먼트 및 서비스를 배포하고, 인그레스를 만들어서 nginx를 통해 서비스에 접근하게 합니다.

---

> `kubectl apply -f [myname].yml` 배포 명령어로 k8s yaml 파일 배포
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/218794f2-bdc4-4a38-a28d-95afb40ca83e)![image](https://github.com/pomottoro/comments/assets/58872932/e3872054-9370-4c9b-b333-bf97fb155a84)
>
> - `kubectl get` 명령어를 사용하여 배포 상태 확인
>
> ``` bash
> kubectl get pods
> kubectl get svc
> ```
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/cd26b62d-e898-454c-82af-e1254a17a9e5)
>
> - CLI 입력후 나오는 결과 물중 Ingress 부분만 복사해서 yaml 파일을 따로 만들어준다.
>
> ``` yaml
>   apiVersion: networking.k8s.io/v1
>   kind: Ingress
>   metadata:
>     name: cozserver
>     namespace: default
>   spec:
>     ingressClassName: nginx
>     rules:
>       - host: localhost
>         http:
>           paths:
>             - pathType: Prefix
>               backend:
>                 service:
>                   name: cozserver
>                   port:
>                     number: 80
>               path: /
> ```
>
> 
>
> - 내용 수정 후 재 배포
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/ad142cae-fa98-4311-bec6-09216fa7f525)
>
> - 정상적으로 배포 생성 된 모습이다.



## 3. 인그레스 접속 로그가 찍히는지 프로메테우스를 통해 확인

![image](https://github.com/pomottoro/comments/assets/58872932/e65ecb45-a138-4b5d-ad73-db15c47d7c58)
![image](https://github.com/pomottoro/comments/assets/58872932/2ab20721-4622-48e6-aec2-51baa544da8d)

<div class="notice" markdown="1">
### SRE의 네 가지의 황금 시그널을 보기 위해 필요한 메트릭의 종류는 다음과 같습니다.
- 트래픽, 오류
  - nginx_ingress_controller_requests
- 대기 시간
  - nginx_ingress_controller_request_duration_seconds_count
  - nginx_ingress_controller_request_duration_seconds_bucket
- 포화 수준
  - node_cpu_seconds_total
</div>

프로메테우스가 지원하는 PromQL을 통해 다음과 같이 복잡한 지표를 표현할 수도 있습니다.

예) 1시간 동안 인그레스 컨트롤러를 통해 들어온 요청의 누적 비율

```sql
sum(rate(
  nginx_ingress_controller_requests[1h]
)) 
by (ingress)
```

예) 1시간 동안 모든 요청 중 400번대 에러의 비율

```sql
sum(rate(
  nginx_ingress_controller_requests{
    status=~"4.."
  }[1h]
))
by (ingress) /
sum(rate(
  nginx_ingress_controller_requests[1h]
))
by (ingress)
```

![image](https://github.com/pomottoro/comments/assets/58872932/a8e4e7f2-ec38-4cd0-a74d-d4f75934e1f6)

## 4. Grafana의 대시보드 살펴보기

그라파나에 접속하고 로그인을 완료하면, 다양한 대시보드가 미리 준비되어 있습니다. 각 Panel에서 사용하는 메트릭을 살펴보고, 어떤 PromQL을 통해 쿼리하는지 알아보세요.

Panel의 제목을 클릭하고 Edit 버튼을 누르면, 다음과 같이 쿼리문을 확인할 수 있습니다.

![image-20230605190706804](https://github.com/pomottoro/comments/assets/58872932/fb1232be-6eef-45d7-b0ef-4db0b337257f)

또한 Create - Import 메뉴를 이용하면, 기존에 커뮤니티에서 만들어놓은 대시보드를 손쉽게 Import 할 수 있습니다.

- [NGINX Ingress controller](https://grafana.com/grafana/dashboards/9614)
- [쿠버네티스 올인원 모니터링 대시보드](https://grafana.com/grafana/dashboards/13770)

# 💡 참고 레퍼런스

그라파나 대쉬보드 : [https://github.com/kubernetes/ingress-nginx/tree/main/deploy/grafana/dashboards](https://github.com/kubernetes/ingress-nginx/tree/main/deploy/grafana/dashboards)

오픈 텔레메트리 데모 : [https://github.com/open-telemetry/opentelemetry-demo](https://github.com/open-telemetry/opentelemetry-demo)

그라파나 대쉬보드 : [https://github.com/prometheus-operator/kube-prometheus/blob/main/manifests/grafana-dashboardDatasources.yaml](https://github.com/prometheus-operator/kube-prometheus/blob/main/manifests/grafana-dashboardDatasources.yaml)

<br>

---

<br>

