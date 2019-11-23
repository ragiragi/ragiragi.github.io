---
layout: post
title: Elixir, Unicode and IOList
tags: [elixir, unicode, iolist]
image: '/images/posts/2019-11-23-string-theory.jpg'
---
<style>
body { font-size: 14px; line-height: 24px; }
code { font-size: 12px; line-height: 20px; }
</style>

_간단한 코딩 테스트들을 하다 보면 문자열을 낱글자로 나눠 처리해야 하는 경우를 쉽게 만날 수 있는데, 이 경우 Elixir에서 사용할 수 있는 함수로 `String.codepoints/1`와 `String.graphemes/1`가 두가지가 있다. 사실 Unicode에 대한 배경 지식이 없다면 [공식 문서의 설명](https://hexdocs.pm/elixir/String.html#module-code-points-and-grapheme-cluster)만으로는 두 함수의 차이가 쉽게 와닿지 않은데, Elixir Unicode 처리에 대한 이해 그리고 `iolist()` 타입까지 Elixir String에 대해 다룬 발표가 있어 요약해본다._

**영상 (37분) : [ElixirConf 2016 - String Theory by Nathan Long & James Edward Gray II](https://youtu.be/zZxBL-lV9uA)**

#### Elixir의 문자열 타입들

* Charlist : unicode codepoints 값을 담고 있는 integer list. 보통 erlang 모듈 호환을 위해 사용.
* Atom : 정체는 name들을 담고 있는 테이블. 단순히 같은 포인터인지 체크하면 되니 equality 체크가 빠름. GC 되지 않기 때문에 사용자 입력값을 atom으로 변경하고자 할 때는 주의가 필요. 이 경우엔 `String.to_existing_atom/1`을 사용하는 게 좋다.
* String
  * bitstrings : 연속된 bit(0, 1)들. `<<..>>` 리터럴로 표현
  * binaries : 연속된 byte(8bit)들
  * Strings : UTF-8으로 인코딩 된 codepoints를 내용으로 하는 binaries
  * 즉, string ⊆ binary ⊆ bitstring
* iolist?
  * 공식 문서상의 타입 스펙은 `maybe_improper_list(byte() | binary() | iolist(), binary() | [])`
  * IO를 위해서 문자열 concatanation 연산은 자주 일어날 수 밖에 없는데, 이럴 때 마다 메모리 복사가 발생
    ```
    iex> IO.puts("James" <> " and " <> "Nathan")
    # 메모리에 "James", " and ", "Nathan", "James and ", "James and Nathan"이 모두 생성.
    ```
  * 이를 대신해 concat 없이 binary가 담긴 list를 주면 차례대로 메모리 복사 없이 iterate 하면서 사용됨. 이게 `iolist()`
  * improper list? : 일반적으로 list 구조는 `[any() | list()]` 이지만, list 뒤에 새로운 값을 추가하는 appending 작업 효율을 위해 `[["James" | " and "] | "Nathan"]`와 같은 중첩 구조도 지원.
  * 담을 수 있는 내용에 따라 세부적으로 `iolist`, `iodata`, `chardata` 등으로 구분.

#### Elixir와 Unicode

* Unicode : 기호와 기호별 숫자 값(codepoint)의 거대한 맵핑 테이블. UTF-8은 이 codepoint를 8-bit(byte)로 표현한 것. 물론 단일 byte로는 Unicode를 모두 커버할 수 없다 보니 바이트 두 개짜리, 세 개짜리, 네 개짜리 템플릿이 존재한다.
  ```
  0xxxxxxx
  110xxxxx 10xxxxxx
  1110xxxx 10xxxxxx 10xxxxxx
  11110xxx 10xxxxxx 10xxxxxx 10xxxxxx

  # "x" 부분을 이용해 codepoint를 나타냄.
  ```
* Elixir의 문자열은 UTF-8 인코딩 된 바이너리이므로 실제 저장되는 내용을 보면
  ```
  iex> i "⏰"
  ...
  Raw representation
    <<226, 143, 176>>
  iex> [226, 143, 176] |> Enum.map(base_2)
  ["11100010", "10001111", "10110000"]
  # 11100010 10001111 10110000
  # 1110xxxx 10xxxxxx 10xxxxxx
  # 템플릿 제외한 "x" 부분만 모아보면
  # 0010001111110000 (10진수로는 9200)
  iex> String.to_integer("0010001111110000", 2) == ?⏰
  true
  ```
  * 즉 여러 개의 byte가 하나의 codepoint를 구성
* 여기에 더해 여러 개의 codepoint가 모여 하나의 grapheme(자소)을 구성할 수도 있다. `e + ̈ = ë`. 이모지 skin tone modifier 조합도 이와 같은 방식을 사용. (여러 codepoint를 조합하여 하나의 codepoint로 표현(compose, nfc), 그 반대로 해체하는 과정을(decompose, nfd) unicode normalization이라고 부른다.)
  * 즉 `codepoints/1`은 이 개별 codepoint 글자들을 반환하는 함수이고, `graphemes/1`은 grapheme cluster 단위로 글자들을 반환한다.
  ```
  iex> IO.inspect({"e\u0308", "\u00EB"})
  {"ë", "ë"} # 같은 표현을 갖는 개별 codepoint도 존재
  iex> "e\u0308" == "\u00EB"
  false
  iex> noel = "noe\u0308l"
  "noël"
  iex> String.codepoints(noel)
  ["n", "o", "e", "̈", "l"]
  iex> String.codepoints(noel) |> Enum.reverse() |> Enum.join()
  "l̈eon"
  iex> String.graphemes(noel)
  ["n", "o", "ë", "l"]
  iex> String.graphemes(noel) |> Enum.reverse() |> Enum.join()
  "lëon"
  ```
* 여러 bytes가 하나의 codepoint를 나타내고, 여러 codepoints가 하나의 grapheme을 나타내고, 중간에 word joiner와 같은 character도 들어갈 수 있고... 상황이 이렇다 보니 문자열을 다루는 구현이 간단하진 않다.
  * 길이를 구한다면? 문자열을 뒤집는다면? 문자열 동등성 비교를 한다면? 문자열 일부를 잘라낸다면? 엑센트가 들어간 글자를 downcase 해야한다면?
  * 다행히 elixir의 String 모듈에 있는 함수들은(`length/1`, `reverse/1`, `equivalent?/1`등) 이러한 이슈들을 꽤 잘 처리해주고 있다.

#### 다시 iolist()

* BEAM VM에서 데이터는 불변이고 프로세스는 개별 heap을 갖고 있다. 즉 메시지가 전달될 때 마다 메모리 복사가 이루어진다. BEAM에서는 이런 점을 개선하기 위해 64bytes 이상의 바이너리는 공용 heap에 저장하고 프로세스간 공유한다. (reference counted binaries)
  * 장점이라면 큰 바이너리를 프로세스간에 전달할 때 매우 가볍고(참조값만 전달), 재사용성이 높아진다.
  * 단점이라면 long-running process에서 참조가 유지되는 경우 GC 되지 않는다. 즉 메모리 릭의 위험성을 가지고 있다.
* iolist를 이용하면 프로세스 내 작은 문자열도 재사용될 수 있다.
  ```
  [start_li, end_li] = ["<li>", "</li>"]
  [%{name: "Joe"}, %{name: "Amy"}]
  |> Enum.map(fn u -> [start_li, u.name, end_li] end)
  |> IO.puts()
  ```
* 내부적으로 iolist는 `write`가 아닌 `writev` 시스템 콜이 사용될 수 있기 때문에 -- 문자열 메모리 주소들만 전달 -- 성능상 잇점을 얻을 수 있다.

#### Web Responses, Phoenix Templates

* 이런 특징을 잘 이용하는게 바로 phoenix의 template인데, EEx 파일을 함수로 컴파일하고(iolist 덩어리), 이 함수는 주어진 인자값을 끼워넣어 최종 iolist를 반환. 이를 통해 템플릿 iolist에서 공통으로 사용되는 HTML 조각들은 메모리내에 재사용되고, `writev`를 통해 직접 참조됨. 🚀
* caching을 위해 별다른 노력을 하지 않아도 자연스럽게 정적인 부분은 cache 되고, 동적인 부분은 cache 안되고, 템플릿 변경되면 함수 재컴파일을 통해 cache invalidate 처리된다.

##### Footnotes

* 위 발표 내용은 발표자 Nathan의 블로그에 보다 더 자세한 설명을 곁들여 네 편의 글로 나뉘어 게시되있다. [http://nathanmlong.com/blog/](http://nathanmlong.com/blog/)
