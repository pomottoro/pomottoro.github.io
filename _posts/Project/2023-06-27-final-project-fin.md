---
title: "Final Project를 마치며"
excerpt: "Final Project"

categories: Project
tags: [Final Project]

date: 2023-06-27
last_modified_at: 2023-06-28
---

# Final Project - Task Management

---

우리 팀은 Final Project 의 주제를 자유 주제로 정했고, 회의 끝에 배민라이더스처럼 사용자의 작업을 관리 하는 Task Management Tool을 만들어 보는 것을 주제로 정했다.

<br>

![image](https://github.com/pomottoro/comments/assets/58872932/5ca71f38-6a98-43fb-9dd6-20634b63e582)

<br>

## Task Management Tool 기능 요구사항

작업의 진행상황을 체계적으로 관리할 수 있도록 업무를 분배하고 진행상황을 관리할 수 있는 시스템을 구축해야한다.

- 사용자는 개인 정보를 이용해 **로그인**을 할 수 있어야 한다.
- 사용자는 새로운 Task를 생성하고, 생성한 Task를 **수정하거나 삭제, 조회**할 수 있어야 한다.
- 사용자는 Task의 진행 상황을 **대기 중, 진행 중, 완료** 등으로 관리할 수 있어야 한다.
- 사용자는 새로운 Task가 추가되거나, 업무 상태가 변경될 때 사용자에게 **알림**을 보낼 수 있어야 한다.
- 특정 업무를 사용자에게 할당하고, 할당된 업무를 사용자가 확인할 수 있어야 한다.

<br>

## 인프라 요구사항

- 사용되는 애플리케이션들은 컨테이너로 구동되어야합니다.
- 시스템 전반에 가용성, 내결함성, 확장성, 보안성이 고려된 서비스들이 포함되어야 합니다.
- 하나 이상의 컴퓨팅 유닛에 대한 CI/CD 파이프라인이 구성되어야합니다.
- 시스템 메트릭 또는 저장된 데이터에 대한 하나 이상의 시각화된 모니터링 시스템이 구축되어야합니다.

<br>

![image](https://github.com/pomottoro/comments/assets/58872932/0a91f884-e9c4-4621-939c-235eadda5e39)

 이 프로그램은 다수의 인원이 한 작업에 대해 역할을 나누어 프로젝트를 진행해야 할 때 이용할 수 있습니다. 사용자는 어떠한 내용의 Task를 생성할 수 있고, 특정 작업에 대한 담당자(등록 된 사용자)를 지정합니다. 담당자는 그 작업을 진행하고, 담당자는 진행 상황이 바뀔 시 Task의 진행도를 바꿀 수 있고 진행도 혹은 Task 의 내용이 수정될 때마다 사용자에게 메일로 특정 로그 내용을 전송하여 보다 쉽게 수정된 내용을 확인할 수 있도록 합니다.

<br>

# DDD

---

<br>

![image](https://github.com/pomottoro/comments/assets/58872932/f7e64f47-7c87-4663-8c19-fefc12cf7d0f)

초기 구상한 DDD는 사용자가 로그인 후 작업관리 툴에 접속 하게 되면 작업 변경 사항이 Log 로 쌓일 수 있도록 구상 해봤다.

<br>

# DB

---

<br>

![image](https://github.com/pomottoro/comments/assets/58872932/261793c1-0d75-4f7d-a6ee-fe3dd362b8ba)

이후 RDS에 들어갈 데이터형식과 DynamoDB로 들어갈 Data형식을 분리 해줬다.

<br>

---

# 리소스 아키텍쳐

---

<br>

![image](https://github.com/pomottoro/comments/assets/58872932/8de2dea0-c850-400c-8558-3433bc3da2fa)

## 필요한 리소스

- 백엔드 개발: **JavaScript (Node.js)**
- 데이터베이스 관리: **Amazon Aurora (MySQL) / DynamoDB**
- 사용자 인증 및 권한 관리: **Lambda / API gateway / DynamoDB**
- 알림 시스템: **Amazon Event Bridge**
- 컨테이너화: **Docker**
- CI/CD 파이프라인: **GitHub Actions**
- 모니터링: **Prometheus, Grafana, CloudWatch 경보**

<br>

# 리소스 선정 이유

---

<br>

![image](https://github.com/pomottoro/comments/assets/58872932/db7846fd-3027-410f-b4c7-65c835252c92)

<br>

## 리소스 선정이유 - EC2

![image](https://github.com/pomottoro/comments/assets/58872932/8243b436-4845-482f-9ada-cdb58dc4215a)

참고자료 : [https://www.youtube.com/watch?v=-3YgdBpCN60](https://www.youtube.com/watch?v=-3YgdBpCN60)

- **리소스에서 Fargate 대신 EC2를 선정한 이유**

>  Project를 하면서 느낀점은 분명 이런 부분의 효율을 따졌을때 프로젝트 기간 동안 Fargate보다 EC2가 유리할 줄 알았다. 하지만 EC2의 리소스 사용량을 90%까지 빡빡하게 굴리면서 ASG로 늘리지 않고 사용 했을 경우 Fargate보다 효율이 더 좋을 수 있는데 무엇보다도 EC2를 이용한 복잡성과 관리의 번거로움, 비용 효율적인 부분, 그리고 프로젝트 중 사용 리소스를 생각 하면 오히려 Fargate로 만들었다면 비용을 훨씬 더 최적화하고 작업도 금방 끝났을것 같다.

<br>

## 리소스 선정 이유 - DynamoDB

![image](https://github.com/pomottoro/comments/assets/58872932/cd2a286f-400e-4c67-817d-329d0a1ce317)

- **리소스에서 로그 저장소로 DynamoDB를 사용한 이유**

  이벤트 로그 저장소 DB는 작업 변경 로그 메시지를 저장하는 것이 목적이기 때문에 매번 상황에 따라 형식과 내용이 바뀐다. 데이터가 key-value 형태로 저장되기 때문에 속성의 변경과 추가가 자유롭고 로그 메시지를 NoSQL형식으로 유동적으로 저장하기 용이하며, read 속도도 빨라 트래픽이 몰려도 견딜 수 있다. <br>
   구현한 아키텍처는 적은 양의 로그 데이터를 처리하기 때문에 처리 속도가 빠른 DynamoDB가 효과적일 것이라고 생각했고 AWS Lambda와 같은 이벤트 기반 처리 시스템과 잘 통합되어 실시간 로그 분석과 같은 복잡한 로그 처리 작업 또한 쉽게 구현할 수 있어서 DynamoDB를 선택했다.

<br>

## 리소스 선정 이유 - RDS or Aurora

![image](https://github.com/pomottoro/comments/assets/58872932/238a13a9-cba3-4da9-86c0-4d7635eb28a6)

- **리소스에서 Aurora 를 채택한 이유**

   클러스터의 Multi AZ 를 통해 가용성과 확장성을 확보하며 RDS와 연결 하려 했지만 RDS가 지원하는 Multi AZ는 비용은 각 AZ에 생성된 RDS 만큼 나가지만 AZ에 문제가 생기지 않는 이상은 휴지상태가 되어 작동하지 않고 비용만 청구되는 반면, Aurora에서 지원하는 Multi AZ 서비스는 읽고 쓰기 가능한 Main DB서버와 별개로 Read가 가능한 Sub DB 서버를 다른 AZ에 두기 때문에 RDS 보다 Data 를 읽고 쓰는게 훨씬 빠르고 비용적인 면에서 역시 RDS Multi AZ 서비스와 크게 차이도 나지 않고 **관리나 성능적인 부분에서도 유리하기**에 아키텍쳐 상에서는 Aurora 를 채택했다. 
   
   >  하지만 비용적인 부분과 사용하는 Data의 양이 크지 않기에 실제 구현은 RDS 를 사용했다. 현업에서 실제로 아키텍쳐를 구현한다면 Aurora를 사용할 것 같다.

<br>

## 리소스 선정 이유 - EventBridge

![image](https://github.com/pomottoro/comments/assets/58872932/9434ae35-0c5f-4dc5-9f23-05b3a874e420)

- **리소스에서 EventBridge를 사용한 이유**

  **EventBridge**는 분리되고 **확장 가능한 이벤트 기반 시스템을 구축하는 데에 적합**한 시스템 이다. **Rule을 적용해 필터링** 기능을 사용할 수 있으며, 사용자 정의 코드 없이 직접 이벤트를 수신하여 다양한 서비스와 통합이 가능하다. **SNS, SQS 사용을 고려했을 때 가지는 장점**인 엄격한 **메세지 순서 보장, 높은 데이터 처리량** 보다는 앞서 언급한 장점을 가진 **EventBridge 사용이 서비스에 더 적합**하다고 판단하여 EventBridge를 사용하기로 결정했다.

<br>

# 아키텍쳐 구조 보완

---

![image](https://github.com/pomottoro/comments/assets/58872932/535cd683-c759-4658-88d2-c25f496a02f8)

참고자료 : [https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/vpc-nat-gateway.html](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/vpc-nat-gateway.html)

-  위에서 구상한 아키텍처에는 ECS가 Public Subnet 에 위치하도록 구성이 되어 있는데, 이 부분이 보안 부분에 취약 할 수 있다고 한다. 그래서 ECS 와 DB서버를 Private Subnet에 위치하게 두고 NAT Gateway를 Public Subnet으로 분리하여 ALB를 통한 ECS와의 통신은 유지하면서, 클러스터 내부의 IP주소 노출위험을 제거했다. 실제 많은 기업의 아키텍처가 이러한 방식으로 구성 되어 있다는 것을 알아보니 앞으로도 더욱 배워야 할 것이 많다는 것을 느꼈다.

<br>

# 완성된 작업물

---

<img width="1589" alt="KakaoTalk_20230628_110238378" src="https://github.com/pomottoro/comments/assets/58872932/8c722f90-2a6a-447f-9f93-4d24ebdee119">
<img width="1589" alt="KakaoTalk_20230628_110238378_01" src="https://github.com/pomottoro/comments/assets/58872932/ed18227a-e24a-4335-a841-cf27ab739742">
<img width="1589" alt="KakaoTalk_20230628_110238378_02" src="https://github.com/pomottoro/comments/assets/58872932/6d5cda9f-f67c-4483-be50-9161dc65e6c3">

- 로그인으로 사용자 인증을 통해 DB에서 Task의 현황을 가져온다.

<br>

---

<br>

# 마치며

---

<div class="notice--primary" markdown="1">
 이번 Final Project를 시작한 첫 날엔 정말 바닥부터 시작했더니 어떤것 부터 해야할 지, 어떤 것을 찾아봐야 할지 갈피를 못 잡았다. 자유주제로 시작하니 정말 사막 한가운데에 홀로 떨어진 것 처럼 막막했다. 주제 선정을 시작했고 주제에 대한 DDD, 아키텍처를 만들고 나니 그림이 그려졌다. 팀원들끼리 서로 업무를 분담해 각자의 일을 했고 그렇게 합쳐진 결과물이 나오고 나니 내심 뿌듯하고 뭔가 시원하기도 하고 기술적인 한계에 막혀 구현 못한 부분이 찝찝하고 아쉽기도 했다. 한편으론 AWS에 대한 이해도가 더욱 올라 많은 성장이 이루어 졌던 프로젝트였다. 아직도 앞으로 한참 공부해야 할 것 들이 많지만 이번 프로젝트 완성물을 보고나니 내가 부족한 부분이 어떤 것인지 느꼈고 앞으로 보완하면 뭐든 할 수 있을것 같다는 생각이 든다.
</div>
<br>

---

Final Project Repo : [https://github.com/pomottoro/devops-04-Final-Team5/tree/main](https://github.com/pomottoro/devops-04-Final-Team5/tree/main)

Final Project PPT : [https://docs.google.com/presentation/d/e/2PACX-1vTGYzIW4blWWCaIiOFcHgFha_5rKp_8sqeTIuhNzLW0gV_iOniUc0YkVE_25jm_Crcy6oreLIuczgL6/pub?start=true&loop=false&delayms=3000](https://docs.google.com/presentation/d/e/2PACX-1vTGYzIW4blWWCaIiOFcHgFha_5rKp_8sqeTIuhNzLW0gV_iOniUc0YkVE_25jm_Crcy6oreLIuczgL6/pub?start=true&loop=false&delayms=3000)

---

<br>
