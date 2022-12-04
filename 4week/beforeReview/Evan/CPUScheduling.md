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

Scheduling Algorithm

NonPreemptive(비선점형)

Preemptive(선점형) 현대에서는 대부분 이걸로 쓰고 있음.

- Scheduling Criteria (Performance Index, Performance Measure 성능 척도)
    - 시스템 입장에서의 성능 척도가 있고
        - CPU utilization(이용률)
            - 씨피유는 비싼자원이기 때문에 얼마나 이용했느냐.
        - Throughput(처리량)
            - 얼마나 처리 했는지에 대한 내용
    - 프로그램 입장에서의 성능 척도가 있다.
        - 프로그램 입장에서는 얼마나 빨리 처리 되느냐가 중요한데 그 기준이 3가지다.
        - Turnaround time(소요시간, 반환시간)
            - 씨피유를 다 쓰고 나갈 때까지의 시간
        - Waiting time(대기 시간)
            - 씨피유를 순수하게 줄서서 기다릴 때까지의 시간
        - Response time(응답 시간)
            - 레디큐에 들어와서 씨피유를 처음 얻기까지 기다리는 시간
        - 대기 시간과 응답 시간의 차이는 한 번의 씨피유 버스트 동안에도 얻었다 뺐겼다를 반복하는데 웨이팅 타임은 이 동안의 기다린 시간을 다 합친거고 응답 시간은 최초의 기다린 시간을 이야기 한다. 소요시간과 반환 시간은 모든 기다린 시간과 씨피유를 쓴 시간을 다 합친 개념이다.

- FCFS (First Come First-Served)
    - 먼저 온 순서대로 처리하는 것이다.
    - 현실 세계에서는 되게 많이 사용하는 방식, 은행에서 번호표 뽑고 온 순서대로 처리하는 것과 같은 원리
    - 비선점형이다. CPU를 얻으면 일단 끝날 때까지는 CPU를 계속 사용
    - 이런 면에서 그렇게 효율적이는 않다. CPU를 오래 쓰는 프로그램이 도착해서 오랫동안 선점해버리면 짧게 쓰는 프로세스가 많을지라도 다 기다린다.
    
    ![스크린샷 2022-12-04 오후 4.29.51.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b8791861-00ae-4b21-9113-01dd634b6794/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.29.51.png)
    
    ![스크린샷 2022-12-04 오후 4.31.59.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/345d496e-22cf-46f9-8911-684fd9fe7a5a/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.31.59.png)
    
    - 콘보이 효과 → 긴 프로세스를 짧은 프로세스들이 과도하게 기다리는 효과

- SJF (Shortest-Job-First)
    - 각 프로세스의 다음번 CPU burst Time을 가지고 스케쥴링에 활용
    - CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케쥴
    - Two schemes
        - NonPreemptive
            - 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점 당하지 않음
        - Preemptive
            - 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
            - Shortest-Remaining-Time-First(SRTF)라고도 부름
    - SJF is Optimal
        - 주어진 프로세스들에 대해 다른 알고리즘보다 최소 평균 대기 시간을 보장한다.  → 선점형 타입이 더 짧아진다.

![스크린샷 2022-12-04 오후 4.43.17.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d576a4d4-e0ea-429b-adea-2a8d4bf4a962/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.43.17.png)

![스크린샷 2022-12-04 오후 4.44.39.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e77b6191-754c-44c2-adf1-24331f4bf25e/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.44.39.png)

- 아래의 대기시간이 조금 더 짧은 것을 볼 수 있음
    - 스케쥴링 시점의 차이가 있음 비선점형 같은 경우는 하나의 프로세스가 끝났을 때 선점형은 프로세스가 새로운 레디큐에 들어왔을 때
    - CPU입장에서 보면 이게 가장 좋을 것 같지만 2가지의 문제점이 있음
        - Starvation(기아 현상)의 문제가 있다. → CPU 사용이 긴 프로세스는 영원히 씨피유를 못 받을 수 있다.
        - CPU 사용시간을 미리 알 수 없다.
            - → 실제로 알 수 없기 때문에 그러면 안 쓰이냐? 그거까진 아니다.
            - 사실 알 수는 없지만 과거의 쓴 흔적을 이용해서 추정은 할 수 있다.
            - 추정 방법
                
                ![스크린샷 2022-12-04 오후 4.56.17.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5cccd1d9-c74e-4ee1-bb84-8a3e277023b7/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.56.17.png)
                

