---
{"publish":true,"title":"\b프록시 서버란?","created":"2025-07-22T01:22:48.623+09:00","modified":"2025-07-22T14:02:35.781+09:00","tags":["server","infra","proxy"],"cssclasses":""}
---

프록시 서버는 클라이언트에서 서버로 접속을 할 때 직접적으로 접속하지 않고, 중간에 대신 전달해주는 서버를 의미한다.  

![[files/image.png]]

## 프록시 서버가 필요한 이유

1. **보안**
	프록시 서버는 클라이언트와 서버 사이에서 중간 역할을 하여 직접적인 연결을 차단한다. 이를 통해 내부 네트워크의 실제 IP 주소를 숨기고, 악성 트래픽을 필터링하여 보안을 강화한다.

2. **익명성**
	사용자의 실제 IP 주소를 숨기고 프록시 서버의 IP로 대체하여 온라인 활동 시 익명성을 제공한다. 이는 개인정보 보호와 추적 방지에 도움이 된다.

3. **접근 제어 및 필터링**
	기업이나 기관에서 특정 웹사이트나 콘텐츠에 대한 접근을 제한하거나 허용할 수 있다. 또한 악성 사이트나 부적절한 콘텐츠를 차단하는 필터링 기능을 수행한다.

4. **캐싱**
	자주 요청되는 데이터를 프록시 서버에 저장하여, 동일한 요청이 들어올 때 원본 서버에 다시 요청하지 않고 캐시된 데이터를 제공한다. 이를 통해 응답 속도를 높이고 네트워크 대역폭을 절약한다.

5. **로드 밸런싱**
	여러 서버로 트래픽을 분산시켜 서버 부하를 줄이고 서비스의 안정성을 높인다. 특정 서버에 과부하가 걸리지 않도록 트래픽을 효율적으로 관리한다.

6. **모니터링 및 로깅**
	네트워크 트래픽을 모니터링하고 로그를 기록하여 네트워크 사용 패턴을 분석하고 문제점을 파악하는 데 도움을 준다.

7. **지역 제한 우회**
	특정 지역에서만 접근 가능한 서비스나 콘텐츠에 다른 지역의 프록시 서버를 통해 접근할 수 있다. 이는 지리적 제한을 우회하는 데 활용된다.


## 프록시 종류

### Forward Proxy
포워드 프록시는 보안을 위해 사용되며 클라이언트의 요청을 서버로 직접 보내는 것이 아닌 프록시 서버를 거치는 방식이다.

### Reverse Proxy
리버스 프록시는 포워드 프록시와 반대로 서버에서 클라이언트를 직접 데이터를 전달하지 않고 프록시 서버를 거치는 방식이다.