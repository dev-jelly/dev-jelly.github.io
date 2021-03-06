---
title: "A Tour of Python 만들기 (1) - 에디터와 콘솔 구현하기"
date: "2021-03-06T09:00:00.000Z"
template: "post"
draft: false
slug: "Making A tour of Python 1"
category: "Web"
tags:
  - "React"
  - "Python"
  - "Brython"
description: "최근에 파이썬을 가르쳐 주려고 보니, Go에는 A tour of go 같은 게 있는데 파이썬은 없는지 확인해 보니 없는 것 같았다. (못 찾은 거일 수도 있지만...) 물론 코드카데미 같은 곳도 있긴 하겠지만 별로 가벼운 서비스는 아니라고 생각이 들어서, 최근에 목말랐던 사이드 프로젝트에 대한 욕구도 충족하기 위해 직접 만들어 보기로 했다."
socialImage: "/posts/2021-03-06/earth-python.png"
---
## 코드에디터 구현 - AceEditor

코드를 수정하는 에디터로는 [AceEditor](https://ace.c9.io/)를 사용했다. 이미 많은 곳에서 사용하고 있고, [React Wrapping 된 라이브러리](https://github.com/securingsincity/react-ace)도 이미 존재 했기 때문에 쉽게 사용할 수 있었다. 문제는 Brython과 섞어 쓰면서 발생했는데 그건 아래에서 다시 다루겠다.

## 파이썬 코드 실행 - Brython

이전글에서 말했듯 Python 인터프리터로는 [Brython]()을 택했다. Brython을 사용함으로써 콘솔에 대한 결과값을 가져오는 게 문제였는데, 자바스크립트에서 Brython에 대한 핸들링은 생각보다 어려웠다. 애초에 Brython은 파이썬을 이용하여 브라우저 API를 다루는 것이지. 브라우저에서 파이썬을 이용하기 위해 나온
f
### 파이썬 콘솔 구현

처음에 아이디어는 간단했다. print 함수를 덮어써서 윈도우 객체에 접근해 msg를 써나가는 식으로 접근했다.

```python
from browser import window

# 재실행 때 중복으로 나타나지 않게 하기위해 미리 초기화
window.brythonConsole = ''
def print(msg):
    window.brythonConsole += msg
   
```

이렇게 작성한 뒤 파이썬을 자바스크립트로 컨버팅해주는 모듈(Py2JS)을 사용후 eval을 사용하게 끔 했는데 문제가 발생했다. 파이썬 코드에서 오류가 났을 때 스택트레이스를 가져오는 방법이 없었고, 먼저 Brython에서 함수 호출이 끝난 시점에 대해서 이벤트를 받을 수 있는 방법이 없었다.

뭔가 방법이 없을까 찾아보다 Brython홈페이지에 이미 에디터가 구현되어 있어서 해당 코드를 참고하여 구현하였다.  일단 스택 트레이스에 대한 부분은 Brython에서는 단순 print를 오버라이드를 사용하여 덮어써서 만든 게 아닌 `cOutput`클래스를 덮어써서 구현하여 스택 트레이스도 가져오게끔 구현하였고, Py2JS가 아닌 에디터의 값도 JS에서 Brython으로 넘겨주는 게 아닌 Brython에서 에디터의 값을 가져와 실행하는 식으로 구현되어 있었다.

이를 참고하여 나온 최종 코드는 아래와 같았다.

```python
from browser import window
import sys
import time
import tb as traceback
import javascript

from browser import document as doc, window, alert, bind, html


output = ''
editor = doc['python-code']

class cOutput:
    encoding = 'utf-8'

    def __init__(self):
        self.cons = doc["console"]
        self.buf = ''

    def write(self, data):
        self.buf += str(data)

    def flush(self):
        self.cons.value += self.buf
        self.buf = ''

    def __len__(self):
        return len(self.buf)

if "console" in doc:
    cOut = cOutput()
    sys.stdout = cOut
    sys.stderr = cOut


def run(*args):
    global output
    doc["console"].value = ''
    src = window.pythonCode
    window.brythonConsole =''
    t0 = time.perf_counter()
    try:
        ns = {'__name__':'__main__'}
        exec(src, ns)
        state = 1
    except Exception as exc:
        traceback.print_exc(file=sys.stderr)
        state = 0
    sys.stdout.flush()
    output = doc["console"].value + '\n<completed in %6.2f ms>' % ((time.perf_counter() - t0) * 1000.0)
    window.brythonConsole = output
    return state


def to_str(xx):
    return str(xx)

run()
```

위와 같이 만든 뒤 리액트 코드에서는 그냥 지속적으로 결과를 업데이트 하는 방식으로 구현했다. 이벤트를 좀더 명확하게 캐치할 수 있는 방법이 있다면 좋겠지만, Brython에 익숙하지 않아서 이게 가능한 지를 찾지 못했고, 일단 브라우저 코드 에디터가 목표가 아닌 학습 사이트가 목표이기 때문에, 저런 것 까지 신경쓰는 건 오버엔지니어링이라는 생각이 들었다. 그렇게 작성된 코드는 아래와 같다.

```typescript
useEffect(() => {
  const interval = setInterval(() => {
    setResult(win.brythonConsole)
  }, 250);
  return () => clearInterval(interval);
}, []);

```

이렇게 기본적으로 코드를 작성하고 실행시키는 걸 완성하였다. 다음 글에선 코드랑 텍스트를 가져오는 것에 대해서 적을 예정이다. 특별한 방법을 사용한 건 아니지만 아직 인덱싱을 구현하지 않아서 인덱싱 구현 후 글을 작성할 예정이다.

