# CPU Scheduling

CPU and IO Bursts in Program Execution

![스크린샷 2022-11-20 오후 8.18.35.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/574f6b93-96cc-484d-bc50-9511c70963bd/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-11-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_8.18.35.png)

load store, add store, read from store → 인스트럭션이다.

io작업하는 동안 또 기다리고 등등..

프로그램의 패스는 cpu를 연속적으로 쓰는 단계와 io 실행하는 단계로 나뉜다 → 각각 CPU brust, IO burst이다.

![스크린샷 2022-11-20 오후 8.31.43.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d3c00edf-236e-4292-85b7-e15ca5959d44/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-11-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_8.31.43.png)

IO바운드 잡이 CPU를 많이 쓰는 것은 아님 → CPU를 잘게 짤라서 많이 써서 빈도가 많은 것일 뿐이다.

→ 여기서 IO바운드 잡이 문제다. interactive한 job이다. → cpu 바운드 잡 시에 이런 것들을 cpu가 잡고 놓지 않으면 io바운드 잡 시에 사람이 답답함을 느낀다. → 효율적으로 잘 분배해야한다 → 무조건 공평하게 분배하는 것이 답이 아니다.

프로세스는 그 특성에 따라 다음 두 가지로 나눔

- IO-bound process
    - CPU를 잡고 계산하는 시간보다 IO에 많은 시간이 필요한 job
    - (many short CPU bursts)
- CPU-bound-process
    - 계산 위주의 job
    - (few very long CPU bursts)
    

CPU Scheduler & Dispatcher

- CPU Scheduler → 운영체제 안에서 씨피유 스케쥴링을 하는 코드가 있다 독립된 무언가의 소프트웨어가 아니다.
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.
- Dispatcher → 이것도 운영체제 안에 있는 특정 기능
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다.
    - 이 과정을 context switch(문맥 교환)라고 한다.
- CPU 스케쥴링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.
    - Running → Blocked (예: IO 요청하는 시스템 콜)
    - Running → Ready (예: 할당시간 만료로 타이머 인터럽트)
    - Blocked → Ready (예: IO 완료 후 인터럽트)
    - Terminate
    - 1, 4에서의 스케줄링은 nonpreemptive ( → 강제로 빼앗지 않고 자진 반납)
    - 나머지는 전부다 preemptive( → 강제로 빼앗음)
