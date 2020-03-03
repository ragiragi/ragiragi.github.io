---
layout: post
title: mix format 설정하기
tags: [elixir, mix, formatter]
---

언젠가부터 auto code formatter, code format lint rule와 같은 소스 코드
포맷팅과 관련한 자동화 도구가 유행하더니 GO였나 ELM이었나 프로그램
언어 차원에서 포맷터를 제공하고 규칙까지 강제하는게 유행하기
시작했다. 확실히 코드 작성시 형식보다 의미에 집중할 수 있어 도움이
되는 것 같다.

elixir도 `mix format`이라는 코드 포맷터를 내장하기 시작했는데, 여느
 언어차원에 제공되는 포맷터와 마찬가지로 표준 컨벤션만 제공하고
 이렇다할 설정 옵션이 없다.

그나마 [몇가지 설정할 수 있는
부분](https://hexdocs.pm/elixir/Code.html#format_string!/2-options)이
있긴 있는데 내 눈에 띄는 건 괄호 적용 규칙 정도. 표준 컨벤션에서는 함수/매크로
호출에 괄호로 감싸는 걸 기본으로 하지만 몇몇 DSL에서는 괄호가 없는
편이 더 자연스러울 때도 있다. 이럴 때 사용할 수 있는 옵션이 `:local_without_parens`.


``` elixir
# .formatter.exs

[
  locals_without_parens = [
    from: 2,
    ...
  ]
]

```

![](/images/posts/2020-03-03-drake-ecto-query.png)
_< ... peace ...>_

매 프로젝트 마다 하나하나 설정할 필요는 없고 각 라이브러리에 정의되어있는 설정(exports)를 불러와서 사용할 수도 있다.

``` elixir
# .formatter.exs
[
  import_deps: [:ecto, :ecto_sql]
]
```

[https://hexdocs.pm/mix/master/Mix.Tasks.Format.html#module-importing-dependencies-configuration](https://hexdocs.pm/mix/master/Mix.Tasks.Format.html#module-importing-dependencies-configuration)


----

그래도 충분히 적응됐다고 생각했는데도, 코드 저장하자마자 무식하게
기계적인 룰을 적용 당해버리는 모습을 보면 - 취향을 거세당한 느낌이라 -
매번 좋지만은 않다. 🙈
