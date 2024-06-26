# Thymeleaf


## 타임리프 소개
- 공식 사이트: https://www.thymeleaf.org/  
- 공식 메뉴얼 - 기본 기능: https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html 
- 공식 메뉴얼 - 스프링 통합: https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html


### 타임리프 특징
- 서버 사이드 HTML 랜더링 (SSR)
- 네츄럴 탬플릿 (순수 HTML를 유지하는 특징이 있음)
```
- 간단한 표현:
    - 변수 표현식: ${...}
    - 선택 변수 표현식: *{...}
    - 메시지 표현식: #{...}
    - 링크 URL 표현식: @{...}
    - 조각 표현식: ~{...}
- 리터럴
    - 텍스트: 'one text', 'Another one!',...
    - 숫자: 0, 34, 3.0, 12.3,...
    - 불린: true, false
    - 널: null
    - 리터럴 토큰: one, sometext, main,...
- 문자 연산:  
    - 문자합치기:+
◦ 리터럴 대체: |The name is ${name}|
- 산술 연산:
    - Binary operators: +, -, *, /, %
    - Minus sign (unary operator): -
- 불린 연산:
	- Binary operators: and, or
```


## 텍스트 - text, utext

```thymeleaf
<!-- 엔티티 그대로 출력됨 -->
<ul>  
  <li>th:text 사용 <span th:text="${data}"/></li>  
  <li>컨텐츠 안에서 직접 출력하기 = [[${data}]]</li>  
</ul>

<!-- 이스케이프 문 -->
<ul>  
  <li>th:text 사용 <span th:utext="${data}"/></li>  
  <li>컨텐츠 안에서 직접 출력하기 = [(${data})]</li>  
</ul>
```
- 이때 <, >, & 등은 HTML 엔티티를 태그의 시작이 아닌 다른 값으로 구분해야 함
- 그래서 < = &lt;, > = &gt; 등으로 변경됨! 따라서 <, > 등을 출력하려면 이스케이프 사용해야함
- 이스케이프 = utext, [(~)]
- 거의 이스케이프 문으로 기본 처리를 하는것이 좋다...ㅋ


## 변수 - SpringEL
타임리프 에서 변수 사용시
```thymeleaf
<ul>Object  
    <li>${user.username} = <span th:text="${user.username}"/></li>  
    <li>${user['username']} = <span th:text="${user['username']}"></span></li>  
    <li>${user.getUsername()} = <span th:text="${user.getUsername()}"></span></li>  
</ul>  

<ul>List  
    <li>${users[0].username} = <span th:text="${users[0].username}"></span></li>  
    <li>${users[0]['username']} = <span th:text="${users[0]['username']}"></span></li>  
    <li>${users[0].getUsername()} = <span th:text="${users[0].getUsername()}"></span></li>  
</ul>

<ul>Map  
    <li>${userMap['userA'].username} = <span th:text="${userMap['userA'].username}"></span></li>  
    <li>${userMap['userA']['username']} = <span th:text="${userMap['userA']['username']}"></span></li>  
    <li>${userMap['userA'].getUsername()} = <span th:text="${userMap['userA'].getUsername()}"></span></li>  
</ul>
```

- 지역 변수 선언 (해당 태그 내부에서 사용 가능)
```thymeleaf
<h1>지역 변수 - (th:with)</h1>  
<div th:with="first=${users[0]}">  
    <p>처음 사람의 이름은 <span th:text="${first.username}"></span></p>  
</div>
```


## 지원 기본 객체
Spring 3.0 이상부터는 넘겨 주어야됨...? == 기본객체 지원 X 직접 넘겨라

```java
@GetMapping("/basic-objects")  
public String basicObjects(Model model, HttpServletRequest request, HttpServletResponse response, HttpSession session) {  
    session.setAttribute("sessionData", "Hello Session");  
    model.addAttribute("request", request);  
    model.addAttribute("response", response);  
    model.addAttribute("servletContext", request.getServletContext());  
    return "basic/basic-objects";  
}
```

