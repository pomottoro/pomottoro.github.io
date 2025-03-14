---
title: "Linux 기초"
excerpt: "Linux 운영체제"

categories: ["Linux"]
tags: ["Linux"]

date: 2023-03-09
last_modified_at: 2023-06-07
---

# 💡 리눅스가 많이 쓰이는 이유

1. ## 오픈 소스(Open Source), 그에 따른 뛰어난 보안성과 커뮤니티의 지원

    오픈 소스는 대부분 별도의 비용 없이 제품을 사용할 권한을 가질 수 있으며, 소스 코드가 공개되어 있습니다. 어떤 벤더(vendor)에 종속적일 필요도 없습니다. 일반적으로 오픈 소스라고 하면 보안에 취약할 것이라고 오해하지만, 오히려 투명하게 공개되어 있기 때문에 다양한 취약점을 빠르게 파악할 수 있게 됩니다. 이는 전 세계의 리눅스 개발자 커뮤니티의 힘에서 비롯됩니다. 실제로 리눅스는 바이러스나 멀웨어로부터 비교적 자유로운 편입니다.

   

2. ## 뛰어난 안정성과 유지 보수성

    최초 부팅 후 시스템이 계속 켜져 있는 시간, 즉 업타임(uptime)이 기타 운영체제보다 월등히 깁니다. 윈도우 등의 운영체제의 경우, 보안 패치를 적용하고 시스템의 재부팅을 요구하는 경우가 빈번하나, 리눅스는 시스템을 재부팅해야 하는 경우가 매우 드뭅니다. 따라서, 높은 가용성을 요구하는 서비스의 경우 리눅스가 적합합니다.

   

3. ## 높은 이식성

    어떤 하드웨어서도 리눅스는 실행 가능합니다. x86 및 arm 아키텍처 외에도 다양한 아키텍처를 지원하며, 라즈베리파이와 같은 IoT 디바이스부터 슈퍼 컴퓨터에 이르기까지 다양한 하드웨어에서 리눅스를 사용합니다. 전 세계의 상위 500대의 슈퍼컴퓨터는 [전부 리눅스를 사용](https://itsfoss.com/linux-runs-top-supercomputers/)한다는 조사 결과도 있습니다.


4. ## 클라우드를 지탱하는 기술

   리눅스 사용법 및 관리 지식은 DevOps 및 클라우드에서 가장 기초가 되는 소양입니다. 결국 서비스를 작동하는 운영체제가 리눅스이기 때문입니다.

<br>

---

<br>

# 리눅스의 종류

 리눅스(Linux)는 **리누스 토발즈(Linus Tovalds)**가 만든 오픈 소스 운영체제입니다.

1991년 대학생이었던 리누스 토발즈가 취미로 만들었다고 알려진 리눅스는 현재 가장 큰 사용자 기반을 가지고 있고, 공용 인터넷 서버에서 가장 많이 사용되며, 상위 500대 초고속 슈퍼컴퓨터에서 유일하게 사용되는 운영체제입니다. 즉, 사양이 낮은 컴퓨터에서도 우수한 성능과 기량을 발휘할 수 있고, 뛰어난 성능이 필요한 경우에도 사용할 수 있는 운영체제라고 볼 수 있습니다.

가장 큰 장점은 **오픈소스**라는 점입니다. 따라서 누구나 소프트웨어를 실행, 연구, 공유, 수정할 수 있으며, 수정한 코드를 재배포하거나 판매하는 것도 가능합니다. 

## Debian (데비안)

데비안은 데비안 프로젝트로 만들어진 운영체제로 어떤 기업이나 영리 단체에 의해 만들어진 리눅스가 아닙니다. 전 세계의 천 명 이상의 개발자들이 자유롭게 데비안을 만들어 나가고 있습니다. 데비안은 초창기 리눅스 배포판에 속하며, 별도의 패키지 관리 도구(**apt, dpkg**)를 이용해 패키지 업데이트나 의존성 및 보안 관련 업데이트를 자동으로 수행합니다. 패키지 관리가 편리하기 때문에 많은 사람들이 좋아하는 리눅스 배포판 중 하나입니다. 

## Ubuntu (우분투)

우분투는 데비안 리눅스를 기반으로 제작되었습니다. 우분투는 초보자들도 설치와 사용이 쉬우며, 많은 정보가 웹에 공개되어 있어 리눅스 입문용이나 공부하는데 적합하다는 점이 가장 큰 장점입니다. 6개월 단위로 새 버전을 발표하며, 2년 주기로 LTS(Long Term Support: 5년 간 기술지원 제공) 버전을 발표합니다. 그뿐만 아니라, 데비안으로부터 이어받은 **apt**을 이용한 패키지 관리 기법과 웹서버, 개인용 서버, 클라우드 컴퓨팅, 엔터프라이즈 서버 등 다양한 형태를 지원하고 있어 강력한 배포판 중 하나입니다. 

## RedHat (레드햇)

레드햇은 유료임에도 높은 국내시장 점유율로 운영자들이 기업용 서버 OS로 가장 선호하는 리눅스입니다. 레드햇 리눅스는 그래픽 사용자 인터페이스(GUI)를 제공하면서 초보자도 쉽게 설치할 수 있으며, 데비안과 우분투가 apt라는 패키지 관리도구를 썼다면 레드햇은 **rpm**과 **yum**을 통해 보다 쉬운 설치 및 관리를 도와줍니다.

레드햇은 안정성 때문에 신기술/신기능 적용을 보수적으로 적용합니다. 대신 레드햇이 후원하고 있는 페도라(Fedora) 리눅스(일반사용자들을 위한 배포본 프로젝트)를 통해서 새로운 기술이나 기능을 먼저 선보이고, 어느 정도 안정화되고 나면 이러한 기술/기능을 기업용 배포본인 Red Hat Enterprise Linux로 포함시켜 발표합니다. 

## CentOS (센트OS)

레드햇이 상용화되면서 무료 버전인 센트OS가 탄생하게 됩니다. 따라서 센트OS의 장점은 안정적인 레드햇의 기술을 그대로 사용할 수 있다는 점입니다. 앞서 이야기 했던 것처럼, 리눅스는 오픈소스이기 때문에 레드햇의 소스를 이용해 만들어지고, 상표권만을 배제한 리눅스가 센트OS입니다. 하지만 레드햇의 기술지원을 받을 수 있는 것은 아니며, 문제가 생겼을 경우 커뮤니티 단위의 지원으로 해결해야 합니다.

<br>

---

<br>

