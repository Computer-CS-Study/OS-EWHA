# Process Synchronization

데이터의 접근

![스크린샷 2022-12-04 오후 10.44.35.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ffa54107-6886-4605-a493-a7fc359d36be/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.44.35.png)

![스크린샷 2022-12-04 오후 10.46.23.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/443c5057-647a-4fe2-adae-0df2e1f3c71a/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.46.23.png)

Race Condition (경쟁 상태)

- 언제 접근한 데이터를 쓰고 읽냐에 따라서 문제가 바뀐다.

OS에서 Race Condition은 언제 발생하는가?

1. Kernel 수행 중 인터럽트 발생 시
    
    ![스크린샷 2022-12-04 오후 11.02.13.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/80f056ae-0be9-45e3-8f6d-4eee38bd1b22/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.02.13.png)
    
    빨간 점 사이에 인터럽트가 발생하지 않도록 막는 방법으로 문제 해결
    
2. Process가 System call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
    
    ![스크린샷 2022-12-04 오후 11.09.57.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bb958344-ce7d-4654-a2d3-69a8e8107187/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-04_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.09.57.png)
    
    ![스크린샷 2022-12-05 오전 12.21.40.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/139a4824-82cf-466b-a7ff-773b1de3cb19/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-05_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_12.21.40.png)
    
    커널 모드에서 수행중일 때는 CPU를 preempt하지 않음
    
    커널모드에서 사용자 모드로 돌아갈 때 preempt
    
3. Multiprocessor에서 shared memory 내의 kernel data
    
    ![스크린샷 2022-12-05 오전 12.23.09.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f2166a14-3885-4b4e-a7a3-bdb299b6f984/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-05_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_12.23.09.png)
    
    방법 1) 커널 전체를 하나의 락으로 제어한다. → 비효율적이다.
    
    방법 2) 해당 데이터를 접근하는게 아니라면 lock을 걸지 않는다 → CPU를 하나로 락에 걸어서 제어하는 것보다는 효율적임.
    

Process Synchronization 문제

- 공유 데이터의 동시 접근은 데이터 불일치 문제를 발생시킬 수 있다.
- 일관성 유지를 위해서는 협력 프로세스 간의 실행 순서를 정해주는 매커니즘 필요
- Race condition
    - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
    - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
    

Critical-Section(임계 구역) Problem

![스크린샷 2022-12-05 오전 12.30.43.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3eeab8c8-8078-493b-926a-c9925299659c/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-05_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_12.30.43.png)

![스크린샷 2022-12-11 오후 3.44.21.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/07c0b7fb-68ed-4563-8ea1-47d895f02831/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.44.21.png)

이런식으로 임계구역 들어가기 전 락을 걸고 나온 후에 풀어서 공유 데이터의 문제를 해결할 수 있다.

- 프로그램적 해결법의 충족 조건
    - Mutual Exclusion(상호 배제)
        - 프로세스 A가 임계구역 부분을 수행하고 있으면 다른 모든 프로세스들은 그들의 임계구역에 들어가면 안된다.
    - Progress
        - 아무도 임계구역에 있지 않은 상태에서 임계구역에 들어가고자 하는 프로세스가 있으면 임계구역에 들어가게 해주어야 한다.
        - 당연한 얘기 같아보이지만 상호배제를 충족하려다 보면 코드를 잘못 짜서 아무도 없는데도 안 들어가기도 한다.
    - Bounded Waiting (유한 대기)
        - 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.
        - 특정 프로세스 입장에서 임계구역에 못 들어가고 Starvation이 생기지 않게 해줘야 함
        - 예를 들면 3개의 프로세스가 있는데 2개의 프로세스만 계속 번갈아가면서 들어가는 경우

- Alogorithm 1

![스크린샷 2022-12-11 오후 3.53.22.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/241d5d51-d728-46d5-a696-913fda2f9081/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.53.22.png)

→ 상호배제는 만족함

→ Progress 조건은 만족하지 못함 → 다른 프로세스에 의해서 자신의 턴이 영향 받음

- Algorithm2

![스크린샷 2022-12-11 오후 3.56.04.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ee9933ab-f79d-4f12-b887-ab714a77ceb0/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.56.04.png)

→ 기준을 나로 삼음 1에서는 상대방으로 기준을 삼았음

→ 둘 다 동시에 일어났을 때 서로 계속 기다리는 현상이 발생함

- Algorithm 3 (Peterson’s Algorithm)

![스크린샷 2022-12-11 오후 4.03.28.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f62e7175-9424-41a4-9d48-771e73217d3d/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.03.28.png)

→ Busy Waiting (=spin lock)

Test & Set을 Atomic할 수 있으면 코드가 간단해질 수 있음

→ 고급언어에서는 대부분 인스트럭션을 하나만 수행할 수 있기 때문에 위의 코드처럼 길어짐

![스크린샷 2022-12-11 오후 4.14.10.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a93e3ebe-f1f1-4db3-97d5-dc0ae836f02b/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.14.10.png)

Semarphores

- 추상 자료형
    - Object와 Operation으로 구성되는 자료형
    - 논리적으로 구현하는거지 컴퓨터에서 어떻게 실제로 구현되고 있는지랑은 별개임
