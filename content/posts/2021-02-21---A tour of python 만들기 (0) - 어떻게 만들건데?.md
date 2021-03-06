---
title: "A Tour of python 만들기 (0) - 어떻게 만들건데"
date: "2021-02-21T09:00:00.000Z"
template: "post"
draft: false
slug: "Making A tour of Python"
category: "Web"
tags:
  - "React"
  - "Python"
  - "Brython"
description: "최근에 파이썬을 가르쳐 주려고 보니, Go에는 A tour of go 같은 게 있는데 파이썬은 없는지 확인해 보니 없는 것 같았다. (못 찾은 거일 수도 있지만...) 물론 코드카데미 같은 곳도 있긴 하겠지만 별로 가벼운 서비스는 아니라고 생각이 들어서, 최근에 목말랐던 사이드 프로젝트에 대한 욕구도 충족하기 위해 직접 만들어 보기로 했다."
socialImage: "/posts/2021-02-21/earth-python.png"
---
# A tour of python 만들기 (0) - 어떻게 만들건데?

최근에 파이썬을 가르쳐 주려고 보니, Go에는 [A tour of go](https://tour.golang.org/) 같은 게 있는데 파이썬은 없는지 확인해 보니 없는 것 같았다. (못 찾은 거일 수도 있지만...) 물론 코드카데미 같은 곳도 있긴 하겠지만 별로 가벼운 서비스는 아니라고 생각이 들어서, 최근에 목말랐던 사이드 프로젝트에 대한 욕구도 충족하기 위해 직접 만들어 보기로 했다.

## 내가 원한 서비스 조건

### 클라이언트 베이스 파이썬 실행기

이게 뭐 그리 중요한가 싶겠지만, 내겐 제일 중요했다. 이게 충족이 안 되면 시도조차 안 할 생각이었다.

서버에서 코드를 실행시키면 여러 단점이 있다. 일단 서버비용이 든다. 단순히 사용료를 의미하는 게 아니라 지속적인 관리를 하고, 시간을 투자해야 한다. 그리고 서버에 이상이 있을 때 서비스를 지속하기 힘들기에 결국 서비스 종료를 해야 한다는 단점이 있다. 하지만 클라이언트 베이스라고 하면 정적 콘텐츠 호스팅 서버만 문제가 없으면 지속할 수 있다. 또 다른 단점으로 일단 코드를 서버에서 실행시킨다는 것 자체가 매우 많은 예외 케이스를 고려해야 한다.

그래서 클라이언트에서 파이썬을 해석할 수 있는 프로젝트가 없나 찾아보다가 [Brython](https://brython.info/index.html)이란 프로젝트를 찾을 수 있었고, 이걸 사용하기로 했다. Brython외에도 비슷한 프로젝트가 있었지만, 최신 버전을 지원하며 아직 활발한 프로젝트는 Brython밖에 없어 보인다.

### 로그인이 필요 없는 가벼운 서비스

기본적으로 계속 `A Tour of Go`(이하 어투고) 와 같은 서비스를 만들려고 했다. 그래서 가볍고, 쉽게 접근할 수 있었으면 좋겠다. 기본적으로 어떤 서비스를 만들 때 최대한 사용자가 불편해하는 부분은 없애고 싶었다.

### 프로그래밍 기본서 같은 것보단 파이썬의 문법과 특징을 다루는 서비스

어투고도 딱히 문법을 자세히 다루지도 않고, 이미 좋은 기본서는 많아서 입문자용보다는 언어 맛보기용으로 만드려고 한다. 타 언어를 쓰던 사람이 찍 먹 할 수 있는 서비스가 목적이었다. 그래도 찍먹시에 이런 건 없나 하는 부분도 어느 정도 해결해주고 싶다.

## 기술 스택

Brython 외에는 딱히 고민할 필요가 없이 그냥 후딱후딱 스택을 선택했다.

- React
- tailwindcss (어차피 이런 플젝은 첨 써보는 기술 습득용이어서 한번 써봤다.)
- Brython (위에서 계속 언급한 브라우저 기반 Python)

## 만들기

이 정도를 2일 정도 고민하고 본격적으로 작업을 시작한 것 같다. 아직 만들고 있는 플젝이긴 하지만 기본적인 작업은 거의 완성이 되었다고 생각되어서 이후 글은 이어서 쓰겠습니다.