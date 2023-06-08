---
title: "AWS EC2와 k6를 이용한 성능 테스트"
excerpt: "성능 테스트"

categories: ["Sprint"]
tags: ["성능 테스트", "load test", "k6"]

date: 2023-06-08
last_modified_at: 2023-06-08
---

# AWS EC2와 k6를 이용한 성능 테스트 실습

---

## procedure

1. aws ec2 인스턴스 생성

   - aws 계정에서 EC2 인스턴스를 생성합니다.

   - 버스트 기능이 있는 t2micro를 생성하세요

   - ubuntu 20.04


<br>

![image](https://github.com/pomottoro/comments/assets/58872932/849b19b0-a5a2-48e6-a63d-aeacfab01a72)

![image](https://github.com/pomottoro/comments/assets/58872932/7626c4da-7c1c-4066-a455-bf995611ed13)

![image](https://github.com/pomottoro/comments/assets/58872932/f985b27f-677c-46f3-a369-a7879c6b8a63)

> 테스트에선 무제한으로 활성화

![image](https://github.com/pomottoro/comments/assets/58872932/4c4c0821-d3a7-4bb9-b327-ac13baf16fb4){: .align-left}고급 세부 정보 탭에서 크레딧 사양 활성화

![image](https://github.com/pomottoro/comments/assets/58872932/3c405ef7-5f71-4a5b-803f-667e42522307)

2. ssh 접속 생성한 ec2에 접속을 합니다.

   ![image](https://github.com/pomottoro/comments/assets/58872932/6a556e67-6649-41dc-ad15-69d1725c4a7b)

3. install docker on EC2

```bash
# 충돌 방지 및 관련 모든 패키지 삭제
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

#업데이트 및 HTTP 패키지 설치
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg

# GPG 키 및 저장소 추가
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

#도커 레포지토리 설정
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
# 도커 엔진, 컨테이너, 도커 컴포즈 설치
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 도커 설치 확인
docker --version
```

> 도커 설치 : [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
>
> ![image](https://github.com/pomottoro/comments/assets/58872932/5e4dbb60-5a89-45ef-b4dc-4276a5ad22a0)

4. run container

- 성능 테스트를 위한 타깃 서버를 컨테이너로 배포합니다.

```bash
sudo docker run --name <이름> -d -p 8080:8080 sebcontents/cozserver:1.0
```

> ![image](https://github.com/pomottoro/comments/assets/58872932/d7ddf607-e392-4122-9e9c-4c8c7264e5f8)

- 서버 배포 확인

```bash
curl http://localhost:8080
```

> ![image](https://github.com/pomottoro/comments/assets/58872932/cedc46bf-63a4-43fe-a552-e3047f0be7ea)

5. [install k6](https://k6.io/docs/get-started/installation/)

- 공식 홈페이지를 이용하여 k6를 설치합니다. (트러블 슈팅 주의)
- 클라우드 상에 빠른 설치를 위해서 snapd도 추천합니다. https://snapcraft.io/install/k6/ubuntu

```bash
sudo apt update
sudo apt install snapd

sudo snap install k6
```

> ![image](https://github.com/pomottoro/comments/assets/58872932/d6446ef8-bb82-48c6-a357-be076d53d5d4)

6. [리포지토리](https://github.com/cs-devops-bootcamp/sprint_k6_test)에 유형별 테스트 스크립트가 작성되어 있습니다. 앞에서 설명한 내용과 비교하면서 코드의 구조를 잘 살펴보시기 바랍니다.

7. run the tests 각각의 스크립트를 실행해 보고 ec2 인스턴스의 CPU 사용률 대시보드를 확인해 보세요. 

```bash
k6 run basic_test.js
k6 run load_test.js
k6 run stress_test.js
k6 run spike_test.js
k6 run soak_test.js
```

![image](https://github.com/pomottoro/comments/assets/58872932/6e460f43-16de-474a-a819-03a405c11953)
![image](https://github.com/pomottoro/comments/assets/58872932/83e5930d-dad5-4e78-8b2d-4afe5015fafd)
![image](https://github.com/pomottoro/comments/assets/58872932/78bbb9bf-2fc8-4a5b-853a-80ce6d43958e)
![image](https://github.com/pomottoro/comments/assets/58872932/f1ae5c87-a46f-424b-8a63-2e8ec5610058)
![image](https://github.com/pomottoro/comments/assets/58872932/2bc0f54a-7e29-466d-a8e8-bcf5afde3a3f)
![image](https://github.com/pomottoro/comments/assets/58872932/6fb8879e-3706-40f7-b24d-135043370690)
![image](https://github.com/pomottoro/comments/assets/58872932/30cfc1d9-2b9a-4e3f-9a87-26d69765d2d7)

> 실제 테스트가 아니기 때문에 결과 확인을 위해 중간에 취소 함

8. inspect aws burst credit 대시 보드의 CPU 크레딧 사용량(개수) 와 CPU 크레딧 밸런스(개수)를 관찰합니다.

> ![image](https://github.com/pomottoro/comments/assets/58872932/51328876-45fd-4466-94da-dbca66b81bba)

<br>

---

<br>