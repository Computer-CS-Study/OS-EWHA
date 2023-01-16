Demand Paging

- 실제로 필요할 때 page를 메모리에 올리는 것
    - IO 양의 감소
        - 대부분의 좋은 소프트웨어 프로그램은 방어로직이 많은데 이 로직은 자주 호출되지 않는다. → 이런걸 메모리에 항상 다 올려놓을 필요가 없음
    - Memory 사용량 감소
    - 빠른 응답 시간
    - 더 많은 사용자 수용
- Valid / Invalid bit의 사용
    
    ![스크린샷 2023-01-15 오후 6.35.43.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/06ace61e-0ebe-4af5-b013-cba13a3dcfff/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-15_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_6.35.43.png)
    
    - Invalid의 의미
        - 사용되지 않는 주소 영역인 경우
        - 페이지가 물리적 메모리에 없는 경우
            - 백킹 스토어에 내려가 있을 때
    - 처음에는 모든 page entry가 invalid로 초기화
    - address translation 시에 invalid bit이 set되어 있으면 “page falut”
        - 주소 변환하려고 봤는데 백킹스토어에서 메모리에 올려야 하면 이건 사용자 프로세스가 할 수 없는 것임 → software interrupt(trap)가 일어남 → OS한테 요청되는 현상
    

Page Falut

- invalid page를 접근하면 MMU가 trap을 발생시킴 (page falut trap)
- Kernel mode로 들어가서 page fault handler가 Invoke됨
- 다음과 같은 순서로 page fault를 처리한다
    1. Invalid reference? (eg. bad address, protection violation) → abort process
    2. Get an empty page frame (없으면 뺏어온다: replace)
    3. 해당 페이지를 disk에서 memory로 읽어온다
        1. disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt 당함 (block)
        2. disk read가 끝나면 page tables entry 기록, valid/invalid bit = “valid”
        3. ready queue에 process를 insert → dispatch later
    4. 이 프로세스가 CPU를 잡고 다시 running
    5. 아까 중단되었던 instruction을 재개

Performance of Demand Paging

- Page Fault Rate 0 ≤ p ≤ 1.0
    - 대부분의 경우 페이지 폴트가 나지 않음
    - 보통 0.0x 정도 됨
- Effective Access Time
    - (1-p) * memory access + p (OS & HW page fault overhead + [swap page out if needed] + swap page in + OS & HW restart overhead)

Free frame이 없는 경우

- Page replacement
    - OS가 하는 업무
    - 어떤 frame을 빼앗아올지 결정해야 함
    - 곧바로 사용되지 않을 page를 쫓아내는 것이 좋음
    - 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음
- Replacement Algorithm
    - page에 새로운 값이 올라오면 쫓아낸 페이지 프레임의 invalid bit를 invalid로 만들어준다
    - 메모리에서 쫓아낼 때 값이 새로 쓰여있다면 백킹스토어에 값 write까지 해야하고 아니라면 메모리에서 해제하기만 하면 됨
    - page-fault rate를 최소화하는 것이 목표
    - 알고리즘의 평가
        - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사
    - reference string의 예
        - 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5

Optimal Algorithm

- MIN(OPT): 가장 먼 미래에 참조되는 page를 replace
    - 실제 프로그램에서 사용할 수 없다..
    
    ![스크린샷 2023-01-15 오후 9.15.28.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c36a5735-e12c-47d2-965c-326d211beeda/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-15_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.15.28.png)
    
- 미래의 참조를 어떻게 아는가?
    - Offline algorithm이라고도 함 → 결국 쓰이지 않기 때문에..
- 다른 알고리즘의 성능에 대한 upper bound 제공
    - Belady’s optimal algorithm, MIN, OPT 등으로 불림

FIFO Algorithm

- 먼저 들어온 것을 먼저 내쫓음
- FIFO Anomaly (Belady’s Anomaly)
    - 프레임을 늘려주면 뭔가 성능이 좋아질 것 같지만 성능이 더 나빠지는 현상

![스크린샷 2023-01-15 오후 9.25.43.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7be06be1-46e6-4bac-b702-ead0c3432420/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-15_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.25.43.png)

LRU (Least Recently Used) Algorithm

- 가장 오래 전에 참조된 것을 지움

![스크린샷 2023-01-15 오후 9.27.53.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e5dc8d49-7287-4e06-86b2-3c6df6f63d61/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-15_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.27.53.png)

LFU (Least Frequently Used) Algorithm

- 참조 횟수가 가장 적은 페이지를 지움
- 최저 참조 횟수인 page가 여럿 있는 경우
    - LFU 알고리즘 자체에서는 여러 page 중 임의로 선정한다
    - 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 구현할 수도 있다.
- 장단점
    - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음
    - 참조 시점의 최근성을 반영하지 못함
    - LRU보다 구현이 복잡함

LRU와 LFU 알고리즘의 구현

![스크린샷 2023-01-15 오후 9.48.35.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/47938703-5340-4ec7-982e-9db9b70ff5a0/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-15_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.48.35.png)

- LFU는 한 번 더 참조됐다고 해서 제일 아랫쪽에 위치 시킬 수 없음
- LFU는 일반적인 링크드 리스트보다는 힙을 사용하여 완전이진트리로 바꿔서 logN의 성능으로 나오게 변경해줌