- Priority Scheduling (우선순위 스케쥴링)
    - 우선순위를 보통 정수 값으로 표현(보통은 제일 작은 정수)
    - SJF도 일종의 우선순위 스케줄링이다. → 시간에 따른 우선순위를 준다고 생각하면 됨
    - Preemptive
        - 더 높은 우선순위가 도착했을 때 뺏는 스케줄링
    - nonPreemptive
        - 더 높은 우선순위가 오더라도 뺏지 않음
    - Problem
        - Starving(기아 현상)
    - Solution
        - Aging: 시간에 따라서 우선순위를 올려준다.

- Round Robin (RR)
    - 가장 좋은 점은 응답시간이 빨라진다.
        - 누구든지 짧은 시간 내에 한 번쯤은 응답 받게 됨
    - 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐
        - 일반적으로 (10~100ms)
    - 할당 시간이 지나면 프로세스는 선점당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다.
    - n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻는다.
        - 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다.
    - Performance
        - q large → FIFO, FCFS랑 같음
        - q small → 라운드로빈의 철학에는 충실하지만 context switch 오버헤드가 커진다.
    
    ![스크린샷 2022-12-04 오후 5.47.44.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/06faa9bc-e346-464c-a270-11714ffb7b3c/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.47.44.png)
    
- Multilevel Queue
    - 이전에는 Ready Queue가 한 줄이었는데 이건 여러 줄이다.
    
    ![스크린샷 2022-12-04 오후 6.06.07.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1f8a5104-2a57-4a8f-af0c-bffead9b591f/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_6.06.07.png)
    
    - 줄마다 우선순위가 있다.
    - Ready Queue를 여러 개로 분할
        - foreground (interactive)
        - background (batch - no human interaction)
    - 각 큐는 독립적인 스케줄링 알고리즘을 가짐
        - foreground - RR
        - background - FCFS
    - 큐에 대한 스케줄링이 필요
        - Fixed priority scheduiling
            - 우선순위에 대한 고정된 CPU할당을 가진다면 Starvation의 가능성이 생김
        - Time slice
            - 각 큐에 적절한 CPU time을 적절한 비율로 할당
            - 80프로에 대한 RR과 20퍼센트는 FCFS로 해결

- Multilevel Feedback Queue
    - 출신이 낮아도 중간에 승격하거나 강등하거나 우선순위를 조정 받을 수 있다.
    - 프로세스가 다른 큐로 이동 가능
    - 에이징을 이용하여 이와 같은 방식으로 구현 가능
    - 보통 운영하는 방식은 처음 들어오는 Queue는 보통 가장 우선순위가 높은 Queue에다 넣는다. 라운드로빈 시간(quantum)을 보통 아래 레벨로 갈수록 길게 줌 → 마지막에는 FCFS가 된다던가 그러한 방식

Multiple-Processor Scheduling

- CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
- Homogeneousr processor인 경우
    - Queue에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
    - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐
- Load sharing
    - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 매커니즘 필요
    - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- Symmetric Multiprocessing (SMP)
    - 모든 프로세스가 동일한 자격을 갖고 있어서 각자 알아서 스케줄링함
    - 각 프로세서가 각자 알아서 스케줄링 결정
- Asymmetric multiprocessing
    - 하나의 CPU가 대장 노릇을 하며 분배를 담당함
    - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

Real-Time Scheduling

- Hard real-time systems
    - Hard real-time systems은 정해진 시간 내에 꼭 끝내야함
    - 리얼 타임은 데드라인에 맞춰서 끝내게 미리 계산해놓고 프로세스에 시간을 선점함
    - 리얼타임은 보통 periodic한게 많음
- Soft real-time computing
    - 일반 프로세스에 비해 높은 priority를 갖도록 해야 함

Thread Scheduling

- Local Scheduling
    - User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
    - OS가 하는 것이 아니고 사용자 프로세스가 직접 하는 것이다.
- Global Scheduling
    - Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정
    - 어차피 운영체제가 쓰레드의 존재를 알고 있기 때문에 프로세스 스케줄링 하듯이 알고리즘에 근거해서 어떤 스레드를 스케줄할지 결정

Algorithm Evaluation

![스크린샷 2022-12-04 오후 10.20.37.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/05458ffc-9572-447a-9073-9bf771e830cc/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.20.37.png)

- Queueing models
    - 확률 분포로 주어지는 arrival rate와 service rate등을 통해 각종 Performance index 값을 계산
- Implementation(구현) & Measurement(성능 측정)
    - 실제 시스템에 알고리즘을 구현하여 실제 작업(workload)에 대해서 성능을 측정 비교
- Simulation (모의 실험)
    - 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교
