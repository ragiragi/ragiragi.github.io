---
layout: post
title: Phoenix Framework Endpoint URL 설정
tags: [TIL, elixir, phoenixframework]
image: '/images/posts/2020-02-22-config-phoenix.jpg'
---



애플리케이션을 로컬 개발 환경 수준에서 서비스 서버로 올리기 시작하면 가장 먼저 드러나는 문제가 서비스 환경에 따른 URL 렌더링 이슈인데 Phoenix Framework에서는 Endpoint url 설정을 통해 이를 지원한다.


``` elixir
config :example, Example.Endpoint,
  http: [port: 4000],
  url: [host: "beta.example.com", scheme: "https"],
  ...
```

`Example.Router.Helpers.xxx_path/4`와 같이 라우터 모듈을 통해 생성된 url은 위의 설정값에 따라 생성이 되고, 만약 해당 값을 직접 참조하고 싶다면 `Phoenix.Endpoint.url/0`을 이용할 수도 있다. 만약 설정하지 않으면 endpoint의 http, https 설정을 따라간다.

물론 애플리케이션에 직접 코딩하지 않고 환경변수를 통한 주입도 가능하다.

``` elixir
config :example, Example.Endpoint,
  url: [host: {:system, "SERVICE_HOST"}],
  ...
```

[https://hexdocs.pm/phoenix/1.4.14/Phoenix.Endpoint.html#module-runtime-configuration](https://hexdocs.pm/phoenix/1.4.14/Phoenix.Endpoint.html#module-runtime-configuration)

---

Phoenix Framework의 Endpoint 모듈은 웹 요청을 받아들이는 시작 지점으로 역할에 걸맞게 다양한 설정 포인트를 가지고 있다. 하지만 개발 도중엔 딱히 손댈 일이 없어 아무 생각 없이 초기 설정 그대로 사용하기 마련이라 Endpoint 설정을 통해 어떤 것들을 할 수 있는지 모르고 넘어가기 쉬운 것 같다.
