## cpu 스케줄링

cpu burst가 짧다는 건 i/o burst가 빈번하게 나타난다는 것

프로세스의 cpu burst가 길기도 하고 짧기도 하기 때문에 cpu 스케줄링이 필요함

cpu스케줄링의 이슈

1. 누구에게 cpu를 줄 것 인가.
2. cpu burst가 길다면 다른 프로세스에게 넘겨줘야 하는가? (계속 쓰게할 것인가?)

공평한 스케줄링 및 효율적인 스케줄링이 필요하다.

크게 2가지로 구분할 수 있는데,

(비선점형)non-preemptive 강제로 빼앗지 않는 방법

(선점형)preemptive 강제로 빼앗는 방법 ⇒ 현대의 대부분 스케줄링

스케줄링알고리즘이 어떤게 좋을지 평가할 방법 ⇒ 성능 척도(cpu 스케줄링을 위한 성능 척도) Scheduling criteria

시스템 입장에서의 성능 척도 vs 프로그램 입장에서의 성능 척도

전자는 보통 이용률과 처리량으로 얘기를 하고, 프로세스입장에서는 (cpu를 빨리 얻어서 빨리 일하면 좋은 것) 소요시간, 대기시간, 응답시간을 확인함.

1. Cpu utillization
    1. 전체 시간중에 cpu가 놀지 않고 일한 시간의 비율을 말함
2. Troughput
    1. 주어진 시간동안에 몇 개의 작업을 완료했는가?

    ---

3. Turnaround time
    1. 프로세스가 cpu를 사용하러 들어와서 다 쓰고 나갈 때 까지 걸린 시간을 의미
    2. cpu burst가 다 쓰고 나갈 때 까지의 시간
4. Waiting time
    1. 프로세스가 기다린 시간(순수하게 레디큐에서 기다린 시간을 의미)
5. Response time
    1. 레디큐에 들어와서 처음으로 cpu를 얻기까지 걸린 시간
    2. time-sharing에서 처음 cpu를 얻는다는 것이 사용자 응답과 중요한 개념이기에 필요.

선점형일 경우 얻었다 빼기는  과정을 반복, waiting time은 줄서서 기다린 모든 시간을 합친 것.

response time은 최오의 cpu를 얻기까지의 시간

turnaround time은 모든 시간 다합침

프로세스를 실행하는 총 시간이 프로세스 시작부터 종료까지가 아니라

프로세스가 cpu를 ㅅ사용하러 들어와서 cpu를 사용하고 i/o하러 나갈 때 까지의 시간을 의미

- FCFS(First-come-first-served)
    - 먼저온 순서대로 처리
    - 은행!, 화장실 줄서기!
    - Convoy effect: 짧은 프로세스가 긴 프로세스 뒤에

- SJF(Shortest-job-first)
    - cpu를 사용하고자 하는 시간이 가장 짧은 프로세스에게 먼저
    - average waiting time이 가장 짧아짐
    - 2가지 방식이 있음
        - nonpreemptive
            - 일단 cpu를 잡으면 더 짧은 cpu가 큐에 들어와도 유지
        - preemptive(SRTF, Shortest-remaining-time-first)라고도 불림
            - cpu를 줬다하더라도 더 짧은 프로세스가 들어오면 넘겨줌
    - 문제점
        - Starvation
            - cpu사용시간이 긴 프로세스는 실행이 안될 수 있다.
        - 프로세스가 cpu를 사용하러 들어왔을 때 얼마나 사용할 수 있을지 예측할 수 없다……
            - 언제 더 짧은게 들어와서 cpu를 못 쓰게 할지…
            - 과거 cpu사용한 흔적을 통해 예측할 수는 있음
                - exponential averaging
                    - 실제 cpu사용시간, cpu사용시간을 예측한 시간을 적당한 비율로 계산해서 처리

- Priority Scheduling(우선순위 스케줄링)
    - 우선순위가 제일 높은 프로세스에게 cpu를 주는 방식
    - nonpreemptive
        - 더 높은 우선순위가 들어와도 유지
    - preemptive
        - 더 높은 우선순위가 들어오면 넘겨줌
    - SJF도 사실 우선수위 스케줄링의 일종 → 우선순위를 cpu사용 시간으로 정의한 것
    - 이것 또한 Starvation 문제가 발생 (우선순위가 매우 낮으면 프로세스가 실행될 수 없음)
        - 아무리 우선순위가 낮아도 오래 기다릴 때마다 우선순위를 높여주자의 방식으로 처리 가능(Aging)

- Round robin(RR)
    - 현대적인 스케줄링 기법
    - 각 프로세스는 동일한 크기와 할당 시간을 가지고, 할당 시간이 지나면 빼앗기고(preemptive함..) 레디큐 맨 뒤에 줄 선다.
    - response time이 가장 빠름
    - 대기시간이 본인이 사용하려는 cpu의 시간과 비례함.
    - 할당시간:q가 커지면 FCFS와 같고, 작아진다면 context switch 오버헤드가 커짐