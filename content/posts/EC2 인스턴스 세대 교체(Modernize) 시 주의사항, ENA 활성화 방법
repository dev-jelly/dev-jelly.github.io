---
title: "EC2 인스턴스 세대 교체(Modernize) 시 주의사항, ENA 활성화 방법"
date: "2020-07-10T00:00:00.000Z"
template: "post"
draft: false
slug: "EC2 인스턴스 세대 교체(Modernize) 시 주의사항, ENA 활성화 방법"
category: "DB"
tags:
  - "Mysql"
  - "mysqldump"
description: "최근에 서버를 전체적으로 정리하면서, 이전 세대의 인스턴스를 다 업그레이드 하고 있다. 업그레이드를 하던 중 아래와 같은 메시지가 나타났다."
socialImage: "/media/image-2.jpg"
---

## 짧은 글

생성한 지 오래 된 EC2 인스턴스의 타입 변경 전에는 `modinfo ena` 나 `ethtool -i eth0`를 통해서 정상적으로 ENA(Elastic Network Adapter)가 활성화 되어 있는 지 확인 하자. 정상적으로 설치되어 있다면 아래와 같이 나올 것이다.

```
[ec2-user@ip-0.0.0.0 ~]$ modinfo ena
filename:       /lib/modules/4.14.181-108.257.amzn1.x86_64/kernel/drivers/amazon/net/ena/ena.ko
version:        2.2.8g
license:        GPL
description:    Elastic Network Adapter (ENA)
author:         Amazon.com, Inc. or its affiliates
srcversion:     EEB4F7FE4AF4BC0F0050480
...
...
```

```
[ec2-user@ip-0.0.0.0 ~]$ ethtool -i eth0
driver: ena
...
...
```

위와 같이 driver가 ena로 잡혀있어야 한다. 만약 아래와 같다면 제대로 설정되어 있지 않은 것이다. 

```
[ec2-user@ip-0.0.0.0 ~]$ ethtool -i eth0
driver: vif
...
...
```

위와 같다면 대부분의 새로운 세대의 인스턴스는 사용하지 못하며, 꼭 ENA를 활성화 해야한다. 

## 긴 글

### 요구사항

aws-cli 

- 로컬 OS는 macOS 기준으로 설명합니다.

### 시나리오

최근에 서버를 전체적으로 정리하면서, 이전 세대의 인스턴스를 다 업그레이드 하고 있다. 업그레이드를 하던 중 아래와 같은 메시지가 나타났다.

```
Enhanced networking with the Elastic Network Adapter (ENA) is required for the 't3.small' instance type. Ensure that you are using an AMI that is enabled for ENA. Launching EC2 instance failed.
```

뭔가 ENA를 활성화 해달라고 하는데 저 정체를 들어본 적이 없기에 일단 다시 t2로 변경하여 ENA를 살펴보기 시작했고, 

```
[ec2-user@ip-0.0.0.0 ~]$ modinfo ena
filename:       /lib/modules/4.14.181-108.257.amzn1.x86_64/kernel/drivers/amazon/net/ena/ena.ko
version:        2.2.8g
license:        GPL
description:    Elastic Network Adapter (ENA)
author:         Amazon.com, Inc. or its affiliates
srcversion:     EEB4F7FE4AF4BC0F0050480
...
...
```

위와 같이 ena가 정상적으로 설치 되어 있는 것은 확인하였지만, 

```
[ec2-user@ip-0.0.0.0 ~]$ ethtool -i eth0
driver: vif
...
...
```

ena가 정상적으로 설정되어 있진 않았다. 대부분의 경우 커널을 최신버전으로 유지하거나, `yum update`를 한 뒤 재시작을 하면 정상적으로 ena가 설치되어 있지만 ena가 설정되어 있지 않을 수 있다. 우분투의 경우 [AWS 매뉴얼](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/enhanced-networking-ena.html#enhanced-networking-ena-ubuntu)을 참고하자.

설정되어 있다면, 인스턴스를 종료한 뒤 로컬 컴퓨터에서 아래의 명령어를 실행해 정상적으로 ena 활성화를 해준 뒤 인스턴스 세대 변경작업을 진행해주면 정상적으로 인스턴스가 실행 가능하다. ({instanceId}를 본인 환경의 instanceId로 대체하여 사용하시면 됩니다.)

```shell
aws ec2 modify-instance-attribute --instance-id {instanceId} --ena-support
```

대충 2016년 인스턴스들이 대부분 ENA가 비활성화 상태인 것 같다.

