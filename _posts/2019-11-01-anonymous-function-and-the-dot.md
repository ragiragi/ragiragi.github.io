---
layout: post
title: Anonymous Function and the Dot
tags: [elixir, function, lisp-2]
image: '/images/posts/thinking-face.png'
---

elixir를 처음 접할 때 가장 🤔스러웠던 부분은 익명 함수(anonymous
function) 호출 부분이었다. 익명함수를 호출하기 위해서는
`func_name.(args)`와 같은 문법을 써야하는데

``` elixir
iex(1)> say = fn s -> IO.puts(s) end
#Function<6.128620087/1 in :erl_eval.expr/5>
iex(2)> say("Hello")    # X
** (CompileError) iex:2: undefined function say/1
iex(2)> say.("Hello")   # O
Hello
:ok
```

반면 기명 함수(named function)는 일반적인 방법으로 호출 가능하다.

``` elixir
$ iex -e "defmodule Greeter, do: (def say(s), do: IO.puts(s))"
...
iex(1)> Greeter.say("Hello")
Hello
:ok
```

-----

이런 특징을 보이는 이유는 [언어 설계 때문][1]이라 한다. Elixir는
lisp-2 스타일 언어로서, 함수(named function)와 변수(variables)가 각기
다른 namespace를 사용하기 때문에 일반 함수 호출 문법으로는 변수에
바인드 된 함수를 찾을 수가 없는 것. (반대로 lisp-1 계열 언어들은
함수와 변수가 같은 namespace를 사용한다.) 결국 별도의 함수 호출 문법이
필요했고 그게 바로 "." 호출이라 한다. 물론 '1차로 함수 영역에서 찾고
없으면 2차로 변수 영역에 정의된 함수를 찾아주면 좋지 않을까?'라는 단순
무식한 생각도 들지만 분명 그로 인한 부작용도 있을 터이고 이는 언어
~~취향~~ 설계의 영역이니 그냥 따르기로...

##### Footnote
* 다른 lisp-2 스타일로는 Common Lisp, Ruby, Perl, Erlang 이 있다고
  한다.
* 쉽게 설명해보려다가 실패한듯한 [José의 글][2]도 참고해볼 만하다.
* Joe Armstrong(Erlang 창시자)이 일찍이 오래전 [elixir 리뷰][3] 글에서
  "이거 지금 안고치면 나처럼 20년동안 사람들에게 설명하느라 시간
  허비하고 머리가 하얗게 세버릴 껄?"라고 충고 했었다. 🤣

[1]: https://elixirforum.com/t/anonymous-functions-the-dot-and-parameters/10886/7
[2]: https://stackoverflow.com/a/18023790/1105414
[3]: https://joearms.github.io/published/2013-05-31-a-week-with-elixir.html
