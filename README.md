# spring_sse
spring5 sse(server sent event)를 학습하기 위한 프로젝트

### @EventListener 활용한 응용 프로그램 개발

> 스프링 프레임워크에서 발행-구독(sub-scribe) 패턴을 활용하기

[[Spring] ApplicationEventPublisher를 통한 스프링 이벤트 처리(ApplicationEventPublisher, Spring Event Processing)](https://engkimbs.tistory.com/718)

- 리액티브 디자인에 따라 애플리케이션 만들기
- 고전적인 방식의 폴링 모델(pulling model)을 사용하여 데이터 조회 x

일반적으로 SSE 는 브라우저에 메시지를 업데이트 하거나 연속적인 데이 스트립을 보내는 데 사용한다.

HTML5 의 시작과 함께 모든 최신 브라우저는 EventSource라는 자바스크립트 API 가 적용된다.

EventSource는 접속 문제가 발생할 경우 자동으로 재연결한다.

SSE가 리액티브 시스템의 구성 요소간의 통신 요구 사항을 충족시키는 최고의 후보이다.

### 스프링 웹 MVC를 이용한 비동기 HTTP 통신

서블릿 3.0 에서 추가된 비동기 지원 기능은 HTTP 요청을 처리하는 기능을 확장했다.

Callable<T>는 컨테이너 스레드 외부에서도 실행될 수 있지만, 여전히 블로킹 함수입니다.

DeferredResult<T>는 setResult(T result) 메서드를 호출해 컨테이너 스레드 외부에서도 비동기 응답을 생성하므로 이벤트 루프 안에서도 사용할수 있습니다.

스프링 웹 MVC는 버전 4.2부터 DefferedResult와 비슷하게 동작하는 ResponseBodyEmitter 를 반환할 수 있다. ResponseBodyEmitter 는 메시지 컨버터에 의해 개별적으로 만들어진 여러 개의 오브젝트를 전달하는 용도로 사용할 수 있습니다.

SseEmitter는 ResponseBodyEmitter 를 상속했으며, SSE의 프로토콜 요구 사항에 따라 하나의 수신 요청에 대해 다수의 발신 메시지를 보낼수 있습니다.

이를 사용하면 @controller에서 반환될 때 데이터(페이로드)를 비동기적으로 보낼수 있습니다.

> StreamingResponseBody는 서블릿 스레드를 차단하지 않으면서 큰 파일을 스트리밍해야 하는 경우에 매우 유용합니다.

### 단점

스프링에서 제공하는 발행-구독(pub-sub)구조를 사용하고 있습니다. 스프링 프레임워크에서 이 메커니즘은 응용 프로그램 수명주기 이벤트를 처기하기 위해 도입됐으며, 고부하 및 고성능 시나리오를 위한것은 아닙니다.

프레임워크의 내부 메커니즘을 사용하는 것은 프레임워크의 사소한 변경으로 인해 응용 프로그램의 안정성을 위협할수 있습니다.

 ApplicationContext를 로드하지 않고 비즈니스 로직을 단위 테스트하는 것이 어렵습니다. SseEmitter를 사용하면 스트림의 졸료와 오류 처리에 대해 구현을 추가할 수 있지만, @EventListener는 그렇지 않습니다. 스트림 종료 또는 오류 방생을 알리기 위해 별도의 오브젝트 또는 클래스 상속 구조를 정의 해야 하는 것도 잊기 쉽습니다.

또한 비동기 적으로 브로드캐스팅하기 위해 스레드 풀을 사용하는 것은 진정한 비동기적 리액티브 접근에서는 필요 없는 일입니다. 클라이언트의 수가 없더라도 이벤트가 발생하는 일이 발생합니다.
