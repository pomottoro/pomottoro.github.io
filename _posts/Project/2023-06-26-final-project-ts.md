---
title: "Final Project Trouble Shooting"
excerpt: "Final Project Trouble Shooting"

categories: Project
tags: [Final Project]

date: 2023-06-26
last_modified_at: 2023-06-26
---

## ERR .1) ECR 레포지토리 이미지 Task 생성 실패

---

  작성한 백엔드 코드를 테스트 해보기 위해 ECR에 이미지 푸쉬 후 콘솔에서 테스트를 해보려 했으나 클러스터에 Task가 생성되지 않는 문제가 발생 했다. 

![ecr](https://github.com/pomottoro/comments/assets/58872932/a6ff9c04-cd50-4035-aee3-8b6bc4fff062)

![task-fail](https://github.com/pomottoro/comments/assets/58872932/33808759-a2c3-4c90-b1de-f4313c760a02)

![task-fail2](https://github.com/pomottoro/comments/assets/58872932/79567198-a064-42b1-b83f-78c10f4a813a)

- Fargate에서는 정상적으로 돌아가는 서비스가 EC2로 생성하면 지속된 에러가 괴롭혔다.
- 확인 해본 결과 생성된 EC2가 서비스에 등록되지 않아 발생한 문제인 것을 확인했다. 

![svc1](https://github.com/pomottoro/comments/assets/58872932/d939b391-ba4f-4173-98fc-0c063d725aa1)

- 용량 공급자 전략으로 클러스터 기본 옵션을 사용 했고 이것이 문제인 것을 확인 했다.

## ERR .2) 생성된 EC2 클러스터에 등록 불가.

---

 시작 유형으로 직접 클러스터 생성 시 만들어지는 EC2를 등록하려 했으나 클러스터에 EC2가 잡히지 않았다. 

![cluster-ec2](https://github.com/pomottoro/comments/assets/58872932/9619141e-a0ad-4f7c-bb52-e1f37ae3e0b8)

- 생성된 EC2에 퍼블릭 IP주소 할당이 안 되어 클러스터에 노출이 안 되는 것을 확인 했다.

![ec2-ip](https://github.com/pomottoro/comments/assets/58872932/bae003b6-b36d-4e1a-bd7a-4c75aef3848b)

- VPC 옵션 중 퍼블릭 IPv4 주소 자동 할당을 활성화 시켜주니 EC2에 퍼블릭 IPv4 주소가 할당 되고 클러스터에 EC2가 잡혔다.

![vpc-ip](https://github.com/pomottoro/comments/assets/58872932/771673d1-6edb-465e-9572-f405b806c552)

![EC2-ip2](https://github.com/pomottoro/comments/assets/58872932/249fabc2-db23-49cc-a10c-0bb59bdd964b)

![cluster-ec2-2](https://github.com/pomottoro/comments/assets/58872932/01203825-9854-44cf-a86a-ebf7c1ed3727)

- 정상적으로 EC2가 활성화 되었다.

![infra](https://github.com/pomottoro/comments/assets/58872932/3c515f54-8dad-4775-a515-10a44a942f17)

![Task-def2](https://github.com/pomottoro/comments/assets/58872932/7b5b9cd1-4d6e-41e4-91e2-bb5d6c6d5d1b)

- 당시 생성 했던 EC2 유형.

![image](https://github.com/pomottoro/comments/assets/58872932/8e863b9f-f7b2-44eb-9f54-af46886fe04e)

- 등록된 인프라 컨테이너

## ERR. 3) Task 생성 실패

---

 EC2 컨테이너는 등록을 했지만 이번엔 Task가 생성이 되지 않았다.

![con-ec2](https://github.com/pomottoro/comments/assets/58872932/f6982594-ab50-4614-a59c-6206c780921b)

- 에러 로그에서 생성된 EC2의 메모리의 크기가 컨테이너보다 작아서 발생 된 문제 라는 것을 확인했고 t3.nano 에서 m6i.large로 바꿨더니 정상적으로 Task가 생성 되었다.

![image](https://github.com/pomottoro/comments/assets/58872932/03f0dca1-9d74-4d2f-b8c2-0dea4e22237e)

![image](https://github.com/pomottoro/comments/assets/58872932/d8a24811-8d6f-43ef-b259-d8f531d93f0f)

## ERR .4) Service 배포 실패

---

정상적으로 Task 생성에는 성공 했지만 서비스 배포에는 실패 했다.

![svc-fail](https://github.com/pomottoro/comments/assets/58872932/720f19ca-7e6c-4b2b-8d8f-dce9226e2f06)

![healthcheckfail](https://github.com/pomottoro/comments/assets/58872932/17947d15-db9a-44e0-8da9-6f0b8a878583)

- health check 에 unhealthy가 떴고, 원인이 타겟 그룹과 ALB 의 포트 매핑이 잘못되어 원활하게 포트를 못 잡고 있던 것을 확인했다.

![alb](https://github.com/pomottoro/comments/assets/58872932/ed9a80b1-2316-47d3-ad96-a86eef2ffd4b)

- ALB (80) - Listener(80) - Target Group (3000) - 컨테이너 (3000) 으로 매핑을 시켜 포트를 다시 잡아줬다.

![sg-inbound](https://github.com/pomottoro/comments/assets/58872932/e62ece4e-5138-47af-808d-05f3e2346bfe)

- 보안그룹의 불필요한 포트들도 다시 정리 해주었다. 

![image](https://github.com/pomottoro/comments/assets/58872932/8d636138-6959-42af-8e60-b83c53e91f44)

## 그리고 마참내 !!

![마참내](https://github.com/pomottoro/comments/assets/58872932/f06482b3-17e9-4969-8854-0988ab9668f4)

![image](https://github.com/pomottoro/comments/assets/58872932/5fea8a39-cfce-4a4b-9e7e-819bad20b811)

![status200](https://github.com/pomottoro/comments/assets/58872932/ce95fe39-36b6-47df-8bbf-44ded47f777c)

![image](https://github.com/pomottoro/comments/assets/58872932/09efac68-332c-4e28-af63-e57521536790)

- Healthy와 status code 200의 반환과 ALB 주소로 접속이 성공 했다.

<br>

---

<br>