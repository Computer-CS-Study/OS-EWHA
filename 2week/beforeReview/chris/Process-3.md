쓰레드

- 빠른응답성 & 자원 절약
- 다중스레드가 협력하여 높은 처리율(throughput)을 얻을 수 있다.
- 병렬성을 높일 수 있음

프로세스는 하나이기에 PCB도 1개

그러나 프로그램카운터와 레지스터는 스레드마다 생김

→ 이것들은 독자적으로 가져야하는 정보이기에

주소공간에서는 스택부분만 각 스레드마다 가짐

스레드의 장점

- 응답성
    - 사용자 입장에서 빠름
    - 하나의 쓰레드가 blocked되어도 다른 스레드는 실행중이기에
- 자원공유
    - 똑같은 일을 하는 프로그램이 여러개 있을 때 별도의 프로세스로 사용하는 것 보다는, 하나로 실행하고 code, data 및 각종 자원을 공유할 수 있음
- 경제성
    - 응답성과는 다른의미, 프로세스를 하나 만드는 건 오버헤드가 큼
    - 그러나 스레드를 하나 추가하는건 오버헤드가 작음
    - 문맥교환은 오버헤드가 큼
    - 동일한 주소공간을 사용하고 있어 대부분의 문맥을 사용 가능

⇒ cpu가 하나 있는 환경에서 가능

- 멀티 프로세서에서의 사용성

⇒ 멀티 프로세서 환경

스레드 구현 방법

- 운영체제 커널의 지원을 받는, 커널 쓰레드
    - 스레드가 여러개 있다는 사실을 운영체제(커널)이 알고 있음
    - 하나의 스레드에서 다른 스레드로 cpu가 넘어가는 걸 커널이 넘겨줌
- 라이브러리 형태, 유저 쓰레드
    - 운영체제는 모름, 유저 프로그램이 스스로 스레드를 관리
    - 구현상의 제약이 조금 있음