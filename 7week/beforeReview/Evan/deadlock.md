# Deadlock

교착상태(deadlock)

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

Resource(자원)

- 하드웨어, 소프트웨어 등을 포함하는 개념
- (예) I/O device, CPU cycle, memory space, semaphore emd
- 프로세스가 자원을 사용하는 절차
    - Request, Allocate, Use, Release

Deadlock Example 1

- 시스템에 2개의 tape drive가 있다.
- 프로세스 P1과 P2 각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.

Deadlock Example 2

- Binary semaphores A and B

![스크린샷 2022-12-25 오후 3.48.08.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5f9ead3b-2aab-487d-bc9f-d6bb9129b16d/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.48.08.png)

Deadlock 발생의 4가지 조건

- 4가지를 모두 만족해야함.
- Mutual exclusion (상호 배제)
    - 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
- No preemption (비선점)
    - 프로세스는 자원을 스스로 내어놓을 분 강제로 빼앗기지 않음
- Hold and wait (보유 대기)
    - 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음
- Circular wait (순환대기)
    - 자원을 기다리는 프로세스 간에 사이클이 형성 되어야 함

Resource-Allocation Graph (자원 할당 그래프)

- 화살표가 두 개 있는데
    - 자원에서 프로세스 쪽으로 나가는 화살표는 프로세스가 이 자원을 가지고 있다.
    - 프로세스에서 자원으로 나가는 화살표는 프로세스가 요청한 상황.

![스크린샷 2022-12-25 오후 3.57.06.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6611e47f-46f2-4521-a2f1-d9c4f7ef631c/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.57.06.png)

- 사이클이 없기 때문에 데드락이 아니다.

![스크린샷 2022-12-25 오후 3.59.56.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/423079b9-b57c-4098-8ec8-cf7024771494/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.59.56.png)

- 그래프에 사이클이 있으면 데드락일 수 있다. → 그 다음 살펴봐야 하는 조건을 살펴봐야함
- 자원에 인스턴스가 몇개인지 봐야한다.
- 한 개씩만 있으면 데드락이다.
- 여러개 있으면 따져봐야함 요청 수에 따라서 다름
- 왼쪽은 데드락임, 오른쪽은 자원에 사이클이 생기지 않아서 데드락이 아님

Deadlock의 처리 방법(아래로 갈 수록 데드락에 더 약한 방법)

- Deadlock Prevention
    - 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것
- Deadlock Avoidance
    - 자원 요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당
    - 시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당
- Deadlock Detection and recovery
    - Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견 시 recover
- Deadlock Ignorance
    - Deadlock을 시스템이 책임지지 않음
    - UNIX를 포함한 대부분의 OS가 채택

Deadlock Prevention

- Mutual Exclusion
    - 공유해서는 안되는 자원의 경우 반드시 성립해야 함
- Hold and Wait
    - 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
    - 방법 1.
        - 프로세스가 시작 시 모든 필요한 자원을 할당받게 하는 방법
    - 방법 2.
        - 자원이 필요한 경우 보유 자원을 모두 놓고 다시 요청
- No Preemption
    - 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
    - 모든 필요한 자원을 얻을 수 이 ㅆ을 때 그 프로세스는 다시 시작된다.
    - State를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용 (CPU, memory)
- Circular Wait
    - 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
    - 예를 들어 순서가 3인 자원 Ri를 보유 중인 프로세스가 순서가 1인 자원 Rj을 할당받기 위해서는 우선 Ri를 Release해야 한다.
- 이 방법들은 데드락을 원천적으로 막을 수 있지만 자원에 대한 이용률, 전체 시스템의 성능이 떨어지고 starvation 문제도 생길 수 있다.
    - 아직 생기지도 않은 데드락 때문에 시스템의 성능을 낮추는 것에 대한 우려가 있음

Deadlock Avoidance

- 자원 요청에 대한 부가정보를 이용해서 자원 할당이 deadlock으로 부터 안전한지를 동적으로 조사해서 안전한 경우에만 할당
- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법임

![스크린샷 2022-12-25 오후 4.28.02.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a368a4ab-a80c-4848-95b1-aa8fe77870c3/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.28.02.png)

- 점선 화살표는 프로세스로부터 자원한테만 가고 의미는 ‘이 프로세스가 평생에 한 번은 이 자원을 활용할 일이 있다.’ 이다.

![스크린샷 2022-12-25 오후 4.28.20.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/07b63fc1-f77c-42c8-b2fd-7100443f2935/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.28.20.png)

- Banker’s Algorithm
    - 아랫걸로 가정해봐도 데드락은 생기지 않지만 대단히 비효율적이다.

![스크린샷 2022-12-25 오후 4.58.44.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/49450ebf-c3c3-4b0c-bead-a93075445774/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.58.44.png)

- Safe state
    - 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태
- safe sequence
    - 프로세스의 sequence <P1, P2, … Pn>이 safe하려면 Pi(1 ≤ i ≤ n)의 자원 요청이 “가용 자원 + 모든 Pj(j<i)의 보유 자원”에 의해 충족되어야 함
    - 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
        - Pi의 자원 요청이 즉시 충족될 수 없으면  모든 Pj(j<i)가 종료될 때까지 기다린다
        - Pi-1이 종료되면 Pi의 자원요청을 만족시켜 수행한다.

Deadlock Detection and Recovery

- Deadlock Detection
    - Resource type 당 single instance인 경우
        - 자원할당 그래프에서의 cycle이 곧 데드락을 의미
    
    ![스크린샷 2022-12-25 오후 5.44.24.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bb36363f-0345-4327-854f-ceac44b44eb6/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.44.24.png)
    
    - Resource type 당 multiple instance인 경우
        - Banker’s algorithm과 유사한 방법 활용
        
        ![스크린샷 2022-12-25 오후 5.56.19.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6783966b-f961-456e-8e1a-29b734121761/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.56.19.png)
        
- Wait-for graph 알고리즘
- Resource type당 single instance인 경우
- Wait-for graph
    - 자원할당 그래프의 변형
    - 프로세스만으로 node 구성
    - Pj가 가지고 있는 자원을 Pk가 기다리는 경우 Pk → Pj
- Algorithm
    - Wait-for graph에 사이클이 존재하는지를 주기적으로 조사
    - O(n2)
- Recovery
    - Process termination
        - 데드락과 관련 있는 모든 프로세스를 다 죽이는 방법
        - 데드락에 연류된 프로세스를 하나씩 죽여보는 것
    - Resource Preemption
        - 비용을 최소화할 victim(희생양)의 선정
        - safe state로 rollback하여 process를 restart
        - Starvation문제
            - 동일한 프로세스가 계속 victim으로 선정되는 경우
            - cost factor에 rollback 횟수도 같이 고려

Deadlock Ignorance

- Deadlock이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음
    - 데드락이 매우 드물게 발생하므로 데드락에 대한 조치 자체가 더 큰 오버헤드일 수 있음
    - 만약 시스템에 데드락이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 프로세스를 죽이는 등의 방법으로 대처
    - UNIX, windows 등 대부분의 범용 OS가 채택
