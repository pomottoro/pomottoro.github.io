---
title: "Final Project Trouble Shooting"
excerpt: "Final Project Trouble Shooting"

categories: Project
tags: [Final Project]

date: 2023-06-26
last_modified_at: 2023-06-26
---

## ERR .1) ECR 레포지토리 이미지 서비스 생성 실패

---

  작성한 백엔드 코드를 테스트 해보기 위해 ECR에 이미지 푸쉬 후 콘솔에서 테스트를 해보려 했으나 클러스터에 Task가 생성되지 않는 문제가 발생 했다. 

![image](https://private-user-images.githubusercontent.com/58872932/246980479-43a869aa-e86f-474e-a1db-bd2a5f6a0aa3.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJrZXkiOiJrZXkxIiwiZXhwIjoxNjg3Nzg0NTk4LCJuYmYiOjE2ODc3ODQyOTgsInBhdGgiOiIvNTg4NzI5MzIvMjQ2OTgwNDc5LTQzYTg2OWFhLWU4NmYtNDc0ZS1hMWRiLWJkMmE1ZjZhMGFhMy5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBSVdOSllBWDRDU1ZFSDUzQSUyRjIwMjMwNjI2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDIzMDYyNlQxMjU4MThaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01MmYwMmNmMjNlMTc4OTAzMjY4MjIyODFiNzJlMTYzODcyZGFjYjMxNWJjYTAwMTUwYmM1OTAxZTE4MmVhMTVkJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.5LzYY9oRHgE-CpnMNAC2-DdOaBFfw8SKsFDOxHT99fg)

![task-fail](https://github.com/pomottoro/comments/assets/58872932/33808759-a2c3-4c90-b1de-f4313c760a02)

![task-fail2](https://github.com/pomottoro/comments/assets/58872932/79567198-a064-42b1-b83f-78c10f4a813a)

- Fargate에서는 정상적으로 돌아가는 서비스가 EC2로 생성하면 지속된 에러가 괴롭혔다.

![con-ec2](https://github.com/pomottoro/comments/assets/58872932/f6982594-ab50-4614-a59c-6206c780921b)

- 에러 로그에서 생성된 EC2의 메모리의 크기가 컨테이너보다 작아서 발생 된 문제 라는 것을 확인했다.

![infra](https://github.com/pomottoro/comments/assets/58872932/3c515f54-8dad-4775-a515-10a44a942f17)

![Task-def2](https://github.com/pomottoro/comments/assets/58872932/7b5b9cd1-4d6e-41e4-91e2-bb5d6c6d5d1b)

- 당시 생성 했던 EC2 유형.

## ERR .2) 생성된 EC2 클러스터에 등록 불가.

---

EC2 용량을 m6i.large로 늘렸으나 이번엔 EC2를 클러스터에서 못 잡는 결과가 다시 돌아왔다.

![cluster-ec2](https://github.com/pomottoro/comments/assets/58872932/9619141e-a0ad-4f7c-bb52-e1f37ae3e0b8)

- 생성된 EC2에 퍼블릭 IP주소 할당이 안 되어 클러스터에 노출이 안 되는 것을 확인 했다.

![ec2-ip](https://github.com/pomottoro/comments/assets/58872932/bae003b6-b36d-4e1a-bd7a-4c75aef3848b)

- VPC 옵션 중 퍼블릭 IPv4 주소 자동 할당을 활성화 시켜주니 EC2에 퍼블릭 IPv4 주소가 할당 되고 클러스터에 EC2가 잡혔다.

![vpc-ip](https://github.com/pomottoro/comments/assets/58872932/771673d1-6edb-465e-9572-f405b806c552)

![EC2-ip2](https://github.com/pomottoro/comments/assets/58872932/249fabc2-db23-49cc-a10c-0bb59bdd964b)

![cluster-ec2-2](https://github.com/pomottoro/comments/assets/58872932/01203825-9854-44cf-a86a-ebf7c1ed3727)

- 정상적으로 EC2가 활성화 되었다.

## ERR .3) Service 배포 실패

---

하지만 서비스 배포에는 실패 했다.

![svc-fail](https://github.com/pomottoro/comments/assets/58872932/720f19ca-7e6c-4b2b-8d8f-dce9226e2f06)

- 원인이 타겟 그룹과 ALB 의 포트 매핑이 잘못되어 원활하게 포트를 못 잡고 있던것을 발견했다.

![alb](https://github.com/pomottoro/comments/assets/58872932/ed9a80b1-2316-47d3-ad96-a86eef2ffd4b)

- ALB (80) - Listener(80) - Target Group (3000) - 컨테이너 (3000) 으로 매핑을 시켜 포트를 다시 잡아줬다.

![sg-inbound](https://github.com/pomottoro/comments/assets/58872932/e62ece4e-5138-47af-808d-05f3e2346bfe)

- 보안그룹의 불필요한 포트들도 다시 정리 해주었다. 

![image](https://github.com/pomottoro/comments/assets/58872932/8d636138-6959-42af-8e60-b83c53e91f44)

## 그리고 마참내 !!

![마참내](https://github.com/pomottoro/comments/assets/58872932/f06482b3-17e9-4969-8854-0988ab9668f4)

![status200](https://github.com/pomottoro/comments/assets/58872932/ce95fe39-36b6-47df-8bbf-44ded47f777c)

![image](https://github.com/pomottoro/comments/assets/58872932/09efac68-332c-4e28-af63-e57521536790)

- status code 200의 반환과 ALB 주소로 접속이 성공 했다.