- 세마포어
    - 정수값(자원의 값이라고 생각하면 됨, 5면 자원이 5개임 → P연산을 다섯번 해서 동시에 5개의 자원을 가져갈 수 있음)을 가질 수 있음
    - 2가지 atomic 연산을에 의해서만 접근 가능하다
        - P연산
            - 세마포어 변수 값(공유 데이터)을 획득하는 과정이다.
            - 여기서도 busy-wait 문제는 생긴다
        - V연산
            - 다 사용하고 나서 반납하는 과정이다.
    - 세마포어를 왜 정의하는 것이냐?
        - 공유자원을 획득하고 반납하는 것을 세마포어가 처리해줌.

![스크린샷 2022-12-11 오후 9.44.55.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ba562780-f65d-41c0-99ce-f2c9feadba07/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.44.55.png)

- 프로그래머는 세마포어가 지원된다면 P연산과 V연산만 하면 된다.
- 세마포어의 구현 방식이 busy-wait 방식 말고도 Block & Wakeup 방식도 있다. → sleep lock이라고도 부름

Block & Wakeup

![스크린샷 2022-12-11 오후 9.50.41.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/24abdc21-0699-4012-832b-1a1b3a86bdd9/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.50.41.png)

![스크린샷 2022-12-11 오후 9.51.08.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c342fefb-3d35-46e8-80a3-0c71d765de1c/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.51.08.png)

![스크린샷 2022-12-11 오후 9.51.56.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/75070fcc-5f03-42c9-acb5-c54f61f14d6a/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.51.56.png)

- Busy-wait vs. Block & wakeyp
    - 보통은 블락 앤 웨이크업을 사용함
        - 비지 웨잇을 쓰면 씨피유 입장에서는 의미 있는 이용률이 떨어짐 → throughput이 떨어질 것임
    - 블락 앤 웨이크업을 할 때는 오버헤드가 생기기 때문에 크리티컬 섹션의 길이가 길지 않을 때는 오히려 블락 앤 웨이크 업의 오버헤드가 비지웨잇의 대기 시간보다 비효율적일 수 있음
        - 크리티컬 섹션의 길이가 긴 경우 블락 앤 웨이크업이 적당

Two Types of Semaphores

- Counting semaphore
    - 도메인이 0 이상인 임의의 정수값
    - 주로 resource counting에 사용
- Binary semaphore (=mutex)
    - 0 또는 1값만 가질 수 있는 semaphore
    - 주로 mutual exclusion(상호배제) lock, unlock에 사용

Deadlock and Starvation

- Deadlock
    - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
    - 해결법은 자원을 획득하는 순서를 같게 해준다.
- Starvation
    - indefinite blocking
        - 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 상황
        - 특정 프로세스들만 자원을 공유하면서 다른 프로세스들은 자원을 얻지 못하는 문제를 얘기한다.

Dining-Philosophers Problem

![스크린샷 2022-12-11 오후 10.07.11.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d5b7856f-ecba-43fc-af90-98f4df8d66d0/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.07.11.png)

- 다 왼쪽 젓가락을 잡으면 → 데드락
- 양쪽 사람들이 번갈아서 잡으면 → 기아현상

Bounded-Buffer Problem (Producer-Consumer Problem)

![스크린샷 2022-12-18 오후 7.50.57.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f43691da-4440-4563-a9f6-075662105f83/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-18_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7.50.57.png)

![스크린샷 2022-12-18 오후 8.48.03.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b1520dbd-446b-4dfc-89b1-c8c5b1dddee4/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-18_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_8.48.03.png)

Readers-Writers Problem

- 읽는 프로세스가 있고 쓰는 프로세스가 따로 있다.
- 공유 데이터는 DB로 가정해보자.
- Reader는 여럿이 동시에 접근해도 상관 없다. 하지만 Writer는 여럿이 접근하면 안된다.

![스크린샷 2022-12-18 오후 9.00.10.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b74fb422-e604-49ab-aeed-3bf9915c3ba4/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-18_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.00.10.png)

- 공유 데이터 → DB, readCount
- synchrnoization variables → mutex 바이너리 세마포어, db 바이너리 세마포어

Dining-Philosophers Problem

![스크린샷 2022-12-18 오후 10.36.53.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3842d2da-80e5-4a5b-8f2e-bdf04479d843/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-18_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.36.53.png)

- 데드락 가능성이 있음 → 모든 철학자가 동시에 왼쪽 젓가락을 집었을 때
- 해결 방안
    - 4명의 철학자만 테이블에 동시에 앉을 수 있도록 한다.
    - 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 한다.
    - 비대칭
        - 짝수 철학자는 왼쪽, 홀수 철학자는 오른쪽을 먼저 잡게 한다. → 우선순위를 부여

![스크린샷 2022-12-18 오후 10.59.48.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a0f3d3ee-5f5c-4549-b3cb-794a0cda1342/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-12-18_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_10.59.48.png)

Monitor

- Semaphore의 문제점
    - 코딩하기 힘들다
    - 정확성의 입증이 어렵다
    - 자발적 협력이 필요하다
    - 한 번의 실수가 모든 시스템에 치명적 영향이 있다.
- 동시 수행중인 프로세스 사이에서 추상 데이터 타입의 안전한 공유를 보장하기 위한 high-level synchronization construct
- 공유 데이터를 객체 지향적 관점에서 클래스로 캡슐화 하고 인터페이스를 제공하여서 실수할 여지를 줄여주는 것.
- monitor는 lock을 걸 필요가 없음 → 기본적으로 모니터에 대한 동시 접근을 허용하지 않기 때문에 프로그래머가 락을 걸 필요가 애초에 없음
