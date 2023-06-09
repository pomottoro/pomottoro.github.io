---
title: "Prometheus, Grafana 설치"
excerpt: "Prometheus, Grafana"

categories: ["Sprint"]
tags: ["서비스 모니터링", "Prometheus", "Grafana"]

date: 2023-06-02
last_modified_at: 2023-06-02
<<<<<<< HEAD

header:
  overlay_image: assets/images/Thumbnail/infinite.jpg
  overlay_filter: 0.5 
  teaser: assets/images/Thumbnail/Sprint-thumbnail.png
published: true
=======
>>>>>>> parent of df92387f (대규모 업데이트)
---

# 💡 Prometheus, Grafana 설치

---

## Prometheus Operator를 이용하여 설치하기

minikube를 이용해 클러스터를 생성합니다. `--nodes` 옵션을 이용하여 여러 개의 노드를 만들 수 있습니다.

```bash
minikube start --nodes=3
```

![image](https://github.com/pomottoro/comments/assets/58872932/8eb7783c-a7e5-404d-b3f5-b0acb0546ec9)

- 터미널에서 미니큐브로 클러스터를 생성 하는 모습.

 여러 개의 노드를 만들면, 첫 번째는 master, 나머지 두 개의 노드는 worker 노드로 구성됩니다. 여러 개의 노드를 구성할 경우, 워커 노드에는 실제로 실행되는 애플리케이션을 배치하며, 마스터 노드는 클러스터 관리 그 자체에 집중하게 합니다.

> Q. 모니터링의 관점에서 워커 노드에 애플리케이션을 별도로 배치하는 것이 왜 유리할까요?
>
> - A. 워커 노드에 애플리케이션을 별도로 배치하는 것이 모니터링 관점에서 유리한 이유
>
>   1. **분리된 리소스 관리:** 각 워커 노드에서 실행되는 애플리케이션은 해당 노드의 리소스(CPU, 메모리, 스토리지 등)를 독립적으로 사용합니다. 이로 인해 애플리케이션 간의 리소스 경합을 방지하고, 각 애플리케이션의 성능을 독립적으로 관리하고 최적화할 수 있습니다.
>
>   2. **장애 격리:** 애플리케이션을 별도의 워커 노드에 배치하면, 한 애플리케이션에서 발생하는 문제가 다른 애플리케이션에 영향을 미치는 것을 방지할 수 있습니다. 예를 들어, 한 애플리케이션에서 발생한 버그나 과도한 리소스 사용 등이 해당 노드 내에서 격리되어, 전체 시스템에 영향을 미치는 것을 최소화합니다.
>
>   3. **세부 모니터링:** 애플리케이션을 별도의 워커 노드에 배치하면, 각 애플리케이션의 세부적인 동작 상황을 더욱 정확하게 모니터링할 수 있습니다. 즉, 애플리케이션 별로 트래픽, CPU 사용량, 메모리 사용량 등의 메트릭을 따로 모니터링하고 분석할 수 있습니다.
>
>   4. **유연한 스케일링:** 각 워커 노드에 애플리케이션을 별도로 배치하면, 애플리케이션의 요구사항에 따라 각각의 워커 노드를 독립적으로 확장하거나 축소할 수 있습니다. 이렇게 함으로써 필요한 리소스를 보다 효율적으로 사용할 수 있습니다.
>
>   이러한 이유들로 인해, 큰 규모의 시스템에서는 워커 노드에 애플리케이션을 별도로 배치하는 것이 유리할 수 있습니다. 그러나 이 방식은 리소스를 추가로 사용하므로, 작은 규모의 시스템에서는 비효율적일 수 있습니다. 따라서 애플리케이션의 규모와 요구사항에 따라 적절한 배치 전략을 선택해야 합니다.
>

```bash
$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

minikube-m02
type: Worker
host: Running
kubelet: Running

minikube-m03
type: Worker
host: Running
kubelet: Running
```

![image](https://github.com/pomottoro/comments/assets/58872932/3e6d1607-66d4-42ea-b294-ebd58cc727f2)

- 미니큐브 상태

---

## 🏷 Prometheus Operator Quick Start

[Prometheus 공식 문서](https://prometheus-operator.dev/docs/prologue/quick-start/)

1. kube-prometheus를 클론하세요.

   ```bash
   git clone https://github.com/prometheus-operator/kube-prometheus.git
   ```

   ![image](https://github.com/pomottoro/comments/assets/58872932/4895e6da-e3e7-4cfd-93d6-99b166471a17)

2. 해당 디렉토리로 이동하여 kube-prometheus를 배포하세요.

   ```bash
   # STEP 1
   kubectl create -f manifests/setup
   # STEP 2
   until kubectl get servicemonitors --all-namespaces ; do date; sleep 1; echo ""; done
   # STEP 3
   kubectl create -f manifests/
   ```

   1. `manifests/setup` 안의 워크로드를 전부 실행합니다.
   2. 일단 모든 모든 서비스 모니터(servicemonitor)가 구동될 때까지 기다립니다.
   3. `manifest/` 안의 워크로드를 전부 실행합니다.

3. Prometheus Operator 관련 서비스 및 워크로드가 실행되기 전까지 기다립니다.

4. 관련 서비스를 로컬호스트에 포트 포워딩으로 노출할 수 있습니다.

   1. 프로메테우스 노출

      ```bash
      kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090
      ```

      [http://localhost:9090](http://localhost:9090/)로 접속해서 프로메테우스에 접속할 수 있습니다.

      ![image](https://github.com/pomottoro/comments/assets/58872932/e9f1651a-ca23-45f0-ab02-363dfb5a8f2e)

      ![image](https://github.com/pomottoro/comments/assets/58872932/99d83194-146e-4835-ae2f-2a9d2f2d0a0e)
   
      

   2. 그라파나 노출
   
      ```bash
      kubectl --namespace monitoring port-forward svc/grafana 3000
      ```
   
      [http://localhost:3000](http://localhost:3000/)으로 접속해서 그라파나에 접속할 수 있습니다. (초기 사용자 이름과 비밀번호는 `admin`/`admin`입니다)
      
      ![image](https://github.com/pomottoro/comments/assets/58872932/1c61838b-abdf-413b-8e67-179789b34919)
      
      ![image](https://github.com/pomottoro/comments/assets/58872932/bcc6935a-b87a-417e-8c8e-3fbd42316263)
      
      - localhost:3000 으로 접속시 나오는 화면
      
      ![image](https://github.com/pomottoro/comments/assets/58872932/6d9f38eb-f9ec-4db2-9382-1f10cd46faca)
      
      - admin/admin 으로 로그인 후 화면

<br>

---

<br>