```thymeleaf
<h1>식 기본 객체 (Expression Basic Objects)</h1>  
<ul>  
    <li>request = <span th:text="${request}"></span></li>  
    <li>response = <span th:text="${response}"></span></li>  
    <li>session = <span th:text="${session}"></span></li>  
    <li>servletContext = <span th:text="${servletContext}"></span></li>  
    <li>locale = <span th:text="${#locale}"></span></li>  
</ul>  
<h1>편의 객체</h1>  
<ul>  
    <li>Request Parameter = <span th:text="${param.paramData}"></span></li>  
    <li>session = <span th:text="${session.sessionData}"></span></li>  
    <li>spring bean = <span th:text="${@helloBean.hello('Spring!')}"></span></li>  
</ul>
```

### URL 링크
@{...} 문법 사용

### 리터럴
= 소스 코드의 고정된 값
A-Z , a-z , 0-9 , [] , . , - , _  의 연속은 리터럴로 생각, 공백 X
이는 "| ~ |" 로 리터럴 대체 가능

### 연산
- 비교연산: HTML 엔티티를 사용해야 하는 부분을 주의하자
	- (gt), < (lt), >= (ge), <= (le), ! (not), == (eq), != (neq, ne) 
- 조건식: 자바의 조건식과 유사하다. 
- Elvis 연산자: 조건식의 편의 버전 
- No-Operation: _ 인 경우 마치 타임리프가 실행되지 않는 것 처럼 동작한다. 이것을 잘 사용하면 HTML의 내 용 그대로 활용할 수 있다.

### 속성 값 설정

- th:속성="값" -> 속성 대체
- th:attrappend="class=' large'" -> 속성 추가

### 반복
```
<tr th:each="user : ${users}">
```
두번째 파라미터 설정 (이름 s빼면 생략가능) 시 반복 상태 확인
반복 상태 유지 기능 
- index : 0부터 시작하는 값 
- count : 1부터 시작하는 값
- size : 전체 사이즈 
- even , odd : 홀수, 짝수 여부( boolean ) 
- first , last :처음, 마지막 여부( boolean ) 
- current : 현재 객체

### 조건부 평가
- if, unless 
	- 타임리프는 해당 조건이 맞지 않으면 태그 자체를 렌더링하지 않는다. 
	- 만약 다음 조건이 false 인 경우 ... 부분 자체가 렌더링 되지 않고 사라진다.

### 주석
```
# 1. 표준 HTML 주석
<!--
<span th:text="${data}">html data</span>
-->

# 2. 타임리프 파서 주석
<!--/* [[${data}]] */-->

# 3. 타임리프 프로토타입 주석
<!--/*/
<span th:text="${data}">html data</span>
/*/-->
```

### 블록
= 타임리프의 유일한 태그
```
<th:block th:each="user : ${users}">
...
</th:block>
```

### 자바스크립트 인라인
```
<script th:inline="javascript">
```
- 그냥 JS 를 네추럴 템플릿으로 쉽게 사용하게 도와줌
- " 처리, 이스케이프 문자 삽입 등
- 인라인 each 지원
```
<!-- 자바스크립트 인라인 each -->
<script th:inline="javascript">
 [# th:each="user, stat : ${users}"]
 var user[[${stat.count}]] = [[${user}]];
 [/]
</script>
```

### 템플릿 조각
```
# 부분 포함

## 부분 포함 insert
<div th:insert="~{template/fragment/footer :: copy}"></div>
## 부분 포함 replace
<div th:replace="~{template/fragment/footer :: copy}"></div>
## 부분 포함 단순 표현식
<div th:replace="template/fragment/footer :: copy"></div>
# 파라미터 사용
<div th:replace="~{template/fragment/footer :: copyParam ('데이터1', '데이터2')}"></
div>
```


