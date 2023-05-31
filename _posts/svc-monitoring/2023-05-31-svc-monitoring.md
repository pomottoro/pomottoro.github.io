---
title: "서비스 모니터링"
excerpt: "서비스 모니터링"

categories: [서비스 모니터링]
tags: [서비스 모니터링]

date: 2023-05-31
last_modified_at: 2023-05-31
---





# 

# 계층별 메트릭과 메트릭 구분

## 메트릭 한눈에 보기

|            | 컴퓨팅 유닛 관련 메트릭                                      | 요청/응답 관련 메트릭                                        | 스케일링 관련 메트릭                                         |
| :--------: | ------------------------------------------------------------ | ------------------------------------------------------------ | :----------------------------------------------------------- |
|  **k8s**   | - CPU 사용량 (utilization) <br />- 메모리 사용량<br />- 네트워크 in/out <br />- 디스크 사용량  (노드 및 파드 별) | - etcd latency <br />- ingress <br />- 요청 개수 <br />- 요청 latency <br />- 에러율 | - 디플로이먼트 상황                                          |
|  **ECS**   | - CPU 사용량 <br />- 메모리 사용량 <br />- 네트워크 in/out  (클러스터 및 서비스 별) | 해당 사항 없음 (ALB와 사용하여 분석해야 함)                  | - 서비스 개수 <br />- (원하는/실행 중인/보류 중인) 작업 개수 <br />- 컨테이너 인스턴스 개수 |
|  **EC2**   | - CPU 사용량 <br />- 네트워크 in/out <br />- 네트워크 패킷 in/out <br />- 디스크 읽기/쓰기 (바이트), 작업 개수 <br />- CPU 크레딧 사용량, 밸런스 | - 상태 검사 실패 횟수                                        | 해당 사항 없음                                               |
| **Lambda** | 해당 사항 없음                                               | - 호출 개수 <br />- 실행 시간 <br />- 에러 개수 및 성공률 <br />- throttles <br />- async delivery failures <br />- IteratorAge | - 동시 실행 횟수                                             |
|  **ALB**   | 해당 사항 없음                                               | - 대상 응답 시간 <br />- 요청 개수 <br />- 응답 코드 개수 (2xx, 4xx, 5xx) <br />- 대상 연결 오류 <br />- 거부된 연결 개수 합계 <br />- 대상 TLS 협상 오류 <br />- 클라이언트 TLS 협상 오류 <br />- 활성 연결 개수 <br />- 새 연결 개수 <br />- 처리된 바이트 <br />- 사용된 Load Balancer 용량 단위 | 해당 사항 없음                                               |



---

# 레퍼런스 모음

- [Application Load Balancer](https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/load-balancer-cloudwatch-metrics.html) 공식 문서
- [ECS](https://docs.aws.amazon.com/ko_kr/AmazonECS/latest/developerguide/cloudwatch-metrics.html) 공식문서
- [AWS 키 메트릭](https://www.datadoghq.com/blog/aws-monitoring/)
- [ELB 키 메트릭](https://www.datadoghq.com/blog/top-elb-health-and-performance-metrics/)
- [EC2 키 메트릭](https://www.datadoghq.com/blog/ec2-monitoring/)
- [AWS 키 메트릭](https://www.datadoghq.com/blog/aws-fargate-metrics/)
