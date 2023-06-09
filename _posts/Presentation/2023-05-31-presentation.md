---
title: "23-05-31 Presentation"
excerpt: "서비스 모니터링"

categories: ["Presentation"]
tags: ["서비스 모니터링"]

date: 2023-05-31
last_modified_at: 2023-05-31
<<<<<<< HEAD

header:
  overlay_image: assets/images/Thumbnail/infinite.jpg
  overlay_filter: 0.5 
  teaser: assets/images/Thumbnail/Presentation-thumbnail.jpg
published: true
=======
>>>>>>> parent of df92387f (대규모 업데이트)
---

#  람다를 모니터링하려는 경우, 메트릭을 활용해 어떤 질문이 나올 수 있을까요? [레퍼런스(Lambda 키 메트릭)](https://www.datadoghq.com/blog/key-metrics-for-monitoring-aws-lambda/)를 읽고, 어떤 질문을 해결할 수 있는지 알아봅시다. (힌트: 레퍼런스 문서에서 how many, how much, how long으로 검색해 보세요.)

---

## 							기능 사용률 및 성능 지표

> | 이름               | 설명                                                         | 메트릭 유형  | 유효성             |
> | ------------------ | ------------------------------------------------------------ | ------------ | ------------------ |
> | 지속               | 함수 실행의 경과 시간(밀리초)                                | 가동률       | CloudWatch 및 로그 |
> | 청구 기간          | 실행 시간은 100ms 블록 단위로 청구되며 다음 전체 블록으로 반올림됩니다. | 가동률       | 로그               |
> | 메모리 크기        | 함수에 할당된 메모리 양(MB)                                  | 리소스: 활용 | 로그               |
> | 사용된 최대 메모리 | 호출에 사용되는 최대 메모리 양(MB)                           | 리소스: 활용 | 로그               |
> | 오류               | 함수 오류로 인해 실패한 호출 수                              | 작업: 오류   | CloudWatch         |
>
> ---

## 							관찰할 메트릭: 기간 및 청구 기간

