---
layout: post
title: Review - Elixir for Programmers
tags: [elixir, davethomas]
image: '/images/posts/2019-12-21-elixir-for-programmers.jpg'
---

[https://codestool.coding-gnome.com/courses/elixir-for-programmers](https://codestool.coding-gnome.com/courses/elixir-for-programmers)

최근 팀에 새로운 멤버들이 하나둘 합류하면서 [Programming Elixir (Book)](https://pragprog.com/book/elixir16/programming-elixir-1-6)로 세미나를 진행하고 있다. 언어 기초 문법과 프로그래밍 방법을 다루는 전반부는 나름 재미있게 진행할 수 있었지만, Process/OTP를 다루는 후반부는 어떻게 해야 할지, Process / Supervisor 처음 접할 때의 혼란스러움이 떠올라 난감했다. 사실 Dave Thomas의 온라인 강의가 있다는 건 알고 있었지만, 책 내용과 같겠지란 생각에 잊고 있었는데 [PagerDuty에서 150명의 개발자들이 위 강의를 수강했다는 글](http://evrl.com/programming/elixir/2019/09/24/erlang-for-elixir.html)을 보고 살펴본 이후에야 이거다 싶었다. (팀 단위 결제도 지원한다.)

강의 내용은 [Hangman 게임](https://en.wikipedia.org/wiki/Hangman_(game))을 구현을 통해 언어 기본 문법부터 Phoenix Channel 사용하는 예까지 다루고 있다. 언듯 새로운 프로그래밍 언어 약 파는 평범한 튜토리얼일 것 같지만, 의외로 언어 소개보다는 Elixir를 이용한 소프트웨어 아키텍처에 방점을 두고 있다.

특히 먼저 상태를 외부에 둔 채 순수 함수로 코어 로직을 작성하고, 이 구조를 유지하면서 자연스럽게 프로세스 소개로 전환하는 점이 인상적이었다. 프로세스를 배우고 나면 프로세스 활용하고 싶은 마음에 코어 로직과 쉽게 뒤섞이기 마련인데, 로직은 순수 함수로 프로세스는 상태 관리에 집중하도록 가이드하는 부분이 본 강의의 핵심이 아닌가 싶다. [Designing Elixir Systems with OTP](https://pragprog.com/book/jgotp/designing-elixir-systems-with-otp)의 순한 맛 버전이랄까.

![](/images/posts/2019-12-21-i-have-a-hammer.png)
*< 이제 막 GenServer를 배운 나의 모습. '모든 게 못으로 보여요' >*

Elixir의 Process를 알 것 같다가도 모르겠는 이유가 다른 언어와 달리 코드의 Lexical 형태와 Process Instance가 1:1 매치되지 않기 때문인데 이 부분을 명확히 짚어 주는 점도 좋았다.

강의에서 보여주는 코딩 스타일이 조건문을 피하기 위해 boolean parameter를 적극 사용한다던지, 'Bounded Context도 No, Umbrella 마저도 No, 반드시 애플리케이션으로 구분해!'라고 이야기하는 등 일반적인 스타일을 벗어나는 모습도 보여 살짝 거부감이 들 수도 있지만 저자가 이야기하고자 하는 메시지를 전달하는 측면에서 생각해보면 나쁘지 않았던 것 같다.

하나의 챕터가 3분 ~ 10분 정도로 짧아서 틈틈이 진행하기에도 좋았고, 내용도 천천히 따라 할 수 있는 수준이라 머슬 메모리를 기르며 자신감을 키우기에도 좋은 것 같다. 전체적으로 좋은 개발 습관을 기를 수 있도록 안내하는 내용이 많아 Elixir 도입을 준비 중인 팀이라면 Programming Elixir와 함께 반드시 진행해야 할 코스인 것 같다.

##### Footnote

* 후반부 Phoenix 부분은 구버전을 다루고 있어 업데이트가 필요해 보인다.
