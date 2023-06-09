---
title: "23-06-07 Presentation"
excerpt: "병목(Bottleneck) 찾기"

categories: ["Presentation"]
tags: ["Bottleneck", "AWS", "Burst"]

date: 2023-06-07
last_modified_at: 2023-06-07

header:
  overlay_image: assets\images\Thumbnail\infinite.jpg
  overlay_filter: 0.5 
  teaser: assets\images\Thumbnail\Presentation-thumbnail.jpg
published: true
---

# Q. AWS에서는 인스턴스나 볼륨에 대해서 버스트 기능을 제공합니다. 이는 평소에 사용하지 않을 때의 성능을 모아두고, 부하가 발생할 경우 일시적으로 성능을 올리는 기능입니다. 이것이 어떤 메커니즘으로 작동하는지 연구하세요.

> A. AWS는 부스트 기능을 인스턴스나 볼륨에 제공합니다. 이것은 일반적으로 사용되지 않을 때 성능을 수집하고, 부하가 발생할 때 임시적으로 성능을 증가시키는 기능입니다. 이 메커니즘의 작동 방식에 대해 자세히 설명하겠습니다.
>
> AWS에서 제공하는 인스턴스 중 일부는 "버스트블" 인스턴스라고 불립니다. 이들 인스턴스는 기본 성능 수준을 초과하여 일시적으로 높은 성능을 제공할 수 있습니다. 이 기능은 일정한 작업량을 처리하는 데에는 충분하지 않지만, 가끔씩 높은 수준의 처리량이 필요할 때 유용합니다.
>
> 이러한 버스트블 인스턴스는 "크레딧"이라는 개념을 사용하여 이를 가능하게 합니다. 크레딧은 성능 능력의 일종으로, 인스턴스가 아직 사용하지 않은 자원을 표현합니다. 인스턴스가 일정 시간동안 아무런 작업도 하지 않으면, 크레딧이 쌓이게 됩니다. 반대로, 인스턴스가 많은 작업을 처리할 때는 크레딧이 소비됩니다.
>
> 인스턴스의 기본 성능 이상으로 작업을 처리해야 할 때, AWS는 이 쌓여있는 크레딧을 사용하여 임시적으로 성능을 증가시킵니다. 따라서 인스턴스는 일시적으로 더 많은 작업을 처리할 수 있게 됩니다. 하지만, 크레딧이 모두 소비되면 인스턴스의 성능은 다시 기본 수준으로 돌아갑니다.
>
> 결론적으로, AWS의 버스트블 인스턴스는 일시적인 부하를 처리하기 위해 사용되지 않는 자원을 "크레딧"으로 저장하고, 필요할 때 이 크레딧을 사용하여 성능을 증가시키는 메커니즘을 사용합니다. 이는 부하가 일시적으로 발생하는 애플리케이션에 대해 효율적인 비용 및 자원 사용을 가능하게 합니다.

---

## 레퍼런스 

 [https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/burstable-credits-baseline-concepts.html]( https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/burstable-credits-baseline-concepts.html)

[https://instances.vantage.sh/?region=ap-northeast-2](https://instances.vantage.sh/?region=ap-northeast-2)

<br>

---

<br>