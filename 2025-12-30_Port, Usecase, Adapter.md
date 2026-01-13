## Port
- core에서 외부와 통신하기 위한 존재
- 인터페이스로 구현
#### 종류
1. inbound port: 외부에서 inbound port를 구현하여 외부에서 의존
2. outbound port: 내부에서 service로 구현하여 Bean 의존성 주입으로 외부와 연결
## UseCase
- 사용자가 core에 요청한 시스템을 사용하는 동작의 시나리오
- inbound port의 한 종류(service로 구현됨)
## Adapter
- port를 통해 구현함
- 호출하여 core와 연결함
- layer가 core의 로직에 접근할수 있게 함
