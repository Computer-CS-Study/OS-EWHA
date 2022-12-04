## CPU Scheduling

지금껏 레디큐 줄이 1개였음

Multilevel queue

- 줄마다 우선순위가 필요
    - system → interactive → interactive-editing → batch

1. 프로세스를 어느 줄에 넣어야 하는가
2. 우선순위가 낮은 줄을 starvation을 겪여야 하는

- Ready Queue를 분할
    - foreground
        - interactive한 job
    - background
        - batch
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
    - foreground - RR
    - background - FCFS
- 큐에 대한 스케줄링이 필요
    - 우선순위 고정 스케줄링
        - starvation 가능성이 있음
    - Time slice
        - 각 큐에 cpu time을 적절한 비율로 조정 (예를들어 fore는 80, back20)

Multilevel feedback queue

- 줄을 왔다갔다 할 수 있는 큐
- 프로세스가 다른 큐로 이동 가능
- 각 큐는 어떤 스케줄링으로 처리할 것 인가
- 상위큐로, 하위큐로 보내는 기준을 어떻게 할 것 인가?

→ cpu사용시간이 짧은 프로세스가 우선순위를 더 받게됨. 긴 프로세스는 밑으로 쫓겨나는 방식

---

Multiple-processor scheduling

화두만 던지고 자세히 다루지 않음

Real-time scheduling

- 데드라인이 있는 잡, 데드라인을 보장해줘야 함
- 데드라인을 보장하도록 미리 스케줄링함
- Hard real-time
    - 반드시 보장되어야 하는
- Soft real-time
    - 일반 프로세스에 비해 높은 우선순위를 가짐

Thread scheduling

- Local scheduling(유저레벨)
    - 어떤 스레드에게 cpu를 줄지 프로세스 내부에서 결정함, os가 하지 않음(os는 스레드의 존재를 모르니)
- Global scheduling(운영체제가 스레드 존재를 앎)
    - 운영체제가 스레드의 존재를 알기에 프로세스를 스케줄링하듯 어떤 thread에게 스케줄할지 결정함

---

## Algorithm Evaluation

1. Queueing models
    1. 예전에 많이 씀. 요즘에는 실제 사용된 값으로 측정함
2. Implementation & Measurement
    1. 실제 시스템에 알고리즘을 구현하여 실제 작업 체크
3. Simulation
    1. 실제로 돌리는게 아니라 모의 실험, input을 trace라 함

---

## Process Synchronization

데이터를 읽기만 하면 문제가 될 게 없겠지.

컴퓨터 시스템안에서 데이터에 접근하는 패턴에 대한 설명

1. 저장소
2. 연산할 데이터를 가져옴
3. 연산
4. 연산한 결과를 저장소에 넣음

→ 그러나 위와 같은 접근을 여러 execution에서 사용한다면 문제가 생김

저장소가 꼭 disk가 아니라 멀티프로세서일 경우에는 공유 메모리를 사용할 때 문제가 생김

커널 내부 데이터를 접근할 때도 문제가 생길 수 있음

운영체제 커널에 있는 데이터는 여러 프로세스가 공유로 사용하는 데이터이기에 문제가 생길 수 있음

race-condition이 언제 발생할까?

- interrupt handler vs kernel
    - 커널모드 실행중(register에 이전 값을 담아두고 있음)에 interrupt가 발생해서 interrupt가 처리될 때
        - 이 경우에는 interrupt를 disabled시켰다가 먼저 실행시키고 interrupt 처리하여 해결한다.
- 프로세스가 system call을 했을 경우에 커널의 코드가 실행하다가, cpu가 다른 프로세스에게 넘어갈 때 이 때 다른 프로세스에서 systemcall을 하고 이전과 같은 커널의 코드를 실행했을 때 다시 처음의 프로세스로 돌아오면 문제가 생김
    - 커널의 코드가 실행중일 때는 빼앗지는 않고 커널의 코드실행이 모두 끝났을 때 다른 프로세스에게 넘겨줌.
    - 조금 더 많은 시간을 할당 받음. time-sharing이기에 real-time이 아니기에 괜찮음

- cpu가 여러개 있는 환경
    - 작업주체가 여럿인 상황이라 이전의 문제처럼 해결 불가.
    - 공유 데이터에 접근할 때 데이터에 대해 lock함.

Process Synchronization문제

- 공유 데이터의 동시접근시 데이터의 불일치 문제 발생
- 일관성 유지위해 프로세스간 실행 순서를 정해주는 매커니즘 필요

Race condition

- 여러 프로세스들이 동시에 공유데이터에 접근하는 상황을 말함
- 이러한 상황을 막기위해서는 동기화 되어야 함!

The critical-section problem

- 공유 데이터에 접근하는 코드를 critical-section이라 함
- 하나의 critical-section에 있을 때 다른 모든 프로세스는 critical-section에 들어갈 수 없다.