다양한 캐싱 환경

- 캐싱 기법
    - 한정된 빠른 공간(=캐쉬)에 요청된 데이터를 저장해 두었다가 후속 요청 시 캐시로부터 직접 서비스하는 방식
    - paging system 외에도 cache memory, buffer caching, Web caching등 다양한 분야에서 사용
- 캐쉬 운영의 시간 제약
    - 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음
    - Buffer caching이나 Web caching의 경우
        - O(1)에서 O(logn)정도까지 허용
    - Paging system인 경우
        - page fault인 경우에만 OS가 관여함
        - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
        - O(1)인 LRU의 list 조작조차 불가능

Paging System에서 LRU, LFU 가능한가?

- 결론적으로 불가능하다
- 해당 page가 메모리에 올라가있으면 운영체제를 거치지 않기 때문에 불가능하다.

Clock Algorithm

- LRU의 근사(approximation) 알고리즘
- 여러 명칭으로 불림
    - Second chance algorithm
    - NUR (Not Used Recently) 또는 NRU (Not Recently Used)
- Reference bit을 사용해서 교체 대상 페이지 선정 (circular list)
- reference bit가 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
- 포인터 이동하는 중에 reference bit 1은 모두 0으로 바꿈
- Reference bit이 0인 것을 찾으면 그 페이지를 교체
- 한 바퀴 되돌아와서도 (=second chance) 0이면 그때에는 replace 당함
- 자주 사용되는 페이지라면 second chance가 올 때 1
- Clock algorithm의 개선
    - reference bit과 modified bit (dirty bit)을 함께 사용
    - reference bit = 1 : 최근에 참조된 페이지
    - modified bit = 1 : 최근에 변경된 페이지 (I/O를 동반하는 페이지)

Page Frame의 Allocation

- Allocation problem: 각 process에 얼마만큼의 page frame을 할당할 것인가?
- Allocation의 필요성
    - 메모리 참조 명령어 수행 시 명령어, 데이터 등 여러 페이지 동시 참조
        - 명령어 수행을 위해 최소한 할당되어야 하는 frame의 수가 있음
    - Loop를 구성하는 page들은 한꺼번에 allocate 되는 것이 유리함
        - 최소한의 allocation이 없으면 매 loop마다 page fault
- Allocation Scheme
    - Equal allocation: 모든 프로세스에 똑같은 갯수 할당
    - Proportinal allocation: 프로세스 크기에 비례하여 할당
    - Priority allocation: 프로세스의 prirority에 따라 다르게 할당

Global vs Local Replacement

- Global replacement
    - Replace시 다른 process에 할당된 frame을 빼앗아 올 수 있다.
    - Process별 할당량을 조절하는 또 다른 방법임
    - FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용시에 해당
    - Working set, PFF 알고리즘 사용
- Local replacement
    - 자신에게 할당된 frame내에서만 replacement
    - FIFO, LRU, LFU 등의 알고리즘을 process별로 운영시

Thrashing

![스크린샷 2023-01-16 오후 7.49.12.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4a85f10a-640c-442c-baa0-3a46834abf12/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-01-16_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7.49.12.png)

- 프로세스의 원활한 수행에 필요한 최소한의 page frame의 수를 할당 받지 못한 경우 발생
- Page fault rate가 매우 높아짐
- CPU utilization이 낮아짐
- OS는 MPD(Multiprogramming degree)를 높여야 한다고 판단
- 또 다른 프로세스가 시스템에 추가됨 (higher MPD)
- 프로세스 당 할당된 frame의 수가 더욱 감소
- 프로세스는 page의 swap in / swap out으로 매우 바쁨
- 대부분 시간에 CPU는 한가함
- low throughput

Working-set Model

- Locality of reference
    - 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다
    - 집중적으로 참조되는 해당 page들의 집합을 locality set이라고 함
- Working-set Model
    - Locality에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page들의 집합을 Working Set이라 정의함
    - Working Set 모델에서는 process의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 frame을 반납한 후 swap out(suspend)
    - Thrashing을 방지함
    - Multiprogramming degree를 결정함
        - 워킹셋이 페이지 프레임보다 작으면 서스펜드
        - 워킹셋이 페이즈 프레임보다 크면 MPD를 키움
- Working set의 결정
    - Working set window를 통해 알아냄
    - windos size가 X인 경우 해당 시간 동안 몇 사이즈가 쓰였는지를 봄

PFF (Page-Fault Frequency) Scheme

- page-fault rate의 상한값과 하한값을 둔다
    - Page fault rate이 상한값을 넘으면 frame을 더 할당한다
    - Page fault rate이 하한값 이하이면 할당 frame 수를 줄인다
- 빈 frame이 없으면 일부 프로세스를 swap out

Page Size의 결정

- Page size를 감소시키면
    - 페이지 수 증가
    - 페이지 테이블 크기 증가
    - Internal fragmentation 감소
    - Disk transfer의 효율성 감소
        - Seek/rotation vs transfer
    - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
        - Locality의 활용 측면에서는 좋지 않음
- Trend
    - Lager page size → 최근에는 4kb보다 더 큰걸 쓰려고 생각중임 → 64bit 컴퓨터가 나와서
