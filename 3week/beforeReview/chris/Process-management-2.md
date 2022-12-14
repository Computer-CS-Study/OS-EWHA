process management

복제 = 프로세스의 문맥을 모두 복사하는 것

- 코드,데이터,스택을 모두 복사
- 프로그램카운터 ,레지스터 모두 복사

자원 공유

원칙은 별개의 프로세스이기 때문에 독립적으로 가지고 있는 것이 원칙

사실 메모리가 낭비되는 것,

리눅스에서는 공유가능할 것들은 최대한 공유하려 함, 프로그램 카운터만 copy ⇒ copy-on-write기법

- write가 발생했을 때 그 때 copy함
- write = 원래 내용을 바꾸는 행위

### 프로세스와 관련한 시스템 콜!

fork() 시스템콜!

자식프로세스는 fork()함수 이후 부분을 실행하게 된다

- 부모의 프로세스 문맥을 그대로 복사하기 때문에(프로그램 카운터를 복사하니까)
- 당연히 처음부터가 아니라 부모의 문맥에서부터 실행

fork로 자식 프로세스를 만들면 자식과 부모를 구분한다.

fork의 return value는 부모는 결과값이 양수(id값) 반면 자식은 리턴밸류로 0을 얻음

exec() 시스템콜!

완전히 새로운 프로세스로 태어나게 해주는 역할

exec한 후 돌아올 수 없음

exec은 자식을 만들어서 exec해야하는 건 아님 ⇒ 바로 다른 프로세스로 덮어씌울 수 있다.

wait() 시스템콜!

프로세스를 block상태로 보낸다.

자식 프로세스를 만들고 종료될 때 까지 block상태(sleep시킨다)

이후 자식프로세스가 종료되면 깨운다(ready상태)

자식이 종료될 때 까지 기다리는 시스템콜!

exit() 시스템콜!

자발적 종료

- 컴파일러가 프로그램이 끝나면(메인 함수가 리턴되는 위치에) 알아서 exit을 호출하도록 처리
- exit()시스템 콜

비자발적 종료

- 부모프로세스가 자식프로세스 강제 종료
    - 한계치를 넘어선 자원 요청
    - 자식에게 할당된 태스크가 필요하지 않음
- 키보드로 kill, break하는 경우
- 부모가 종료되는 경우
    - 부모프로세스가 종료하기전에 자식들이 먼저 종료

### 프로세스 협력

원칙적으로 독립전 프로세스

하나의 프로세스가 다른 프로세스에게 영향을 미치지 않는다(부모 자식 관계 제외)

프로세스간 협력 매커니즘(IPC: interprocess communication)

- shared memory
    - 원칙적으로는 코드,데이터,스택을 각자 가지지만 일부 주소공간을 두 프로세스가 공유할 수 있다. → 이 방법이 shared memory
    - 물리적인 영역이 공통적으로 매핑되도록 처리
    - 커널에게 같이 사용하겠다는 요청을 해야함
    - 쓰레드는 프로세스 1개 안에 실행단위가 여러개 인 것
        - 즉 프로세스간 협력이라 볼 수 없다
        - 쓰레드끼리는 완전한 협력
- message passing
    - 프로세스가 다른 프로세스에게 메시지를 전달함
    - 프로세스가 직접 전달하는 방법은 아님
    - 커널을 통해 전달(메신저 역할)

    - 통신하려는 프로세스의 이름을 명시적 표시 유무에 따라 2가지로 나뉨
        - direct
            - 누가 보낸다, 누구에게 받는다가 명시적
        - indirect
            - 메시지를 보내는 측에서 누가 받아올지 명시하지 않는다. mail box에 메시지를 집어 넣는 방식
            - 여러개의 프로세스에게 보낼 수 있음

궁금한 점

- [ ]  트리구조라 했는데 그러면 모든 프로세스들이 서로 연결된 구조?
- [ ]  그러면 exec한 경우 여전히 부모프로세스와 트리구조상의 위계(?)를 계속 가지는지
- [ ]  그냥 프로세스를 실행할 수 없었나?
    - [ ]  유저에 의해서 실행될 수 있었던 것 같은데
    - [ ]  이 경우마저도 부모 프로세스가 자식 프로세스를 만들어 실행하는건가?

---

cpu 스케줄링

프로그램이 실행된다는 것은 ⇒ cpu burst → i/o burst가 반복된다라 볼 수 있다

중간에 io작업이 끼는 경우가 빈도가 높고, cpu만 길게 쓰는 경우는 적었다.

전자는 i/o bound job 후자를 cpu bound job이라 부른다.

이처럼 2가지 job(process)가 섞여있기 때문에 스케줄링 필요!

사람과 인터랙션이 많은 i/o bound job에게 더 많이 자주 할당해준다.

cpu 스케줄러

- ready상태의 어떤 프로세스에게 cpu를 줄지
- 운영체제 안에서 cpu스케줄링하는 코드가 있다.
    - 하드웨어나 cpu의 소프트웨어가 아니다.

dispatcher

- cpu를 프로세스에게 넘겨주는
- 커널의 코드(이것도 위와 같이 os의 코드임)
- 해당 과정을 context switch라 함
- 이전 context save, 다음 context를 가져와 레지스터 세팅 및 pc설정

스케줄링이 필요한 경우

- cpu가 어떤 프로세스에게 잡혀있다가 i/o요청할 경우 (running → blocked)
- 프로세스가 cpu를 계속 쓰고 싶지만 빼앗기는 경우, timer interrupt (running → ready)
- i/o 요청이 완료되었을 경우 (running → ready)
    - i/o를 요청한 프로세스의 우선순위가 제일 높을 경우 바로 cpu를 넘겨주는 경우도 있음
- 프로세스가 종료되었을 때

모든 스케줄링은 preemptive(강제로 빼앗음)

위의 예시중 1,4는 nonpreemptive(자진 반납)