>  함수의 기간 또는 실행 시간을 모니터링하면 비용을 관리하고 최적화할 수 있는(또는 최적화해야 하는) 함수를 결정하는 데 도움이 될 수 있습니다. 느린 코드 실행은 콜드 스타트 또는 코드 복잡성의 결과일 수 있습니다. 기능이 타사 서비스 또는 기타 AWS 서비스에 의존하는 경우 네트워크 지연 시간과 같은 요인도 실행 시간에 영향을 미칠 수 있습니다. Lambda는 또한 함수가 종료되고 시간 초과 오류가 발생하기 전에 함수가 실행될 수 있는 시간(15분)을 제한합니다. 모니터링 기간은 이 실행 임계값에 도달하려는 시기를 확인하는 데 도움이 됩니다.
>
> ![모든 기능의 기간 보기](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_duration_overview.png?auto=format&fit=max&w=847)
>
>  기간은 함수의 각 호출 실행 시간을 측정하는 반면 청구 기간은 가장 가까운 100ms로 반올림한 실행 시간을 측정합니다. AWS Lambda 요금은 청구 기간과 함수의 메모리 크기를 기반으로 합니다.
>
>  함수의 기간을 청구 기간과 비교하여 실행 시간을 줄이고 비용을 낮출 수 있는지 확인할 수 있습니다. 예를 들어 이 함수의 로그를 살펴보겠습니다.
>
> ```fallback
> REPORT RequestId: f1d3fc9a-4875-4c34-b280-a5fae40abcf9	Duration: 102.25 ms	Billed Duration: 200 ms	Memory Size: 128 MB	Max Memory Used: 58 MB	Init Duration: 2.04 ms	
> ```
>
>  함수의 기간은 102ms이지만 청구 기간(비용 지불)은 200ms입니다. 기간이 일정하다면(예: 약 102ms) 기간(및 청구 기간)을 줄이기 위해 메모리를 더 추가할 수 있습니다. 예를 들어 함수의 메모리를 192MB로 늘리고 기간이 98ms로 떨어지면 청구 기간은 100ms가 됩니다. 청구 기간 동안 200ms 블록 대신 100ms 블록에 있기 때문에 요금이 적게 [부과](https://aws.amazon.com/lambda/pricing/) 됩니다 .
>
> 간단한 예를 사용했지만 이 두 지표를 모니터링하면 특히 수백 개의 기능에서 대량의 요청을 관리하는 경우 비용을 관리하는 데 도움이 될 수 있습니다.

## 							관찰할 메트릭: 오류

>  Lambda에서 발생할 수 있는 오류에는 호출과 함수의 두 가지 유형이 있습니다. [호출 오류에는](https://docs.aws.amazon.com/lambda/latest/dg//API_Invoke.html#API_Invoke_Errors) 서비스에 함수를 호출할 수 있는 적절한 권한이 없거나 계정의 동시 실행 제한에 도달한 경우가 포함될 수 있습니다. 함수 오류는 코드에 문제가 있거나(예: 예외 발생, 구문 문제) 함수가 시간 초과된 경우에 발생할 수 있습니다. Lambda의 **오류** 메트릭은 함수 오류로 인해 실패한 호출 수를 계산합니다. 지표에는 다른 AWS 서비스의 호출 오류 또는 내부 서비스 오류가 포함되지 않습니다.
>
>  애플리케이션이 여러 Lambda 함수에 의존하는 경우 오류 수를 모니터링하면 어떤 함수가 문제를 일으키는지 정확히 파악하는 데 도움이 될 수 있습니다.
>
>  ![AWS Lambda 함수별 실행 오류](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_execution_errors_by_function.png?auto=format&fit=max&w=847)
>
>  함수 성능을 모니터링하는 것 외에도 동시성과 다른 서비스가 함수를 호출하는 방식을 모니터링하는 것이 중요합니다. Lambda는 호출 및 동시성을 관리하기 위한 다양한 지표를 제공합니다.
>
>  ---



## 							관찰할 지표: 예약되지 않은 동시 실행

> 계정에서 사용 가능한 총 동시 실행 수와 동일한 **예약 되지 않은 동시 실행을** 추적할 수도 있습니다 . 함수에 대해 동시성을 예약한 경우 이 지표는 사용 가능한 총 동시 실행에서 예약된 동시성을 뺀 값과 같습니다.
>
> 예약되지 않은 동시 실행 지표를 동시 실행 지표와 비교하여 더 많은 워크로드 중에 함수가 나머지 동시성 풀을 소진하는 시기를 모니터링할 수 있습니다.
>
> ![예약되지 않은 동시 실행 AWS Lambda 함수의 동시 실행 비교](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_unreserved_concurrent.png?auto=format&fit=max&w=847)
>
>  위의 그래프는 예약되지 않은 동시성의 급증과 사용 가능한 대부분의 동시성을 사용하는 하나의 함수를 보여줍니다. 이는 함수에 너무 많은 요청을 보내는 업스트림 서비스 때문일 수 있습니다. 다른 기능이 효율적으로 작동하기에 충분한 동시성을 확보하기 위해 이 기능에 대한 동시성을 예약할 수 있습니다. 그러나 Lambda는 예약된 동시성을 모두 사용하는 경우 함수를 조절한다는 점에 유의해야합니다.

## 							관찰할 지표: 사용된 메모리 크기 및 최대 메모리

>  Lambda는 함수의 컴퓨팅 성능을 제한하지만 함수에 대한 메모리 또는 메모리 **크기를** [AWS Lambda 제한](https://docs.aws.amazon.com/lambda/latest/dg//limits.html) 내에서 할당할 수 있습니다 . 함수의 기간(및 청구 기간)은 메모리 양에 부분적으로 영향을 받기 때문에 메모리 사용량을 모니터링하는 것이 중요합니다. 메모리가 부족하면 실행 시간이 느려집니다. 반면에 필요한 것보다 더 많은 메모리를 할당했을 수 있습니다. 두 시나리오 모두 비용에 영향을 미치므로 메모리 사용량을 모니터링하면 처리 능력과 실행 시간 간의 균형을 맞추는 데 도움이 됩니다.
>
>  아래와 같이 `CloudWatch` 로그에서 이 데이터를 볼 수 있습니다.
>
>  ```fallback
>  REPORT RequestId: f1d3fc9a-4875-4c34-b280-a5fae40abcf9	Duration: 20.21 ms	Billed Duration: 100 ms	Memory Size: 512 MB	Max Memory Used: 58 MB	Init Duration: 2.04 ms	
>  ```
>
>  함수가 사용 가능한 메모리의 일부만 사용함( **max memory used** )을 볼 수 있습니다. 이 문제가 지속적으로 발생하는 경우 함수에 할당된 메모리 양을 조정하여 **<u>비용을 줄일 수</u>** 있습니다.
>
>  ![AWS Lambda 함수의 메모리 사용량과 메모리 크기](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_available_memory_max_memory.png?auto=format&fit=max&w=847)
>
>  또는 함수의 실행 시간이 예상보다 오래 걸리는 경우 함수가 요청을 처리하는 데 사용 가능한 메모리가 충분하지 않음을 나타낼 수 있습니다. 함수의 메모리 사용량은 지속적으로 메모리 크기에 도달합니다.
>
>  ---



## 							주시해야 할 메트릭: 호출

> 호출 수를 모니터링하면 애플리케이션 활동과 기능이 전반적으로 수행되는 방식을 이해하는 데 도움이 될 수 있습니다. 함수가 여러 지역에 있는 경우 호출 횟수를 사용하여 함수가 효율적으로 사용되는지 확인할 수 있습니다. 예를 들어 어떤 함수가 가장 자주 호출되는지 빠르게 확인하고 대기 시간을 개선하기 위해 리소스를 이동하거나 로드 밸런싱을 수정해야 하는지 평가할 수 있습니다. [Lambda@Edge](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-at-the-edge.html) 와 같은 서비스는 고객에게 더 가까운 리전에서 코드를 자동으로 실행하여 대기 시간을 개선할 수 있습니다.
>
> ![AWS Lambda 함수별 그룹화 된 호출](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_invocations_by_region.png?auto=format&fit=max&w=847)
>
> 호출이 갑자기 감소하면 함수 또는 연결된 AWS 서비스에 문제가 있음을 나타낼 수 있습니다. 추가 문제 해결을 위해 이 드롭을 Lambda 로그와 같은 다른 데이터 포인트와 연관시킬 수 있습니다.
>
> ---



## 							주의해야 할 메트릭: 데드 레터 오류

> 비동기식으로 호출되거나 이벤트 소스 매핑에서 호출되는 함수는 DLQ(배달 못한 편지 대기열)를 사용하여 삭제된 이벤트, 즉 처리할 수 없고 재시도에 실패한 이벤트를 처리합니다. **DLQ 지표**는 Lambda가 배달 못한 편지 대기열에 이벤트를 보낼 수 없는 횟수를 추적합니다. 이 측정항목이 증가하면 함수의 [권한](https://docs.aws.amazon.com/lambda/latest/dg/lambda-permissions.html) 에 문제가 있거나 다운스트림 서비스가 제한될 수 있습니다.
>
> ---



## 							경고할 메트릭: 동시 실행

> 함수는 동시에 여러 프로세스를 실행하거나 **동시에 실행**할 수 있습니다 . 이 메트릭을 모니터링하면 기능이 풀의 모든 동시성을 사용하는 시기를 추적할 수 있습니다. 이 메트릭이 특정 임계값에 도달하면 알리는 **경고를 생성**할 수도 있습니다.
>
> ![AWS Lambda의 동시 실행 모니터링](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_concurrent_executions_by_function.png?auto=format&fit=max&w=847)
>
> 위의 예에서 특정 함수에 대한 실행이 급증하는 것을 볼 수 있습니다. 앞에서 언급했듯이 공통 실행 풀에서 동시성을 예약하여 함수의 동시 실행을 제한할 수 있습니다. 함수가 너무 많은 요청을 동시에 처리하지 않도록 해야 하는 경우에 유용할 수 있습니다.

## 							경고할 메트릭: 스로틀

>  요청이 들어오면 함수는 예약되지 않은 동시성 풀(예약된 동시성이 없는 경우) 또는 예약된 동시성 풀(사용 가능한 경우)에서 끌어와 수요를 충족하도록 확장됩니다. 풀이 소진되면 Lambda는 해당 지역의 모든 기능을 조절하기 시작하고 들어오는 모든 요청을 거부합니다. 기능의 용량과 효율성을 사전에 모니터링할 수 있도록 기능 제한에 대해 경고해야 합니다.
>
> ![AWS Lambda 함수에 대한 스로틀 모니터링](https://imgix.datadoghq.com/img/blog/key-metrics-for-monitoring-aws-lambda/lambda_throttles_by_function.png?auto=format&fit=max&w=847)
>
> 지속적인 제한은 기능이 처리할 수 있는 것보다 더 많은 요청이 있고 기능에 대한 용량이 충분하지 않음을 나타낼 수 있습니다. 애플리케이션에 중요한 기능이 있는 경우 예약된 동시성을 할당할 수 있습니다. 이렇게 하면 함수가 들어오는 요청을 처리하기에 충분한 동시 실행을 갖도록 하는 데 도움이 됩니다. 또는 처리하는 요청 수를 제한하려는 경우 함수에 대한 동시성을 예약할 수 있습니다. 스로틀을 모니터링하면 함수의 동시성 제한에 여전히 도달하는지(그리고 어디에서) 확인하는 데 도움이 됩니다. 동시성 풀을 지속적으로 소진하는 경우 계정에서 리전당 동시 실행 한도 [증가를 요청할 수 있습니다.](https://docs.aws.amazon.com/lambda/latest/dg/limits.html)
>
>  함수가 [호출된](https://www.datadoghq.com/blog/key-metrics-for-monitoring-aws-lambda/#invocation-metrics) 방식에 따라 Lambda는 스로틀의 실패를 다르게 처리합니다. 예를 들어 Lambda는 동기 소스(예: API 게이트웨이)에서 제한된 요청을 재시도하지 않습니다. 함수의 동시성을 관리할 때 이 점을 염두에 두는 것이 중요합니다.
>
> ---

#  쿠버네티스에 어떤 파드가 Pending 상태에 머물러있다면, 어떤 계층부터 살펴보아야 할까요? 이 경우는 파드가 Running 상태인데 잘 작동하지 않는 경우랑은 어떻게 다른가요? (서비스는 연결되어 있다고 가정합니다)

---



## 							파드가 계속 pending 상태인 경우

> ### 파드 디버깅하기
>
> 파드 디버깅의 첫 번째 단계는 파드를 살펴 보는 것입니다. 다음의 명령어를 사용하여 파드의 현재 상태와 최근 이벤트를 점검합니다.

```bash
kubectl describe pods ${POD_NAME}
```

>  파드의 상태에 따라 디버깅을 계속합니다. 파드가 `Pending` 상태로 멈춰 있는 경우 <u>노드에 스케줄 될 수 없음을 의미</u>합니다. 일반적으로 이것은 어떤 유형의 **리소스가 부족**하거나 **스케줄링을 방해하는 다른 요인** 때문입니다. 파드를 스케줄 할 수 없는 사유에 대한 스케줄러의 메세지가 있을 것이고 다음과 같은 사유가 있을 수 있습니다.

> - **리소스가 부족한 경우**: 사용자 클러스터의 CPU 나 메모리가 고갈되었을 수 있습니다. 이러한 경우, 파드를 삭제하거나, 리소스 요청을 조정하거나, 클러스터에 노드를 추가해야 합니다. 참고 할 문서: [컴퓨트 자원](https://kubernetes.io/ko/docs/concepts/configuration/manage-resources-containers/)
> - **`hostPort`를 사용하고 있는 경우**: 파드를 `hostPort`에 바인딩할 때, 파드가 스케줄링될 수 있는 장소 수 제한이 존재합니다. 대부분의 경우 `hostPort`는 불필요하므로, 파드를 노출하기 위해서는 서비스(Service) 오브젝트 사용을 고려해볼 수 있습니다. `hostPort`가 꼭 필요하다면 클러스터의 노드 수 만큼만 파드를 스케줄링할 수 있습니다.
> - 파드의 컨테이너 이미지를 확인합니다. 이미지가 존재하지 않거나 다운로드되지 않은 경우, 파드는 Pending 상태로 남아 있습니다.
> - 파드의 이벤트를 확인합니다. 이벤트를 사용하여 감지할 수 있는 일반적인 시나리오는 노드에 할당될 수 없는 파드를 생성하는 경우입니다.



## 					파드가 계속 waiting 상태인 경우

>  파드가 `Waiting` 상태에서 멈춘 경우는, 파드가 워커 노드에 스케줄링되었지만 해당 노드에서 실행될 수 없음을 의미합니다. 파드가 `Waiting` 상태에서 멈추는 가장 흔한 원인은 이미지 풀링(pulling)에 실패했기 때문입니다. 다음의 3가지 사항을 확인합니다.
>
>  - 이미지 이름이 올바른지 확인
>  - 해당 이미지를 저장소에 푸시했는지 확인
>  - 이미지가 풀 될 수 있는지 확인하기 위해 수동으로 이미지를 풀 해본다. 예를 들어, PC에서 도커를 사용하는 경우, `docker pull <image>` 명령을 실행한다.
>
>  ---



### 			🎵 참고자료 :  [파드 디버깅 하기](https://kubernetes.io/ko/docs/tasks/debug/debug-application/debug-pods/), [예시:Pending상태의 파드 디버깅하기](https://kubernetes.io/ko/docs/tasks/debug/debug-application/debug-running-pod/#%EC%98%88%EC%8B%9C-pending-%EC%83%81%ED%83%9C%EC%9D%98-%ED%8C%8C%EB%93%9C-%EB%94%94%EB%B2%84%EA%B9%85%ED%95%98%EA%B8%B0)



---
