---
title: Tailwind CSS 편법을 이용한 class 덮어쓰기
date: "2022-03-16T21:00:00.000Z"
template: "post"
draft: false
slug: "Tailwind 사용시 편법을 이용하여 class 덮어쓰기"
category: "Unity"
tags:
  - "CSS"
  - "Tailwind"
  - "HTML"
description: "재작년 말에 살짝 맛본 뒤 요새 Tailwind를 메인으로 사용중인데, 컴포넌트를 확장하여 사용하려다 보니 문제가 발생하였다. 처음에는 아래와 같은 방식으로 처리하려고 했다."
socialImage: "/media/tailwind-css.png"
---

## 짧은 글
테마를 확장하여 새로운 modifier를 이용해 선택자 우선순위를 높여서 처리하기.
```JavaScript
/// tailwind.config.js
module.exports = {
  ...
  theme: {
    extend: {
      screens: {
        ow: '0px',
      },
    },
  },
  ...
};
```


## 긴글
재작년 말에 살짝 맛본 뒤 요새 Tailwind를 메인으로 사용중인데, 컴포넌트를 확장하여 사용하려다 보니 문제가 발생하였다.
처음에는 아래와 같은 방식으로 처리하려고 했다.
```Typescript
type BaseComponentProps = HTMLAttributes<HTMLDivElement>;

const BaseComponent: React.FC<BaseComponentProps> = (props) => {
  const { className } = props;
  return (
    <div className={`w-20 h-8 bg-orange-300 ${className || ''}`}>
      I am Base!
    </div>
  );
};

const ExtendedComponent: React.FC = () => (
  <BaseComponent className="bg-gray-300" />
);
```
하지만 [HTML은 class의 순서 따위 신경쓰지 않기](https://css-tricks.com/the-order-of-css-classes-in-html-doesnt-matter/) 때문에, 
경우에 따라 정상적으로 적용되지 않는다. 그래서 생각한 편법은 modifier를 이용해 선택자 우선순위를 변경하는 방법이었다.

일단 아래의 코드가 잘 동작하는 걸 확인 한 뒤 바로 설정작업에 들어갔다.
```Typescript
const ExtendedComponent: React.FC = () => (
  <BaseComponent className="sm:md:lg:xl:bg-gray-300" />
);
```


아래와 같이 theme 를 확장하게 되면 미디어 쿼리가 있기 때문에 선택자 우선순위가 올라간다. 
```JavaScript
/// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      screens: {
        ow: '0px',
      },
    },
  },
};
```

이후엔 아래와 같이 modifiers를 이용하여 기존 컴포넌트를 덮어써서 사용할 수 있다.

```Typescript
const ExtendedComponent: React.FC = () => (
  <BaseComponent className="ow:bg-gray-300" />
); 
```